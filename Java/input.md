## 개요

> 아마 대다수 사람들이 BufferedReader 사용법을 백준 사이트에서 알고리즘 문제풀이를 시작으로 배우게 될 것이다.
> 나도 마찬가지로 Scanner 클래스를 사용했지만 시간초과가 나와서 해결법을 구글링한 결과로 처음 BufferedReader 클래스를 사용했다.
> 그 당시에는 이 둘의 차이점을 단지 BufferedReader 클래스가 더 빠르다고 인지만 한 상태로 넘겼지만,
> 이번 기회에 조금더 자세하게 알아보고 정리를 하려고 한다.

## Scanner

Scanner 클래스는 바이트 형식으로 입력 데이터를 받은 후 사용자가 정의한 타입으로 변환하여 전송하는 클래스이다.

<br>

## Scanner 클래스 특징

- 기본적인 데이터 타입들을 Scanner의 메소드를 사용하여 입력받을 수 있다.
  - String 같은 경우는 next(), nextLine()을 사용할 수 있으며, int로 받고 싶은 경우 nextInt()를 사용하여 입력받으면 해당 타입으로 입력된다.
- Scanner클래스는 util 패키지에 내부에 있기때문에 import해서 사용해야 한다.

```java
import java.util.*; // util 패키지 내부에 있는 클래스 모두 import
import java.util.Scanner; // util 패키지 내부에 있는 Scanner 클래스 import
```

- 공백(띄어쓰기) 및 개행(줄 바꿈)을 기준으로 읽는다.(' ', '\t', '\r', '\n' 등)
- 데이터를 입력받을 경우 즉시 사용자에게 전송되며 입력받을 때마다 전송되어야 하기에 많은 시간이 소요된다.
- 입력 데이터는 내부 로직에 인해 정규식을 검사한다.
- UnChecked(Runtime) Exception으로 별도로 예외 처리를 명시할 필요가 없다.

## BufferedReader

Buffer(버퍼)를 통해 입력받은 문자를 쌓아둔 뒤 한 번에 문자열처럼 프로그램에게 데이터를 전송하는 클래스이다.

<br>

## BufferedReader 클래스 특징

- 데이터를 파싱하지 않고 String으로만 읽고 가져온다.
  - readLine() 클래스로 문자열을 읽어온다.
  - 읽고 가져온 해당 문자열을 가공해서 사용

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int number = Integer.parseInt(br.readLine()) // int형으로 가공
```

- BufferedReader 클래스는 io 패키지에 내부에 있기때문에 import해서 사용해야 한다.

```java
import java.io.*; // io 패키지 내부에 있는 클래스 모두 import
import java.io.BufferedReader; // io 패키지 내부에 있는 BufferedReader 클래스 import

```

- Checked Exception으로 반드시 예외 처리를 명시해야한다.(I/O Exception을 throw하거나 try/catch 해야한다.)
  - 보통은 I/O Exception을 throw사용한다.
- **Scanner 클래스와 다르게 별다른 정규식을 검사하지 않는다.**
- **Buffer(버퍼)가 있는 스트림이다.**

> 위 둘의 특징으로 인해 Scanner보다 성능이 우수하다.

# Scanner와 BufferedReader의 차이점

TODO
