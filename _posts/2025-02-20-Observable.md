---
title: Observable
date: 2025-02-20 12:00:00 +0900
categories: [개념, RxSwift]
tags: []
---



# 비동기 흐름을 다루는 핵심 개념, Observable이란?

앱 개발을 하다 보면 버튼 클릭, 네트워크 응답, 텍스트 필드 변경 등 다양한 비동기 이벤트를 다뤄야 한다. 이럴 때 단순한 콜백 방식은 코드 흐름을 이해하기 어렵게 만들 수 있다.
이를 해결하기 위해 등장한 개념이 바로 `Observable`이다.

## Observable은 무엇인가?

`Observable`은 값의 변화를 **관찰할 수 있는 스트림**이다. 대표적으로 RxSwift에서 사용되며, Combine에서는 `Publisher`라는 이름으로 같은 개념이 구현되어 있다. 핵심은 다음과 같다.

* 비동기 데이터 흐름을 다룬다.
* 데이터를 직접 요청하지 않아도, 새로운 값이 생기면 자동으로 알려준다.
* 콜백이 아니라 **구독(subscribe)** 기반으로 동작한다.

## 예제를 통한 이해

아래는 RxSwift에서 Observable을 사용하는 기본적인 예제다.

```swift
import RxSwift

let observable = Observable<Int>.just(1)

observable.subscribe(onNext: { value in
    print("다음 값: \(value)")
})
```

* `just(1)`은 1이라는 값을 단 한 번 방출하고 종료한다.
* `subscribe`를 통해 이 값이 방출될 때 실행할 동작을 정의할 수 있다.

이 흐름은 "Observable이 값을 방출 → Observer가 반응"하는 구조다.

## 왜 Observable이 필요한가?

예를 들어, 네트워크 요청 결과를 처리하고 싶을 때, 단순한 클로저 방식보다 Observable을 사용하면 다음과 같이 명확한 흐름으로 작성할 수 있다.

```swift
apiService.getUser()
    .observe(on: MainScheduler.instance)
    .subscribe(onNext: { user in
        print("유저 정보: \(user)")
    }, onError: { error in
        print("에러 발생: \(error)")
    })
    .disposed(by: disposeBag)
```

여기서 중요한 건 다음과 같다:

* `observe(on:)`으로 스레드 전환을 간단히 처리
* `onNext`, `onError`, `onCompleted` 분리로 가독성 향상
* `disposeBag`을 통한 메모리 관리

## Cold Observable vs Hot Observable

* **Cold Observable**: 구독할 때마다 처음부터 이벤트를 시작함 (예: `Observable.create`)
* **Hot Observable**: 이벤트가 이미 발생 중이며, 구독자는 중간부터 데이터를 받음 (예: `PublishSubject`, `BehaviorSubject`)

## SwiftUI와 Observable의 차이점

SwiftUI에서는 `ObservableObject`, `@Published` 같은 속성으로 상태를 감지한다.

```swift
class ViewModel: ObservableObject {
    @Published var count: Int = 0
}
```

이와 같이 SwiftUI는 뷰와 상태를 자동으로 연결하고, 값이 바뀌면 뷰가 다시 렌더링된다. RxSwift의 Observable과 개념은 비슷하지만, 동작 방식은 프레임워크에 따라 다르다.

## 마무리

`Observable`은 복잡한 비동기 흐름을 단순하고 선언적으로 다룰 수 있게 해준다. 특히 RxSwift나 Combine과 함께 쓰면, 이벤트 흐름을 조합하거나, 조건에 따라 처리하거나, 스레드를 전환하는 작업이 훨씬 명확해진다.

"변화를 감지하고 반응한다"는 것은 앱의 기본 동작과 밀접하게 연결돼 있다.
따라서 `Observable`은 단순한 기술 개념이 아니라, 앱 전체 구조를 설계하는 중요한 패러다임이다.

---

