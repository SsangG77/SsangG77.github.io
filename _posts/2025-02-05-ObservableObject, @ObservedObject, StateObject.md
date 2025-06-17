---
title: ObservableObject, @ObservedObject, StateObject
date: 2025-02-05 12:00:00 +0900
categories: [개념, Swift, Combine]
tags: [Combine]
---

# ObservableObject

- `Combine`에서 `ObservableObject`를 사용하는 이유는 해당 프로토콜을 채택한 객체의 상태 변화를 감지하고 UI에 즉시 실시간으로 반영하기 위해서 입니다.

```swift
class ViewModel: ObservableObject {
	@Published var count = 0
}
```

<aside>

`@Published` 
- 해당 키워드를 사용한 프로퍼티가 변경되면 `Combine`을 통해 이벤트가 방출됨.

```swift
class ViewModel: ObservableObject {
    @Published var count = 0  // 변경 감지됨
    var text = "Hello"        // 변경 감지 안됨
}

let vm = ViewModel()
vm.$count.sink { print("Count changed: \($0)") }  // sink로 방출 감지(구독)

vm.count = 1  // 출력: "Count changed: 1"
vm.text = "Hi"  // 아무 반응 없음

```

</aside>

---

# @ObservedObject

- `ObservableObject`객체가 부모에서 주입되었을때 사용하는 프로퍼티 래퍼이다.
- `ObservableObject`객체가 포함되어있는 뷰에서 `@Published`프로퍼티가 변경될때 UI를 자동 갱신한다.
- 뷰의

```swift
struct ParentView: View {
    @StateObject private var viewModel = ViewModel() // ✅ 부모에서 생성

    var body: some View {
        ChildView(viewModel: viewModel) // ✅ 자식 뷰에 주입
    }
}
```

```swift
struct ChildView: View {
    @ObservedObject var viewModel: ViewModel // ✅ 부모에서 받은 객체 감지

    var body: some View {
        VStack {
            Text("Count: \(viewModel.count)")
            Button("Increment") {
                viewModel.count += 1
            }
        }
    }
}
```

---

# StateObject

- `@StateObject`는 SwiftUI에서 **뷰가 메모리에서 해제되지 않는 한 `ObservableObject`를 채택한 객체의 인스턴스를 유지**하는 프로퍼티 래퍼입니다.

```swift
class ViewModel: ObservableObject {
    @Published var count = 0
}

struct ContentView: View {
    @StateObject private var viewModel = ViewModel() // ✅ StateObject 사용

    var body: some View {
        VStack {
            Text("Count: \(viewModel.count)")
            Button("Increment") {
                viewModel.count += 1
            }
            NavigationLink("Go to Next", destination: NextView())
        }
    }
}
```

- 인스턴스가 부모를 통해 주입되는 것이 아닌 직접 생성해서 사용할 때 해당 래퍼를 사용함.
