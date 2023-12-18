# 개요

> 지난 개인과제 시간에는 추가 기능 요구사항으로 RefreshToken을 도입해보는 사항이 있었지만
> 나의 역량 부족으로 미쳐 하지 구현을 하지 못했었다.
> 이번 팀과제를 진행하면서 지난 시간에 구현하지 못한 RefreshToken을 구현을 해보면서
> 겪었던 문제들과 어떻게 구현을 해냈는지 기록하기 위해 작성을 한다.

## RefreshToken이 무엇인지??

- [RefreshToken 그게 뭔데?](https://github.com/junxtar/TIL/blob/main/Spring/refresh-token.md)

## RefreshToekn 보관 방법

나는 RefreshToken을 Redis 서버에 저장하는 방법을 가져갔다.

[Redis 사용 이유]

- Key - Value 방식, 인메모리 DB 방식으로 빠르게 접근 가능
- 브라우저에 비해 탈취 가능성이 낮다고 생각하는 redis 서버에 저장하는 방식
- Refresh Token은 영구적으로 저장되는 데이터가 아니다.

## Token의 생성 방법

아래 코드와 같이 AccessToken에는 Username을 저장하였고 RefreshToken은 AccessToken의 재발급용도로 별도의 유저에 대한 정보를 저장하지 않았다.

```java
 public String createAccessToken(String username) {
        Date date = new Date();

        return BEARER_PREFIX +
            Jwts.builder()
                .setSubject(username)
                .claim(AUTHORIZATION_KEY, username)
                .setExpiration(new Date(date.getTime() + ACCESS_TOKEN_TIME))
                .setIssuedAt(date)
                .signWith(key, signatureAlgorithm)
                .compact();
    }

public String createRefreshToken() {
        Date date = new Date();

        return BEARER_PREFIX +
            Jwts.builder()
                .setExpiration(new Date(date.getTime() + REFRESH_TOKEN_TIME))
                .setIssuedAt(date)
                .signWith(key, signatureAlgorithm)
                .compact();
    }
```

## RedisConfig

RedisConfig 클래스에 host와 port에 대한 값을 선언해주었으며, 데이터 삽입, 수정, 읽기를 위한 redisTemplate을 Bean으로 등록했다.

```java
@Configuration
public class RedisConfig {

    @Value("${spring.data.redis.host}")
    private String host;
    @Value("${spring.data.redis.port}")
    private int port;

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory(host, port);
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate() {
        RedisTemplate<String, Object> redisTemplate = new
            RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory());
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}

```

### RedisUtil

RedisTemplate를 활용해 코드로 구현한 RedisUtil이다.

```java
@Component
@RequiredArgsConstructor
public class RedisUtil {

    private final RedisTemplate<String, Object> redisTemplate;

    public void set(String key, Object o, int minutes) {
        redisTemplate.setValueSerializer(new Jackson2JsonRedisSerializer<>(o.getClass()));
        redisTemplate.opsForValue().set(key, o, minutes, TimeUnit.MINUTES);
    }

    public Object get(String key) {
        return redisTemplate.opsForValue().get(key);
    }

    public void delete(String key) {
        redisTemplate.delete(key);
    }

    public boolean hasKey(String key) {
        return Boolean.TRUE.equals(redisTemplate.hasKey(key));
    }
}

```

### 사용자의 로그인 성공

나는 사용자가 로그인을 성공했다면, AccessToken과 RefreshToken을 모두 응답 Header에 실어서 클라이언트에 반환하는 방식으로 구현했다.

또한 별도로 해당 RefreshToken을 서론에서 언급했듯이 Key값으로는 RefreshToken으로 지정하였고, Value값으로는 UserId로 지정했다.

나는 해당 여기에서 에러가 발생한 것을 인지 하지 못한채 코드가 완성된줄 알았다.

```java
@Override
    protected void successfulAuthentication(HttpServletRequest request,
        HttpServletResponse response, FilterChain chain, Authentication authResult) {

        User user = ((UserDetailsImpl) authResult.getPrincipal()).getUser();

        String accessToken = jwtUtil.createAccessToken(user.getUsername());
        String refreshToken = jwtUtil.createRefreshToken();

        response.addHeader(JwtUtil.ACCESS_TOKEN_HEADER, accessToken);
        response.addHeader(JwtUtil.REFRESH_TOKEN_HEADER, refreshToken);

        redisUtil.set(refreshToken, user.getId(), 60 * 24 * 14);

    }
```

### 문제점

해당 아래와 같이 Postman을 활용해서 로그인을 성공을 하였다.

응당 코드로는 200과 동시에 코드에 작성한대로 응답 Header에 AccessToken과 RefreshToken이 정상적으로 반환된 것을 확인할 수 있다.

![token](../img/redis1.png)

다음으로는 redis-cli을 통해 해당 RefreshToken의 저장 여부를 살펴보겠다.

![token](../img/redis2.png)

해당 값이 redis환경에 잘 들어갔다!

하지만 get을 통해서 해당 RefreshToken을 가져오려고 했지만 error문구가 나오는 것을 알 수 있었다.

```java
127.0.0.1:6379> get Bearer eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MDI4NzAxMDUsImlhdCI6MTcwMTY2MDUwNX0.lW2zJD9kFoVdftpLSejHQmtQJmxt7Bb_pGm3XaPTcDw
(error) ERR wrong number of arguments for 'get' command
```

redis cli에서는 공백이 포함된 문자열을 조회하려면 "" 을 추가해서 조회를 해줘야하는 문제가 있었다.

### 해결방법

코드 상에서 "" 를 추가해주는 방식보다는 Bearer 를 제외해주고 저장하는 방식을 택했다.

Bearer은 토큰 타입인데 해당 토큰 타입을 redis에 저장할때 필수라고는 생각하지 않기 때문이다.

refreshToken을 redis에 저장하기 전에 토큰 타입 Bearer 을 제외하고 저장을 해 공백 문제를 해결하였다.

```java
 @Override
    protected void successfulAuthentication(HttpServletRequest request,
        HttpServletResponse response, FilterChain chain, Authentication authResult) {

        User user = ((UserDetailsImpl) authResult.getPrincipal()).getUser();

        String accessToken = jwtUtil.createAccessToken(user.getUsername());
        String refreshToken = jwtUtil.createRefreshToken();

        response.addHeader(JwtUtil.ACCESS_TOKEN_HEADER, accessToken);
        response.addHeader(JwtUtil.REFRESH_TOKEN_HEADER, refreshToken);

        refreshToken = refreshToken.split(" ")[1].trim();  <<- 추가

        redisUtil.set(refreshToken, user.getId(), 60 * 24 * 14);
    }
```

해당 아래와 같이 제대로 저장된 것을 확인할 수 있었다.

<img width="564" alt="스크린샷 2023-12-04 13 19 32" src="https://github.com/junxtar/TIL/assets/75934088/5b44836e-792b-4340-ad6a-47dc239de44c">

## ++ 추가로 
해당 key값을 RefreshToken이 아닌 유저의 Id로 저장을 하는 방식을 택하면 어땠을까? 라는 생각을 하였다.

그렇게 생각한 이유는 매우 긴 key값은 메모리 측면뿐만 아니라 데이터 집합에서 키를 조회하는 데 비용이 많이 들 수 있기 때문에 좋은 생각이 아니라고 생각했기 때문이다.
