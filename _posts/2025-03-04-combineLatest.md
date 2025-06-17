---
title: combineLatest
date: 2025-03-04 12:00:00 +0900
categories: [개념, RxSwift]
tags: []
---

여러 Observable에서 가장 최신 값을 합쳐서 방출하는 연산자

모든 Observable이 최소 하나의 값을 방출하여야함

여러 Observable중 최신 값이 들어오면 값들이 조합되어 새로운 값 방출

### 예제 코드

```swift
let first = BehaviorSubject(value: "A")
let second = BehaviorSubject(value: 1)

Observable.combineLatest(first, second) { str, num in
    return "\(str) - \(num)"
}
.subscribe(onNext: { print($0) })

```

- 실행 흐름

```swift

first.onNext("B")  
B - 1  ✅ (첫 번째 Subject가 새로운 값을 방출했으므로, 최신 값끼리 결합)

second.onNext(2)  
B - 2  ✅ (두 번째 Subject가 새로운 값을 방출했으므로, 최신 값끼리 결합)

first.onNext("C")  
C - 2  ✅ (첫 번째 Subject가 새로운 값을 방출했으므로, 최신 값끼리 결합)
```

### 사용하는 경우

- UI 바인딩: 텍스트 필드 입력과 버튼 상태를 결합할 때
- API 요청: 여러 개의 네트워크 요청 결과를 합칠 때
- 데이터 흐름 제어: 여러 개의 데이터 스트림을 동기화할 때
