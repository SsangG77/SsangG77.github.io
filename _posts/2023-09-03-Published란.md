---
title: Published란
date: 2023-09-03 12:00:00 +0900
categories: [개념, SwiftUI]
tags: []
---

# Swift의 `@Published`란?

Swift에서 `@Published`는 `Combine` 프레임워크의 속성 래퍼로, `ObservableObject` 클래스 안에서 **값이 변경될 때 자동으로 알림을 보내는 데** 사용된다.

`SwiftUI`와 함께 사용할 때 뷰가 상태 변화를 자동으로 감지하고 UI를 업데이트할 수 있도록 도와주는 핵심 구성요소다.

---

## 1. 언제 사용하나?

* `ObservableObject`를 따르는 클래스에서
* 특정 프로퍼티의 값이 바뀔 때마다
* 그 변화를 뷰에 자동으로 반영하고 싶을 때

즉, **객체의 내부 상태 변화를 SwiftUI 뷰에 알리고 싶을 때** 사용한다.

---

## 2. 기본 예시

```swift
import Combine

class UserViewModel: ObservableObject {
    @Published var username: String = "sangjin"
}
```

이제 이 `UserViewModel`을 SwiftUI 뷰에서 사용하면 `username`이 바뀔 때마다 UI가 자동으로 갱신된다.

```swift
struct UserView: View {
    @StateObject private var viewModel = UserViewModel()
    
    var body: some View {
        VStack {
            Text(viewModel.username)
            Button("Change Name") {
                viewModel.username = "cha sangjin"
            }
        }
    }
}
```

버튼을 누르면 `username`이 바뀌고, `Text`가 자동으로 갱신된다.

---

## 3. Combine에서 어떻게 동작하는가?

`@Published`는 내부적으로 **`Publisher`를 생성**한다. 이 Publisher는 값이 변경될 때마다 구독자에게 새 값을 전달한다.

```swift
viewModel.$username
    .sink { newValue in
        print("변경된 이름: \(newValue)")
    }
    .store(in: &cancellables)
```

* `$username` → `@Published`가 생성한 Publisher에 접근
* `sink` → 값이 바뀔 때 실행할 클로저 등록
* `store(in:)` → 구독을 메모리에 유지

---

## 4. 주의할 점

* `@Published`는 **`ObservableObject` 클래스 내부에서만 사용 가능**
* **구조체(struct)나 enum에서는 사용 불가**
* UI와 바인딩할 때는 항상 `@StateObject`, `@ObservedObject`, `@EnvironmentObject`와 함께 사용해야 한다
* `@Published` 프로퍼티를 관찰하려면 `$변수명`을 사용해야 한다

---

## 5. 간단한 정리

| 개념                                | 설명                            |
| --------------------------------- | ----------------------------- |
| `@Published`                      | 값이 바뀔 때마다 Publisher를 통해 알림 발생 |
| `@Published` + `ObservableObject` | SwiftUI와 연동 시 UI 자동 갱신        |
| `$변수명`                            | Publisher에 직접 접근할 때 사용        |

---

## 마무리

`@Published`는 Combine과 SwiftUI에서 상태를 연결해주는 핵심 요소다.
모델의 값을 바꿀 때마다 자동으로 알림을 보내고, 뷰는 이를 감지해 자동으로 UI를 갱신한다.

`@Published`는 다음과 같은 경우에 적절하다:

* 클래스의 프로퍼티가 바뀔 때마다 UI에 반영되어야 한다
* 뷰 모델에서 상태 변화 알림을 보내고 싶다
* Combine 구독을 통해 값의 변화를 추적하고 싶다

> 바뀌는 값이 있다면, `@Published`로 감싸는 것을 고려하자. SwiftUI는 그걸 감지해서 뷰를 갱신해준다.
