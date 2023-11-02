## 개요

> Queue Collection을 처음 배웠을 당시 데이터를 Queue 내부에 삽입할 때 offer()라는 메서드를 사용했다.
> 최근 Java 기초를 다시 학습하면서 강의 자료에서는 add()를 사용하는 것을 보고 이 둘의 차이점이 문득 궁금해져서 정리해보려고 한다.

## LinkedList

우리는 보통 Queue를 선언할 때 LinkedList<>()로 생성을 한다.

LinkedList의 경우에서는 offer()과 add()의 차이점은 **없다고** 명시할 수 없다.

내부 로직을 먼저 파헤쳐보자!

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable {

    public boolean add(E e) {
      linkLast(e);
      return true;
    }

    public boolean offer(E e) {
      return add(e);
    }

    void linkLast(E e) {
      final Node<E> l = last;
      final Node<E> newNode = new Node<>(l, e, null);
      last = newNode;
      if (l == null)
        first = newNode;
      else
        l.next = newNode;
      size++;
      modCount++;
    }
}
```

Java에서 구현되어 있는 LinkedList Class의 경우에서는 offer()과 add()는 항상 true를 반환한다.

해당 이유는 큐가 무제한 크기를 가지므로 일반적으로 LinkedList를 사용한 큐에서 add를 호출할 때는 예외를 처리하지 않고 true를 반환한다.

offer()메서드는 add()메서드를 의존하고 사용하기 때문에 같은 역할을 한다고 볼 수 있다.

## 그렇다면 어떤 경우에 차이가 날까?

- 크기가 무제한이지 않은 경우에 차이가 있는 것을 확인할 수 있다.

- add()의 경우에는 Queue의 공간이 모두 가득 찾을 경우 exception이 발생하며, offer()의 경우에는 false를 반환한다.

### BlockingQueue

크기를 지정할 수 있는 Queue를 만들 수 있는 interface이다.

이를 활용해서 add() 와 offer()의 차이를 예제로 확인 해보자!

1. ArrayBlockingQueue

- 배열을 기반으로 한 정적인 크기를 가지는 큐이다.

2. LinkedBlockingQueue

- 크기를 지정할 수 있는 노드 기반의 큐이다.

BlockingQueue를 구현한 클래스는 위와 같이 두가지가 있습니다.

이번에 add() 와 offer()의 차이에 대한 예제를 다루는 것에는 둘 중 아무거나 사용해도 되지만
저는 1번 ArrayBlockingQueue를 사용하겠습니다.

```java
//크기가 3인 queue를 생성 -> 매개변수 capacity를 지정하면 그 값만큼의 size를 가진다.
Queue<Integer> queue = new ArrayBlockingQueue<>(3);
```

Java에서 구현되어 있는 ArrayBlockingQeueu Class 내부

```java
public class ArrayBlockingQueue<E> extends AbstractQueue<E> implements BlockingQueue<E>, java.io.Serializable {
    public boolean add(E e) {
      return super.add(e);  -> AbstractQeueue에서 구현한 add를 사용
    }
    /*AbstractQeueue에서 구현한 add
    public boolean add(E e) {
      if (offer(e))
        return true;
      else
        throw new IllegalStateException("Queue full");
    }
*/
    public boolean offer(E e) {
        Objects.requireNonNull(e); //null check!
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            if (count == items.length) // count의 개수와 ArrayBlockingQueue 내부 배열의 크기가 같다면 false 리턴
                return false;
            else {
                enqueue(e); // 배열의 크기가 같지 않다면 count++ 하고 값을 내부에 넣고 true 리턴
                return true;
            }
        } finally {
            lock.unlock();
        }
    }

    private void enqueue(E e) {
        // assert lock.isHeldByCurrentThread();
        // assert lock.getHoldCount() == 1;
        // assert items[putIndex] == null;
        final Object[] items = this.items;
        items[putIndex] = e;
        if (++putIndex == items.length) putIndex = 0;
        count++;
        notEmpty.signal();
    }

}
```

## 예제 (offer())

```java
public class Application {
    public static void main(String[] args) {
        Queue<Integer> queue = new ArrayBlockingQueue<>(3);
        System.out.println("1:" + queue.offer(1));
        System.out.println("2:" + queue.offer(2));
        System.out.println("3:" + queue.offer(3));
        System.out.println("4:" + queue.offer(4));
    }
}


----------------------
1:true
2:true
3:true
4:false
```

## 예제 (add())

```java
public class Application {
    public static void main(String[] args) {
        Queue<Integer> queue = new ArrayBlockingQueue<>(3);
        System.out.println("1:" + queue.add(1));
        System.out.println("2:" + queue.add(2));
        System.out.println("3:" + queue.add(3));
        System.out.println("4:" + queue.add(4));
    }
}


----------------------
Exception in thread "main" java.lang.IllegalStateException: Queue full
at java.base/java.util.AbstractQueue.add(AbstractQueue.java:98)
at java.base/java.util.concurrent.ArrayBlockingQueue.add(ArrayBlockingQueue.java:329)
at baseball.Application.main(Application.java:12)
1:true
2:true
3:true
```

## 요약

- 크기를 가진 Queue에서 Queue가 가득찬 경우 add()를 사용하면 IllegalStateException 예외를 반환

- 크기를 가진 Queue에서 Queue가 가득찬 경우 offer()를 사용하면 false 를 반환
