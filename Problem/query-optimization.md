# 개요

> 지금 현재 최종 프로젝트를 진행하고 있다. 최종 프로젝트는 어떻게 하면 최상의 서비스를 줄 수 있는지를 중점으로 대용량 트래픽을 처리하고, 쿼리를 최적화함으로써
> 보다 편리한 서비스를 제공하는데에 있다.

> 현재 친구 목록에서 특정 단어로 친구의 이름을 검색하는 쿼리를 작성하고 있는데 이 로직을 어떻게 개선해나가는지에 대한 기록을 남기려고 한다.

## ERD

현재 서비스 코드를 작성 중이며, 사용자가 서로 친구를 맺을 수 있는 서비스이다.

![post](../img/q1.png)

## 친구 검색 서비스 로직

어떤 흐름으로 서비스가 구현되는지는 주석으로 처리를 하였다.

```java
@Transactional(readOnly = true)
    public List<FriendGetSearchListRes> getFriendSearchList(String keyword, User user) {
        // ----------------------------------------------
        // keyword 로 자신의 친구를 검색한다.
        // ex) 나: junxtar         친구: 홍길동, 이순신, 유관순, 손흥민
        // 검색창: 순
        // 결과: 이순신, 유관순
        // ----------------------------------------------

        long startTime = System.currentTimeMillis();

        // 1. 자신의 친구 목록을 꺼내온다.
        List<Friend> friends = findByUser(user);

        // 2. 자신의 친구 목록에서 친구의 id를 뽑아낸다.
        List<Long> friendsId = friends.stream()
            .map(friend -> friend.getFriend().getId())
            .toList();

        // 친구 id 목록으로 user 정보를 추출한 뒤 keyword가 포함되어 있는 친구를 dto로 변환
        List<FriendGetSearchListRes> collect = userRepository.findUsersByIdIn(friendsId)
            .orElseThrow(() -> new GlobalException(NOT_FOUND_USER))
            .stream()
            .filter(u -> u.getUsername().contains(keyword))
            .map(u -> FriendGetSearchListRes.to(u.getUsername(), u.getProfileImage()))
            .collect(Collectors.toList());

        long endTime = System.currentTimeMillis();
        log.info("실행 시간: " + ((endTime - startTime)));
        return collect;
    }
```

## 출력 결과

```java
2024-01-10T20:10:58.748+09:00  INFO 37134 --- [nio-8080-exec-3] c.c.y.d.friend.service.FriendService     : 실행 시간: 21
```

## QueryDSL

QueryDSL에 대해서는 자세하게 설명되어 있는 참고 블로그 및 레퍼런스가 많기 때문에 설명에 대한 글은 생략하겠다.

## Spring Boot 2.xx QueryDSL gradle 설정

<details>

<summary>

```java
plugins {
    ...
    id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
}

compileQuerydsl{
options.annotationProcessorPath = configurations.querydsl
}

configurations {
...
querydsl.extendsFrom compileClasspath
}

def querydslSrcDir = 'src/querydsl/generated'

querydsl {
library = "com.querydsl:querydsl-apt"
jpa = true
querydslSourcesDir = querydslSrcDir
}

sourceSets {
main {
java {
srcDirs = ['src/main/java', querydslSrcDir]
    }
}
}

project.afterEvaluate {
project.tasks.compileQuerydsl.options.compilerArgs = [
"-proc:only",
"-processor", project.querydsl.processors() +
',lombok.launch.AnnotationProcessorHider$AnnotationProcessor'
]
}

dependencies {
implementation("com.querydsl:querydsl-jpa") // querydsl
implementation("com.querydsl:querydsl-apt") // querydsl
}
```

</summary>

</details>

## Spring Boot 2.xx QueryDSL gradle 설정

<details>

<summary>

```java
dependencies {
		....

    	// 9. QueryDSL 적용을 위한 의존성 (SpringBoot3.0 부터는 jakarta 사용해야함)
    implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
    annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jakarta"
    annotationProcessor "jakarta.annotation:jakarta.annotation-api"
    annotationProcessor "jakarta.persistence:jakarta.persistence-api"

}

```

</summary>

</details>
```
