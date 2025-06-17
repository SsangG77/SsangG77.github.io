---
title: BehaviorRelay 가 subscribe사용이 가능한 이유
date: 2025-02-27 12:00:00 +0900
categories: [개념, RxSwift]
tags: [BehaviorRelay]
---

`BehaviorRelay`는 내부적으로 `BehaviorSubject`를 감싸고 있기 때문이다.

- `BehaviorRelay`는 Relay 타입이지만, 내부적으로 `BehaviorSubject`를 사용하여 동작
- `BehaviorSubject`는 Observable 프로토콜을 준수하므로, `subscribe`를 사용 가능

즉, `BehaviorRelay` 자체가 Observable을 래핑한 형태이기 때문에 구독(subscribe) 가능

### 내부 구조

```swift
public final class BehaviorRelay<Element>: ObservableType {
    private let subject: BehaviorSubject<Element>
    
    public var value: Element {
        return try! subject.value()
    }
    
    public init(value: Element) {
        subject = BehaviorSubject(value: value)
    }

    public func accept(_ event: Element) {
        subject.onNext(event)
    }

    public func subscribe<Observer: ObserverType>(_ observer: Observer) -> Disposable where Observer.Element == Element {
        return subject.subscribe(observer)
    }
}

```

- `BehaviorRelay` 내부에 `BehaviorSubject` (`subject`)가 있음
- `subscribe`를 호출하면 내부적으로 `BehaviorSubject`의 `subscribe`를 실행
- 즉, **`BehaviorRelay` 자체가 `ObservableType`을 준수하기 때문에 구독 가능**

### 예제 코드 - Subscribe 사용

```swift
import RxSwift
import RxRelay

let disposeBag = DisposeBag()
let relay = BehaviorRelay(value: "초기값")

// 구독 (내부적으로 BehaviorSubject의 subscribe 호출)
relay.subscribe { print("구독된 값: \($0)") }
    .disposed(by: disposeBag)

// 새로운 값 전달
relay.accept("새로운 값")

```

