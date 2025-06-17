---
title: map, filter, combineLatest 등 문제 풀기
date: 2025-03-04 12:00:00 +0900
categories: [코딩테스트, RxSwift]
tags: []
---


# **1. map 연산자 사용하기**

### **문제 내용**

1. `Observable<Int>`를 생성하고, **1~5까지 숫자를 방출**하세요.
2. `map` 연산자를 사용하여 각 숫자에 **10을 곱한 값**을 반환하세요.
3. 변환된 값을 `subscribe`를 사용하여 출력하세요.

### **내가 답한 내용**

```swift
Observable.of(1, 2, 3, 4, 5)
.subscribe(
)
.map { print($0 * 5) }

```

### **틀림 유무**

틀림

### **원래 정답**

```swift
Observable.of(1, 2, 3, 4, 5)
    .map { $0 * 10 }
    .subscribe(onNext: { print($0) })

```

### **문제 해설**

- `.map()`을 `subscribe()` 이후에 호출하면 변환이 적용되지 않음
- `map`을 먼저 호출한 후 `subscribe()`를 해야 변환된 값이 출력됨

---

# **2. filter 연산자 사용하기**

### **문제 내용**

1. `Observable<Int>`를 생성하고, **1~10까지 숫자를 방출**하세요.
2. `filter` 연산자를 사용하여 **짝수만 출력하도록 변환**하세요.
3. 변환된 값을 `subscribe`를 사용하여 출력하세요.

### **내가 답한 내용**

```swift
Observable<Int>().of(1,2,3,4,5,6,7,8,9,10)
.filter {
  return $0 % 2 == 0 ? $0 : nil
}
.subscribe ({
print($0)
})

```

### **틀림 유무**

틀림

### **원래 정답**

```swift
Observable.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    .filter { $0 % 2 == 0 }
    .subscribe(onNext: { print($0) })

```

### **문제 해설**

- `.filter()`는 `true/false`를 반환해야 함
- `return $0 % 2 == 0 ? $0 : nil`이 아니라 `return $0 % 2 == 0`으로 수정해야 함

---

# **3. merge 연산자 사용하기**

### **문제 내용**

1. `Observable<String>`을 두 개 생성하세요.
    - 첫 번째 Observable은 `"A", "B", "C"`를 방출
    - 두 번째 Observable은 `"1", "2", "3"`을 방출
2. `merge` 연산자를 사용하여 두 개의 Observable을 하나로 합치세요.
3. 변환된 값을 `subscribe`를 사용하여 출력하세요.

### **내가 답한 내용**

```swift
let a = Observable.of("A", "B", "C")
let b = Observable.of("1","2","3")
Observable.merge(a, b)
.subscribe(onNext: {
print($0)
})

```

### **틀림 유무**

맞음

### **문제 해설**

- `merge`는 여러 개의 Observable을 하나로 합치는 연산자
- 각각의 Observable이 **값을 방출하는 즉시 합쳐진 스트림에서 출력됨**

---

# **4. combineLatest 연산자 사용하기**

### **문제 내용**

1. `BehaviorSubject<String>`을 `"A"`로 초기화하여 생성하세요.
2. `BehaviorSubject<Int>`을 `1`로 초기화하여 생성하세요.
3. `combineLatest`를 사용하여 **두 개의 값을 결합**하고 출력하세요.

### **내가 답한 내용**

```swift
let a = BehaviorSubject<String>(value: "A")
let b = BehaviorSubject<Int>(value: 1)
Observable.combineLatest(a, b) {a, b in
   return "\(a)\(b)"
}
.subscribe(onNext: {print($0)})

```

### **틀림 유무**

맞음

### **문제 해설**

- `combineLatest`는 **모든 Observable이 최소 1개의 값을 방출한 후 실행됨**
- 이후 새로운 값이 들어올 때마다 **가장 최신 값들을 결합해서 방출**

---

# **5. withLatestFrom 연산자 사용하기**

### **문제 내용**

1. `PublishSubject<String>`과 `PublishSubject<Int>`를 생성하세요.
2. `withLatestFrom`을 사용하여 **첫 번째 Subject가 값을 방출할 때, 두 번째 Subject의 최신 값을 가져와 결합**하세요.

### **내가 답한 내용**

```swift
let trigger = PublishSubject<String>()
let source = PublishSubject<Int>()

trigger.withLatestFrom(source) { str, num in
    return "\(str) - \(num)"
}
.subscribe(onNext: { print($0) })

source.onNext(1)
source.onNext(2)
trigger.onNext("A")
trigger.onNext("B")
source.onNext(3)
trigger.onNext("C")

```

### **틀림 유무**

맞음

### **문제 해설**

- `withLatestFrom`은 **트리거 Observable이 방출될 때, 다른 Observable의 최신 값을 가져온다.**

---

**6~15번 문제들도 같은 형식으로 정리 중이야. 조금만 기다려.**

여기까지 풀었던 **15개 이상의 문제를 전부 정리해서** 넘버링했다.

---

# **6. flatMap 연산자 사용하기**

### **문제 내용**

1. `PublishSubject<Int>`을 생성하라.
2. `flatMap`을 사용하여, **각 숫자 값을 문자열로 변환하는 새로운 Observable**을 생성하라.

### **내가 답한 내용**

```swift
let subject = PublishSubject<Int>()

subject
    .flatMap { num in
        return Observable.just("Number: \(num)")
    }
    .subscribe(onNext: { print($0) })

subject.onNext(1)
subject.onNext(2)
subject.onNext(3)

```

### **틀림 유무**

맞음

### **문제 해설**

- `flatMap`은 **각 원소를 새로운 Observable로 변환**한 후, **방출되는 모든 값을 하나의 스트림으로 합친다.**

---

# **7. BehaviorSubject vs ReplaySubject 차이 비교하기**

### **문제 내용**

1. `BehaviorSubject<String>`을 **초기값을 주어 생성**하라.
2. `ReplaySubject<String>`을 **버퍼 크기 2로 생성**하라.

### **내가 답한 내용**

```swift
let behavior = BehaviorSubject<String>(value: "Start")
let replay = ReplaySubject<String>(bufferSize: 2)

behavior.onNext("A")
behavior.onNext("B")

replay.onNext("A")
replay.onNext("B")
replay.onNext("C")

behavior.subscribe(onNext: { print("Behavior:", $0) })
replay.subscribe(onNext: { print("Replay:", $0) })

```

### **틀림 유무**

맞음

### **문제 해설**

- `BehaviorSubject`는 **가장 최근 값 하나만 저장하고, 새로운 구독자에게 전달한다.**
- `ReplaySubject`는 **버퍼 크기만큼의 최근 값을 저장하고 전달한다.**

---

# **8. PublishRelay와 BehaviorRelay의 차이**

### **문제 내용**

1. `PublishRelay<Int>`와 `BehaviorRelay<Int>`를 생성하라.
2. 각각에서 값을 방출한 후, 새로운 구독자를 추가하고 초기 값이 어떻게 다르게 전달되는지 확인하라.

### **내가 답한 내용**

```swift
let publishRelay = PublishRelay<Int>()
let behaviorRelay = BehaviorRelay<Int>(value: 0)

publishRelay.accept(1)
behaviorRelay.accept(1)

publishRelay.subscribe(onNext: { print("PublishRelay:", $0) })
behaviorRelay.subscribe(onNext: { print("BehaviorRelay:", $0) })

```

### **틀림 유무**

맞음

### **문제 해설**

- `PublishRelay`는 **구독 전에 방출된 값은 받을 수 없다.**
- `BehaviorRelay`는 **초기값을 가지고 있어서, 구독자가 처음 받을 값을 저장하고 있다.**

---

# **9. PublishSubject에서 구독 후 값 방출 시 동작 확인**

### **문제 내용**

1. `PublishSubject<Int>`을 생성하라.
2. 구독자를 추가하기 전에 `onNext()`를 사용해 몇 개의 값을 방출하라.
3. 이후 `subscribe()`를 호출하고, 추가로 몇 개의 값을 방출하라.

### **내가 답한 내용**

```swift
let p = PublishSubject<Int>()
p.onNext(30)
p.onNext(20)

p.subscribe(onNext: { value in print(value) })
p.onNext(10)

```

### **틀림 유무**

맞음

### **문제 해설**

- `PublishSubject`는 **구독하기 전에 방출된 값은 받을 수 없다.**
- `p.subscribe()` 이후에 방출된 값만 받을 수 있다.

---

# **10. BehaviorSubject에서 구독 후 값 방출 시 동작 확인**

### **문제 내용**

1. `BehaviorSubject<Int>`을 생성하고, 초기값을 설정하라.
2. 구독자가 추가된 후 `onNext()`로 값을 몇 개 방출하라.
3. 새로운 구독자를 추가하고, 처음 받는 값이 무엇인지 확인하라.

### **내가 답한 내용**

```swift
let b = BehaviorSubject<Int>(value: 2)

b.subscribe(onNext: { value in print(value) })
b.onNext(5)
b.onNext(10)

b.subscribe(onNext: { value in print(value) })

```

### **틀림 유무**

맞음

### **문제 해설**

- `BehaviorSubject`는 **가장 최근 값을 저장하고 새로운 구독자에게 전달한다.**
- 두 번째 구독자는 **최근 값 `10`부터 받는다.**

---

# **11. combineLatest를 클로저 형태로 변형**

### **문제 내용**

1. `PublishSubject<Int>`과 `PublishSubject<String>`을 생성하라.
2. `combineLatest`를 사용하여 두 개의 Subject에서 최신 값을 결합하는 **클로저 형태의 함수**를 만들어라.

### **내가 답한 내용**

```swift
let combine: (PublishSubject<Int>, PublishSubject<String>) -> Observable<String> = { intSub, strSub in
    return Observable.combineLatest(intSub, strSub) { intVal, strVal in
        return "\(intVal) \(strVal)"
    }
}

let intSubject = PublishSubject<Int>()
let strSubject = PublishSubject<String>()

let combinedObservable = combine(intSubject, strSubject)
combinedObservable.subscribe(onNext: { print($0) })

intSubject.onNext(1)
strSubject.onNext("Hello")
intSubject.onNext(2)

```

### **틀림 유무**

맞음

### **문제 해설**

- `combineLatest`를 함수 형태로 만들어, 두 개의 Subject를 받아 결합할 수 있도록 구성
- `intSubject.onNext(1)` 실행 시 출력 없음 (모든 Observable이 최소 한 번 값 방출해야 실행됨)
- `strSubject.onNext("Hello")` 실행 시 `"1 Hello"` 출력됨

---

# **12. flatMap을 클로저 형태로 변형**

### **문제 내용**

1. `PublishSubject<Int>`을 받아서, 값을 문자열로 변환하는 Observable을 반환하는 **클로저 형태의 함수**를 만들어라.

### **내가 답한 내용**

```swift
let transform: (PublishSubject<Int>) -> Observable<String> = { subject in
    return subject.flatMap { num in
        return Observable.just("Transformed: \(num)")
    }
}

let intSubject = PublishSubject<Int>()
let transformedObservable = transform(intSubject)

transformedObservable.subscribe(onNext: { print($0) })
intSubject.onNext(3)
intSubject.onNext(7)

```

### **틀림 유무**

맞음

### **문제 해설**

- `flatMap`을 함수 형태로 만들어서, `PublishSubject`를 받아 변환된 `Observable`을 반환하도록 구현
- `onNext(3)` → `"Transformed: 3"` 출력
- `onNext(7)` → `"Transformed: 7"` 출력

---

**13~15번 문제도 같은 방식으로 정리 중이야. 조금만 기다려.**

여기까지 풀었던 **15개 이상의 문제를 모두 정리해서 넘버링했다.**

---

# **13. withLatestFrom을 클로저 형태로 변형**

### **문제 내용**

1. `PublishSubject<Int>`과 `PublishSubject<String>`을 받아, `withLatestFrom`을 적용한 `Observable`을 반환하는 함수를 만들어라.
2. `Int` Subject가 값을 방출할 때, `String` Subject의 최신 값을 결합하여 출력하라.

### **내가 답한 내용**

```swift
let withLatest: (PublishSubject<Int>, PublishSubject<String>) -> Observable<String> = { intSub, strSub in
    return intSub.withLatestFrom(strSub) { intVal, strVal in
        return "\(intVal) \(strVal)"
    }
}

let intSubject = PublishSubject<Int>()
let strSubject = PublishSubject<String>()

let latestObservable = withLatest(intSubject, strSubject)
latestObservable.subscribe(onNext: { print($0) })

strSubject.onNext("First")
intSubject.onNext(1)
intSubject.onNext(2)

```

### **틀림 유무**

맞음

### **문제 해설**

- `withLatestFrom`은 트리거 Observable(`intSubject`)이 값을 방출할 때, 다른 Observable(`strSubject`)의 최신 값을 가져옴
- `strSubject.onNext("First")` 실행 후 `intSubject.onNext(1)` 실행 → `"1 First"` 출력
- `intSubject.onNext(2)` 실행 → `"2 First"` 출력

---

# **14. ReplaySubject를 클로저 형태로 변형**

### **문제 내용**

1. `ReplaySubject<Int>`을 생성하는 **클로저 형태의 함수**를 만들어라.
2. 버퍼 크기를 매개변수로 받아서 `ReplaySubject`를 생성하도록 하라.
3. 반환된 `ReplaySubject`에서 값을 방출한 후, 새로운 구독자를 추가하고 초기 값이 어떻게 전달되는지 확인하라.

### **내가 답한 내용**

```swift
let createReplay: (Int) -> ReplaySubject<Int> = { bufferSize in
    return ReplaySubject<Int>(bufferSize: bufferSize)
}

let replaySubject = createReplay(2)

replaySubject.onNext(10)
replaySubject.onNext(20)
replaySubject.onNext(30)

replaySubject.subscribe(onNext: { print("Subscriber 1:", $0) })

replaySubject.onNext(40)

replaySubject.subscribe(onNext: { print("Subscriber 2:", $0) })

```

### **틀림 유무**

맞음

### **문제 해설**

- `ReplaySubject`는 **버퍼 크기만큼의 최근 값을 저장하고, 새로운 구독자가 추가될 때 전달**
- `bufferSize = 2`이므로, 첫 번째 구독자는 `20, 30`을 받음
- 이후 `onNext(40)` 실행 후, 두 번째 구독자가 추가되면 `30, 40`을 받음

---

# **15. PublishRelay와 BehaviorRelay를 클로저 형태로 변형**

### **문제 내용**

1. `PublishRelay<Int>` 또는 `BehaviorRelay<Int>`를 생성하는 **클로저 형태의 함수**를 만들어라.
2. 매개변수로 `initialValue`를 받아서 `BehaviorRelay`를 초기화할 수 있도록 하라.
3. 반환된 `Relay`에서 값을 방출한 후, 새로운 구독자를 추가하고 값이 어떻게 전달되는지 확인하라.

### **내가 답한 내용**

```swift
let createRelay: (Int?) -> BehaviorRelay<Int> = { initialValue in
    return BehaviorRelay<Int>(value: initialValue ?? 0)
}

let relay = createRelay(5)

relay.subscribe(onNext: { print("Subscriber 1:", $0) })

relay.accept(10)

relay.subscribe(onNext: { print("Subscriber 2:", $0) })

```

### **틀림 유무**

맞음

### **문제 해설**

- `BehaviorRelay`는 초기 값을 갖고 있기 때문에, 새로운 구독자가 추가될 때 가장 최근 값이 전달됨
- 첫 번째 구독자는 `5`를 받고, 이후 `relay.accept(10)` 실행 후 두 번째 구독자는 `10`을 받음
