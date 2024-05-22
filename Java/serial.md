> seliralVersionUID를 명시하는 이유에 대해 설명하겠습니다.

> Serializable을 구현하는 Class의 Versioning 용도로 serialVersionUID 변수를 사용합니다.

```java
public class InvalidArgumentException extends RuntimeException {

    @Serial
    private static final long serialVersionUID = 4156972833189035090L;

    public InvalidArgumentException(String message) {
        super(message);
    }
}

```

RuntimeException의 최상위 부모 클래스 Throwable Class가 Serializable을 구현하기 때문입니다.

serialVersionUID 명시는 필수가 아니라 **선택**입니다.
하지만 명시를 할 경우 이점에 대해서 설명하겠습니다.

- seliralVersionUID 없이 클래스 구조가 변경되면 역직렬화 과정에서 InvalidClassException 이 발생할 수 있습니다. 명시적인 seliralVersionUID 이를 방지할 수 있습니다.

- 클래스 버전 관리를 개발자가 직접 제어할 수 있습니다.

- 클래스의 구조가 변경되더라도, 같은 seliralVersionUID를 가진 클래스로의 역직렬화가 가능합니다. 특히, 오래된 데이터나 외부 시스템과의 통신에서 중요하게 작용합니다.
