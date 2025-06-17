---
title: PublishRelay
date: 2025-02-27 12:00:00 +0900
categories: [개념, RxSwift]
tags: [PublishRelay]
---


PublishRelay는 PublishSubject를 대체하는 Relay타입으로,

1. ***초기값 없이*** 이벤트를 방출하며, 새로운 구독자는 새로운 값부터 수신합니다.
2. `.accept`로 값을 추가하고, `.completed`나 `.error`없이 동작합니다.

# 사용 예제

```swift
import RxSwift
import RxRelay

let relay = PublishRelay<String>()
let disposeBag = DisposeBag()

// 첫 번째 구독자
relay.subscribe {
    print("구독 1: \($0)")
}.disposed(by: disposeBag)

// 값 추가
relay.accept("첫 번째 값")  
relay.accept("두 번째 값")

// 두 번째 구독자 추가
relay.subscribe {
    print("구독 2: \($0)")
}.disposed(by: disposeBag)

// 값 추가
relay.accept("세 번째 값")

```

출력결과

```swift
구독 1: 첫 번째 값
구독 1: 두 번째 값
구독 1: 세 번째 값
구독 2: 세 번째 값  // ✅ 두 번째 구독자는 이전 값은 못 받고 새로운 값부터 받음

```

# `PublishRelay` vs `BehaviorRelay` 차이


| 특징 | `PublishRelay` | `BehaviorRelay` |
| --- | --- | --- |
| **초기값** | ❌ 없음 | ✅ 있음 (`value`로 접근 가능) |
| **새 구독자 처리** | ✅ 새로운 값부터 받음 | ✅ 최신 값부터 받음 |
| **이전 값 저장 여부** | ❌ 없음 | ✅ 최신 값 저장 |
| **값 추가 방법** | `.accept(값)` | `.accept(값)` |
| **끊김 여부** | ❌ 끊기지 않음 | ❌ 끊기지 않음 |

**즉, `PublishRelay`는 `PublishSubject`처럼 동작하지만, 에러 없이 계속 동작하는 특징이 있음.**

**이전 값을 저장하지 않기 때문에, 새로운 구독자는 이전 데이터를 받지 않음.**

## **`PublishRelay`를 언제 사용해야 할까?**

- **UI 이벤트 처리** (ex. 버튼 클릭, 네트워크 요청 등)
- **이전 값을 저장할 필요가 없는 데이터 스트림**
- **끊기지 않는 이벤트 스트림이 필요한 경우** (예: `Subject` 대신 사용)


### **버튼 클릭 이벤트 처리**

```swift
import RxSwift
import RxRelay

let buttonTapRelay = PublishRelay<Void>()
let disposeBag = DisposeBag()

// 구독: 버튼이 눌릴 때마다 실행됨
buttonTapRelay.subscribe {
    print("버튼 클릭됨!")
}.disposed(by: disposeBag)

// 버튼이 눌렸다고 가정
buttonTapRelay.accept(())
buttonTapRelay.accept(())

```

**출력 결과**

```
버튼 클릭됨!
버튼 클릭됨!
```

- 버튼이 클릭될 때마다 새로운 이벤트를 방출
- 과거 이벤트는 저장되지 않음 (필요한 순간에만 실행)
