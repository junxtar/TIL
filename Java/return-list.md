# 개요

> 많은 개발자 들이 IDE의 자동완성의 힘을 많이 빌린다. 나도 그렇다.
> 자동완성은 양날의 검 같은 존재라고 생각한다.
> Stream을 활용하여 List를 반환하면서 두개의 자동완성이 제시되었고 이 둘의 차이점이 궁금해져서 정리해보려고 한다.

## 본문

### Collectors.toList()

```java
return return boards.stream()
        .map(BoardDto::new)
        .collect(Collectors.toList());
```

### Stram.toList()

```java
return boards.stream().map(BoardDto::new).toList();
```

### 차이점

- Version

  - 첫 번째 로직 (Collectors.toList())은 Java 8 이상에서 사용할 수 있다.

  - 두 번째 로직 (Stream.toList())은 Java 16에서 도입되었다. 따라서 Java 16 이상에서만 사용할 수 있다.

- 구현

  - 첫 번째 로직에서 Collectors.toList()는 일반적으로 ArrayList를 반환 즉, 이 리스트는 mutable(변경 가능)하다.
  - 두 번째 로직에서 Stream.toList()는 불변(immutable list)를 반환한다. 즉, 반환된 리스트를 변경 불가능

- 가독성 및 간결성
  - 두 번째 로직 (Stream.toList())은 코드가 더 간결하고 읽기 쉽다. 메서드 체인에서 collect(Collectors.toList()) 대신 toList()만 사용하면 되기 때문에 코드가 더 짧다.

## 결론

- Java 8 이상에서 실행되는 코드라면 첫 번째 로직을 사용해야 한다.
- Java 16 이상을 사용할 수 있는 환경에서는 두 번째 로직을 사용하는 것이 더 간결하고 불변 리스트를 반환하므로 안전할 수 있다.
