Java에서 String이 **왜? (Why)** 불변(immutable)인지에 대해서 설명하겠습니다.

Java는 String을 String pool에서 관리합니다. 

즉, 동일한 상태값을 가진 String이면 공유하게 됩니다. 이것이 가능한 이유는 String이 immutable하기 때문입니다. 

String pool을 통해 String을 관리함으로써 Heap 영역의 많은 메모리를 절약할 수 있는 이점이 있습니다.

또한, String은 개발함에 있어서 사용자의 비밀번호, URL 주소, Port와 Host 정보 등 다양한 곳에 쓰이고 있습니다. String이 불변하기 때문에 보안적인 측면에서 이점을 가져올 수 있습니다.

마지막으로 멀티쓰레딩 환경에서 thread-safe 합니다. 값의 변경 가능성이 없기 때문에 멀티 쓰레딩 환경에서 동기화 문제에 대해서 피할 수 있습니다. 
