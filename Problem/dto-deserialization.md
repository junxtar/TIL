## 개요

> 최종 프로젝트에서 front를 사용하면서 axios의 방식을 사용하여 데이터를 json형식으로 서버에 보내주는 작업을 하였다.

> 진행 도중 발생한 문제점을 기록하려고 한다.

### 문제점

가장 먼저 문제점이 무엇인지 설명을 하겠습니다. 우리팀의 회원가입 로직에는 이름, 이메일, 비밀번호를 받아서 회원가입을 하는데

중간 단계에서 이메일 인증을 한번 하고 회원가입을 하는 로직이다.

### UserEmailAuthReq

`Jakson`은 `JSON` 데이터 구조를 처리해주는 라이브러리 입니다.

jackson은 json을 역직렬화시 생성자를 활용하고 모든 인자를 받는 생성자에 @ConstructorProperties 어노테이션이 달려있으면

해당 생성자를 역직렬화에 사용하지만 없을 경우 기본 생성자를 사용하게 된다.

따라서 역직렬화 `JSON` -> `Object` 를 변환하는 방식에서는 **Getter**, **기본생성자** 를 필요로한다.

하지만 공식문서를 봤더니 파라미터에 @RequestBody가 있으면 HttpMessageConverter를 통해 HTTP body를 매핑해준다고 합니다.

HttpMessageConverter는 HTTP request message를 역직렬화(Object로 파싱) 해주는 역할을 합니다.

```java
@Getter
public class UserEmailAuthReq {

    @Pattern(regexp = EMAIL_REGEX, message = EMAIL_MESSAGE)
    private String email;

    @Builder
    private UserEmailAuthReq(String email) {
        this.email = email;
    }
}

```

### 에러 로그

```java
2024-01-13T13:46:02.216+09:00  WARN 3958 --- [nio-8080-exec-5] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.http.converter.HttpMessageNotReadableException: JSON parse error: Cannot construct instance of `com.clover.youngchat.domain.user.dto.request.UserEmailAuthReq` (although at least one Creator exists): no String-argument constructor/factory method to deserialize from String value ('email@email.com')]

```

관련된 글을 찾아보니 Object 멤버변수가 오직 하나일 경우(==생성자 인자가 하나일 경우)

@JsonCreator로 Object를 자동설정해줘 getter/ 기본생성자가 없이 처리해주는 Jackson이 제 역할을 하지 않는다고 한다.

### 해결 방법

기본 생성자를 추가해주었으며, 인자값이 하나여서 @JsonCreator로 설정되도 문제 없게

코드를 작성 (기본 생성자, 전체 생성자, getter)를 사용함으로써 문제를 해결하였다.

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class UserEmailAuthReq {

    @Pattern(regexp = EMAIL_REGEX, message = EMAIL_MESSAGE)
    private final String email;

    @Builder
    private UserEmailAuthReq(String email) {
        this.email = email;
    }
}

```

### 느낀점

이번 기회에 생각보다 간단한 에러였음에도 직렬화, 역직렬화에 대해서 추상적으로 알고 있었지만 보다 더 구체적으로 개념을 적립하였다.

정리를 해보면

- ObjectMapper, jackson binding에는 객체 대상의 클래스 형태에서 기본 생성자가 있어야 합니다.

- 기본 생성자가 없어도 역직렬화를 할 수 있습니다. -> jackson-module-parameter-names 모듈이 기본 생성자 없어도 다른 생성자로 대체해 역직렬화를 수행한다.
  - 단 인자값이 하나인 경우 @JsonCreator 로 인해서 Jackson 관련 모듈이 동작을 안해서 기본생성자가 필요하다.
