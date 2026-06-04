```java
public interface Publisher<T> {
  void subscribe(Subscriber<? super T> subscriber);
}

public interface Subscriber<T> {
  void onSubscribe(Subscription subscription);
  void onNext(T item);
  void onError(Throwable throwable);
  void onComplete();
}

public interface Subscription {
  void request(long n);
  void cancel();
}

public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
}
```

# Publisher
`subscribe()` 메서드를 활용하여 데이터를 생산한다.
누군가 subscribe 를 호출하면 Publisher 는 Subscriber 에게 데이터를 보낼 준비를 한다.
하나의 Publisher 에 여러 Subscriber가 구독할 수 있다.
각 Subscriber 는 독립적인 구독 상태값과 정보를 가진다.

# Subscriber
### onSubscribe
구독이 성립되는 시점에 가장 먼저 호출되는 메서드
Subscriber 가 Publisher 에게 `subscribe()` 를 호출했을때 Publisher 가 요청을 수락하고 나서 `onSubscribe()` 를 호출하게 되며, 이때 Subscription 객체를 전달한다.

### onNext
데이터가 하나 도착할 때 마다 호출되는 메서드
데이터가 10개인 경우 10번 호출되며, 데이터가 없는 경우 호출되지 않는다

### onComplete
모든 데이터가 전송이 완료되었을 때 호출되는 메서드
이 메서드가 호출된 이후 더이상 onNext 가 호출되지 않는다.

### onError
데이터 전송 중 에러가 발생했을 때 호출되는 메서드
Throwable 객체를 통해서 에러 정보를 전달 받고, 더이상 onNext 가 호출되지 않는다.

### onComplete 와 onError
둘 중 하나만 호출되며, 둘다 호출이 되지 않을 수도 있다.
둘다 호출이 되지 않는 경우는 무한 스트림 개념처럼 스트림이 끝나지 않는 경우에 해당된다.

# Subscription
### request
인자값 만큼 데이터를 데이터를 Publisher 에게 요청한다.
Subscriber 가 이 메서드를 호출해야만 Publisher 가 데이터를 보내기 시작한다.
Webflux 의 중요한 설계기법 중 하나이며, Subscriber 가 자신의 처리 능력에 맞춰서 데이터의 수신량을 직접 조절한다.

### cancel
구독을 취소하는 메서드
더이상 데이터를 받고 싶지 않을때 호출되며, Publisher 는 Subscriber 에게 데이터 전송을 중단한다.

### 별도의 인터페이스 분리
Subscription 없이 Subscriber가 Publisher에 직접 요청한다면 Publisher는 누구 요청인지 구분 불가능하다.
```
  Subscriber A ──request(10)──→ Publisher
  Subscriber B ──request(5)───→ Publisher  ← Publisher는 누구 요청인지 구분 불가
  Subscriber C ──cancel()─────→ Publisher
```
Subscription을 분리하면, 구독 관계 하나당 Subscription 객체 하나가 생기게 된다.
```
  Subscriber A ──→ Subscription A ──→ Publisher
  Subscriber B ──→ Subscription B ──→ Publisher
  Subscriber C ──→ Subscription C ──→ Publisher
```
각 Subscription 이 컨텍스트를 들고 있으므로, Publisher 는 누가 몇개의 요청을하고 취소하는지 Subscription 단위로 관리할 수 있다.

Subscriber 안에 request와 cancel이 있다면 Publisher는 Subscriber를 직접 호출해야 한다
request와 cancel은 Subscriber → Publisher 방향의 제어인데, Subscriber 안에 두면 Publisher가 Subscriber를 호출하는 역방향이 되어버리는 문제가 생긴다.

# Processor
Subscriber 로 `<T>` 값을 데이터로 받으며 Publisher 로 `<R>` 데이터를 반환한다
Mapper 와 같은 역할을 수행하며, 데이터를 받아 변환하거나 필터링 한다음 다시 내보낸다.
```
  Publisher --> [Processor] --> Subscriber
                 (둘 다 구현)
```

# 전체 흐름
```
  Subscriber          Publisher          Subscription
      |                   |                   |
      |---subscribe()---->|                   |
      |                   |---new------------>|
      |<--onSubscribe()---|<------------------|
      |                   |                   |
      |---request(n)------------------------->| ← 데이터 호출 발생
      |                   |<--trigger---------|
      |<--onNext(item1)---|                   |
      |<--onNext(item2)---|                   |
      |       ...         |                   |
      |<--onComplete()----|                   |

```

