---
title: SwiftUI의 onReceive란?
date: 2025-02-06 12:00:00 +0900
categories: [개념, Swift]
tags: []
---


# SwiftUI의 `onReceive`: 외부 이벤트에 반응하는 방법

앱을 개발하다 보면 외부에서 발생하는 이벤트나 주기적인 데이터 변경에 뷰가 반응해야 할 때가 있다. 예를 들어, 타이머, 알림(Notification), 네트워크 응답 등이다. SwiftUI에서는 이런 외부 이벤트를 `onReceive`를 통해 간결하게 처리할 수 있다.

이번 글에서는 `onReceive`의 동작 원리, 사용법, 실제 예제를 통해 어떻게 사용하는지 살펴본다.

---

## onReceive란?

`onReceive(_:perform:)`는 SwiftUI View에서 **Combine 프레임워크의 Publisher를 구독**하고, **Publisher가 값을 방출할 때마다 지정한 클로저를 실행**할 수 있도록 해주는 뷰 모디파이어(View Modifier)다.

즉, 외부 이벤트를 View 내부에서 처리할 수 있게 도와주는 기능이다.

---

## 기본 문법

```swift
.onReceive(Publisher) { value in
    // 받은 값을 기반으로 처리
}
```

`onReceive`는 View의 생명주기를 따라가기 때문에 View가 사라지면 자동으로 구독도 해제된다. 별도로 메모리 관리를 하지 않아도 된다.

---

## 예제 1. Timer와 함께 사용하기

가장 대표적인 사용 예는 `Timer`를 활용한 주기적 업데이트다.

```swift
import SwiftUI

struct TimerView: View {
    @State private var currentTime = Date()

    let timer = Timer.publish(every: 1, on: .main, in: .common).autoconnect()

    var body: some View {
        Text("현재 시각: \(currentTime)")
            .onReceive(timer) { input in
                self.currentTime = input
            }
    }
}
```

* `Timer.publish`는 Combine의 `Publisher`를 반환한다.
* `autoconnect()`를 사용해 뷰가 나타나면 자동으로 타이머가 시작된다.
* `onReceive`를 통해 매초 갱신되는 시간을 수신하고 `@State` 값을 업데이트한다.

---

## 예제 2. NotificationCenter와 함께 사용하기

앱 내부 또는 외부에서 발생하는 알림 이벤트를 처리할 수도 있다.

```swift
struct NotificationView: View {
    @State private var message = "대기 중"

    var body: some View {
        Text(message)
            .onReceive(NotificationCenter.default.publisher(for: Notification.Name("MyNotification"))) { _ in
                message = "알림 수신됨"
            }
    }
}
```

* `NotificationCenter` 역시 Combine을 통해 Publisher를 제공한다.
* 알림이 발생할 때마다 메시지가 갱신된다.

---

## 예제 3. @Published와 함께 사용하기

`ObservableObject`에서 발행되는 `@Published` 값도 `onReceive`로 처리할 수 있다.

```swift
class Counter: ObservableObject {
    @Published var count = 0
}

struct CounterView: View {
    @StateObject var counter = Counter()
    @State private var log = ""

    var body: some View {
        VStack {
            Text("Count: \(counter.count)")
            Button("증가") {
                counter.count += 1
            }
            Text(log)
        }
        .onReceive(counter.$count) { newValue in
            log = "Count가 \(newValue)로 변경됨"
        }
    }
}
```

* `counter.$count`는 `count`의 변화가 발생할 때마다 새로운 값을 내보내는 Publisher다.
* 이를 통해 상태가 바뀔 때마다 로깅하거나 추가 처리를 할 수 있다.

---

## onChange와의 차이

`SwiftUI`에는 상태 변화에 반응하는 또 다른 방법으로 `onChange`도 있다.

| 구분   | onReceive           | onChange            |
| ---- | ------------------- | ------------------- |
| 대상   | Combine의 Publisher  | 값 자체의 변경 감지         |
| 용도   | 외부 이벤트 수신           | 내부 상태 변화 감지         |
| 사용 예 | 타이머, 알림, @Published | @State, @Binding 변화 |

`onChange`는 SwiftUI 2.0 이후 도입된 문법이고, 내부 상태 감지에 더 간결하게 사용할 수 있다.

---

## 마무리

`onReceive`는 Combine 기반의 데이터 흐름을 SwiftUI View에서 직접 받아 처리할 수 있는 강력한 도구다. 주기적인 데이터, 알림 이벤트, 외부 상태 변경 등 다양한 상황에서 유용하게 쓰인다.

간단하지만 매우 강력한 이 기능을 잘 활용하면, 뷰의 복잡도를 줄이고 외부 상태에 유연하게 대응하는 앱을 만들 수 있다.

