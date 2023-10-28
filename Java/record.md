# 개요

> 프로젝트를 진행하면서 Itellij IDEA에서 나의 현재 클래스를 Record로 변경해볼 것을 권장했다.
> 자바를 배우면서 Record라는 단어를 처음 접했다.
> 무엇인지 궁금해서 구글링을 해서 바로 해당 내용을 학습 한 후 이렇게 기록해보려고 한다.

## Recod란??

- Java 14에서 새롭게 추가된 기능입니다.
- **데이터 클래스** 이며 순수하게 데이터를 보유하기 위한 특수한 종류의 클래스이다.
- **Entity** 혹은 **DTO클래스**를 생성시 상용화된 코드를 간결하게 나타낼 수 있다.

아래 코드는 내가 Record 변경을 권장 받은 **Reservation Class**이다.
Java를 사용해본 대부분의 사람들은 Class를 만들 때 아래와 같이 상용화된 필드 및 메서드를 작성합니다.

```java
public class Reservation {
   private final ProductRoom productRoom;
   private final String userName;
   private final String userPhoneNumber;
   private final LocalDateTime createdAt;

   public Reservation(ProductRoom productRoom, String userName, String userPhoneNumber, LocalDateTime createdAt) {
      this.productRoom = productRoom;
      this.userName = userName;
      this.userPhoneNumber = userPhoneNumber;
      this.createdAt = createdAt;
   }

   public ProductRoom productRoom() {
      return productRoom;
   }

   public String userName() {
      return userName;
   }

   public String userPhoneNumber() {
      return userPhoneNumber;
   }

   public LocalDateTime createdAt() {
      return createdAt;
   }
}

```

위 코드를 Record로 변경하면 다음과 같이 변경이 된다.

```java
public record Reservation(
        ProductRoom productRoom,
        String userName,
        String userPhoneNumber,
        LocalDateTime createdAt) {
}
```

private, final field, public constructor와
Record는 equals, hashcode, tostring 메서드는 Java 컴파일러에 의해 생성됩니다.

아래와 같이 변경되면 가장 큰 **이점**은 새로운 필드를 추가할 때 클래스 내부에 메서드 (getter, hashCode, eaquals, toString)

등을 업데이트를 해주지 않아도 되는 이점이 있다.

## Record 특징

- 레코드는 다른 클래스를 상속받을 수 없다.
- private final fields 이외의 인스턴스 필드를 선언할 수 없다.
- 선언되는 다른 모든 필드는 static 이어야 한다.
- 제네릭으로 될 수 있다.
- 인터페이스를 구현할 수 있다.

<!-- TODO: 코드 추가하기 -->
