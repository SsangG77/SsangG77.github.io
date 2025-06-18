---
title: StateObject란
date: 2023-08-26 12:00:00 +0900
categories: [개념, SwiftUI]
tags: []
---


# SwiftUI에서 `@StateObject`란?

SwiftUI에서 상태 관리는 앱의 흐름과 사용자 인터페이스를 동기화하는 핵심이다. `@StateObject`는 SwiftUI가 도입한 속성 래퍼로, **뷰 내부에서 `ObservableObject`를 생성하고 소유할 때** 사용된다.


## 1. 왜 `@StateObject`가 필요한가?

SwiftUI의 뷰는 상태에 따라 새로 렌더링된다. 이 과정에서 뷰가 다시 생성될 때마다 객체도 새로 만들어지면, 상태가 유지되지 않고 초기화된다.

이 문제를 해결하기 위해 **SwiftUI는 상태 객체를 한 번만 생성하고 계속 유지할 수 있도록 `@StateObject`를 제공**한다.

---

## 2. 언제 사용하는가?

| 상황                                               | 사용                     |
| ------------------------------------------------ | ---------------------- |
| View 내부에서 `ObservableObject` 인스턴스를 직접 생성하고 소유할 때 | ✅ `@StateObject`       |
| View 외부에서 전달받는 객체를 구독할 때                         | ✅ `@ObservedObject`    |
| 전역 상태를 환경에서 불러올 때                                | ✅ `@EnvironmentObject` |

---

## 3. 사용 예시

### 1) 모델 정의

```swift
final class CounterViewModel: ObservableObject {
    @Published var count: Int = 0
    
    func increase() {
        count += 1
    }
}
```

### 2) View에서 소유 및 사용

```swift
struct CounterView: View {
    @StateObject private var viewModel = CounterViewModel()
    
    var body: some View {
        VStack {
            Text("카운트: \(viewModel.count)")
            Button("증가") {
                viewModel.increase()
            }
        }
    }
}
```

* `@StateObject`로 선언했기 때문에 `CounterView`가 다시 렌더링되더라도 `viewModel`은 한 번만 생성됨
* 내부 상태가 유지됨

---

## 4. `@ObservedObject`와의 차이점

| 구분                   | @StateObject | @ObservedObject   |
| -------------------- | ------------ | ----------------- |
| View에서 직접 생성하는가?     | ✅ 예          | ❌ 아니오 (외부에서 주입받음) |
| 메모리 관리 책임            | 해당 View가 소유  | 상위 View 또는 외부가 소유 |
| View가 다시 생성될 때 상태 유지 | ✅ 유지됨        | ❌ 유지 안됨 (초기화됨)    |

> 결론: **생성하는 곳에서 `@StateObject`**, **전달받는 곳에서는 `@ObservedObject`**

---

## 5. 주의 사항

* **반드시 View 안에서 한 번만 생성되어야 한다**

  * 루트 뷰나 진입 시점에서만 `@StateObject`로 생성
  * 자식 뷰에서는 `@ObservedObject` 또는 `@EnvironmentObject`로 주입받아야 함

* **뷰의 라이프사이클과 객체의 생명주기를 맞춰야 한다**

  * 부모 뷰가 사라질 때 `@StateObject`로 소유한 객체도 함께 사라진다

---

## 마무리

`@StateObject`는 SwiftUI에서 상태를 안전하게 관리하기 위한 필수 도구다.
객체를 뷰 내부에서 직접 생성할 경우 무조건 `@StateObject`를 사용하고,
외부에서 주입받을 경우에는 `@ObservedObject`를 사용해야 한다.

정리하면 다음과 같다:

```swift
// 직접 생성 → @StateObject
@StateObject var vm = ViewModel()

// 외부에서 받음 → @ObservedObject
@ObservedObject var vm: ViewModel

// 환경에서 가져옴 → @EnvironmentObject
@EnvironmentObject var vm: ViewModel
```

적절한 시점에 올바른 속성 래퍼를 사용하면 SwiftUI에서의 상태 관리는 훨씬 명확해진다.
