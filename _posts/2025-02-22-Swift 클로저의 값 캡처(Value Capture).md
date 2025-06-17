---
title: Swift 클로저의 값 캡처(Value Capture)
date: 2025-02-22 12:00:00 +0900
categories: [개념, Swift]
tags: [클로저, 메모리]
---



아래는 Swift의 **클로저 값 캡처**, **메모리 누수 디버깅**, 그리고 **SwiftUI에서의 클로저 캡처**까지 모두 포함한 실전 중심의 정리다. 복붙 가능한 마크다운 형식이다.

---


# Swift 클로저의 값 캡처(Value Capture)와 메모리 누수, SwiftUI에서의 캡처 정리

## 1. 값 캡처란?

Swift에서 클로저는 **정의 당시 주변 변수의 값을 캡처**한다. 이 덕분에 클로저는 자신이 선언된 환경을 "기억"할 수 있다.

```swift
func makeCounter() -> () -> Int {
    var count = 0
    return {
        count += 1
        return count
    }
}

let counter = makeCounter()
print(counter()) // 1
print(counter()) // 2
````

* `count`는 `makeCounter` 함수가 끝나도 계속 살아있음
* 클로저가 변수 자체를 **참조 형태로 캡처**했기 때문

---

## 2. 캡처 리스트(Capture List)

값을 캡처 시점에 복사하고 싶을 때 사용

```swift
var x = 10
let closure = { [x] in
    print(x)
}
x = 20
closure() // 10
```

* `[x]`는 캡처 시점의 값을 **복사**
* 이후 외부 x가 변경돼도 클로저 내부에는 영향 없음

---

## 3. 메모리 누수(Retain Cycle)와 디버깅

### 클로저가 `self`를 강하게 참조하면 순환 참조 발생

```swift
class SomeClass {
    var text = "hello"

    lazy var closure: () -> Void = {
        print(self.text) // strong capture
    }
}
```

* `SomeClass`는 `closure`를 소유하고
* `closure`는 `self`를 강하게 참조하므로 **서로 해제가 안 됨**

### 해결 방법: `[weak self]` 또는 `[unowned self]`

```swift
lazy var closure: () -> Void = { [weak self] in
    print(self?.text ?? "")
}
```

* `weak`: 옵셔널, self가 사라지면 nil
* `unowned`: 비옵셔널, self가 항상 살아있다고 가정

### 누수 디버깅 방법

1. **Xcode의 Memory Graph** 사용

   * 실행 후 `Debug Memory Graph` 클릭
   * 해제되지 않은 인스턴스 추적 가능

2. **deinit 로그 확인**

```swift
deinit {
    print("SomeClass deinitialized")
}
```

* 특정 인스턴스가 해제되는지 직접 확인 가능

3. **Instruments의 Leaks 툴 사용**

   * 누수된 객체 추적 가능

---

## 4. SwiftUI에서의 클로저 캡처

SwiftUI는 `@State`, `@ObservedObject`, `@EnvironmentObject` 등 **데이터 상태를 관찰**하면서 UI를 업데이트한다. 이 과정에서 클로저 캡처 주의 필요.

### 문제 예시

```swift
struct MyView: View {
    @State private var count = 0

    var body: some View {
        Button("Increment") {
            print(self.count)
            count += 1
        }
    }
}
```

* SwiftUI는 `View`를 자주 재생성하며, 클로저 내부에서 `self`를 직접 캡처하면 예기치 않은 동작 가능성 존재

### 좋은 예시: 캡처 최소화

```swift
Button("Increment") {
    count += 1
}
```

* `self` 생략 가능하면 생략
* 복잡한 비즈니스 로직은 ViewModel로 분리

### ViewModel에서 클로저 캡처 시 주의

```swift
class MyViewModel: ObservableObject {
    @Published var result = ""

    func load() {
        someAsyncTask { [weak self] output in
            self?.result = output
        }
    }
}
```

* `weak self` 사용으로 ViewModel과 클로저 간 순환 참조 방지

---

## 5. 요약

| 항목          | 설명                                           |
| ----------- | -------------------------------------------- |
| 변수 캡처       | 클로저는 외부 변수의 값을 참조 형태로 캡처                     |
| 값 복사        | `[x]`처럼 캡처 리스트로 시점 고정 가능                     |
| strong self | 클로저와 객체 간 순환 참조 유발 가능                        |
| weak self   | 메모리 누수 방지에 필수                                |
| SwiftUI     | 클로저 내 `self` 사용 최소화, ViewModel 분리 권장         |
| 디버깅 방법      | `deinit`, Xcode Memory Graph, Instruments 활용 |

---

**참고 팁**

* 클로저 내부에서 `[weak self]`는 습관처럼 사용하되,
  self가 확실히 살아있음을 보장할 수 있을 때만 `unowned self` 사용
* SwiftUI의 버튼, `.onAppear`, `.task` 클로저 등에서도 항상 `self` 캡처에 주의

