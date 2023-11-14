# 개요

> 지난 프로젝트 마지막에 properties파일을 암호화하기 위해서 jasypt파일을 암호화하는 시간을 가졌었다.
> 이번 ToDoList 만들기 프로젝트를 진행하면서 프로젝트 초기 설정에 jasypt라이브러리를 적용하려고 하였는데
> 예상치 못한 실수를 범했다.... 이를 기록해서 이번 실수와 같은 실수를 방지하기 위해서 기록을 하려고 한다.
> 추가로 구글링을 하면서 다른 사람이 범한 실수도 인상 깊게 느껴 추가로 기록해서 다음에 이 실수도 방지하려고 한다.

## 첫번째, 내가 저지른 실수

### 요약

먼저 요약 하자면 내가 설정한 암호화를 사용하지 않고 테스트 코드에서는 

다른 암호화를 생성해 그 추출한 값으로 application.yml 파일을 적용해서 일어난 상황이다.

**지난 프로젝트에 적용했던 방법**을 나는 그대로 가져왔다.

[Jasypt 라이브러리 프로젝트 적용기](https://github.com/junxtar/TIL/blob/main/Problem/jasypt.md)


<br>

해당 아래는 서버를 실행하였을때 나타나는 에러 문구이다.

application.yml 파일에 ENC로 암호화한 비밀번호를 읽어오지 못하고 있다.

사실상 비밀번호의 평문을 작성하면 url 및 uesrname도 마찬가지로 읽어오지 못하고 있다.

<!-- 1번 스크린샷 --><img width="1326" alt="스크린샷 2023-11-14 11 43 15" src="https://github.com/junxtar/TIL/assets/75934088/71feaf88-2a46-4aca-9556-a7e7f4cfefb2">


나는 이 암호화를 적용하기 위해서 암호화를 해주는 사이트가 아닌 테스트 코드를 작성해 직접 코드로 추출해내는 방식으로 구현을 하였다.

<!-- 2번 스크린샷 --><img width="786" alt="스크린샷 2023-11-14 11 47 59" src="https://github.com/junxtar/TIL/assets/75934088/91f3a675-3288-4e7e-bb12-fcb88b01e706">


위 그림을 보고 아마 눈치 채셨을 수도 있겠지만, 실제 코드를 작성하는 나는 전혀 방향성을 잡지 못했다.

사실 방향성을 잡지 못한 이유는 이 코드에 대해서 깊게 이해하지 못해서인 이유도 있다고 생각을 한다.

나는 위 테스트코드 사진에서 비밀번호를 왜 따로 설정해줘야하는지 그 이유를 지난 프로젝트에서는 찾지 못하였고, 그저 "사용하기 위해 필요한거겠지?" 라고 얉게 받아들이고 있었다.

```java
public class JasyptConfigTest {

    @Test
    void stringEncryptor() {
        // given
        StandardPBEStringEncryptor encryptor = new StandardPBEStringEncryptor();
        encryptor.setPassword(" ");        //이 부분에서 범용적인 테스트를 위해서 나의 비밀번호가 아닌 공백으로 비밀번호를 설정하였다.
        String test = "test";

        // when
        String encryptPassword = encryptor.encrypt(test);

        // then
        Assertions.assertEquals(encryptor.decrypt(encryptPassword), "test");
    }
}
```

암호화 테스트 코드를 작성을 할때 나의 비밀번호를 테스트한다는 생각보다는 <br>

범용적인 테스트를 해서 이 기능이 순수하게 작동하는지를 검사해야 한다고 생각해 지난 프로젝트에 이렇게 push를 하였다.

## 결론

저 테스트 코드에 설정한 비밀번호를 내가 config파일에 설정했던 비밀번호로 적용을 한 후 암호화를 할 데이터를 추출했어야했다.

이번 기회에 저 비밀번호의 용도를 확실하게 깨달은 것 같다.

## 두번째, 구글링을 하다가 인상깊게 본 실수

해당 아래 코드를 보면 알고리즘을 설정 할 수 있다.
주석문에서 가리키고 있는 부분을 보시면 된다.

알고리즘은

1번 = `"PBEWithMD5AndDES"`

2번 = `"PBEWithMD5AndTripleDES"`

더 많은 알고리즘이 있겠지만 자주 사용할 알고리즘은 이 두가지로 볼 수 있다.

2번 같은 경우는 기존 1번보다는 더 강화된 암호화 알고리즘이라고 생각하면 이해하기 수월할 것이다.

2번과 같은 알고리즘을 아래와 같이 config파일에 적용을 하면 application.yml or properties파일에서는 해당 알고리즘을 적용해 복호화를 할 것이다.

하지만 위에서 언급한 것처럼 암호화 전용 사이트에서 추출해내는 방식으로 적용을 했다면 에러가 날 것이다.

해당 이유는 암호화 전용 사이트에서는 default 알고리즘으로 `"PBEWithMD5AndDES"` 를 사용하기 때문이다.

구글링을 하면서 나도 이런 실수를 할 수도 있겠다 싶어서 이렇게 기록하면서 다음에 같은 실수를 범하는 것을 방지해야겠다.

```java
@Configuration
@EnableConfigurationProperties
public class JasyptConfig {

    private static final String PASSWORD = "DB_DECRYPT_KEY";
    private static final String ALGORITHM = "PBEWithMD5AndDES"; // < 이부분이다.
    private static final String KEY_OBJECTION_ITERATIONS = "1000";
    private static final String POOL_SIZE = "1";
    private static final String PROVIDER_NAME = "SunJCE";
    private static final String SALT_GENERATOR_CLASS_NAME = "org.jasypt.salt.RandomSaltGenerator";
    private static final String STRING_OUTPUT_TYPE = "base64";


    @Bean("jasyptStringEncryptor")
    StringEncryptor stringEncryptor() {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        config.setPassword(System.getenv(PASSWORD));
        config.setAlgorithm(ALGORITHM);
        config.setKeyObtentionIterations(KEY_OBJECTION_ITERATIONS);
        config.setPoolSize(POOL_SIZE);
        config.setProviderName(PROVIDER_NAME);
        config.setSaltGeneratorClassName(SALT_GENERATOR_CLASS_NAME);
        config.setStringOutputType(STRING_OUTPUT_TYPE);
        encryptor.setConfig(config);
        return encryptor;
    }

}
```
