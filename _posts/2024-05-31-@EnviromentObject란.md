---
title: EnviromentObject란
date: 2024-05-31 12:00:00 +0900
categories: [개념, SwiftUI]
tags: []
---


# SwiftUI의 `@EnvironmentObject`란?

SwiftUI는 상태 관리를 위한 여러 도구를 제공한다. 그중에서도 `@EnvironmentObject`는 **전역 상태 공유**를 위해 사용되는 강력한 속성 래퍼다. View 간 데이터 전달에서 발생하는 복잡함을 줄여주며, 특히 앱의 다양한 화면에서 공통적으로 사용하는 모델을 관리할 때 유용하다.

---

## 1. `@EnvironmentObject`의 정의

`@EnvironmentObject`는 SwiftUI의 환경(environment)에 등록된 **공통 ObservableObject 인스턴스**에 접근하는 속성 래퍼다.

* View 간 데이터 전달 시 `@ObservedObject`나 `@StateObject`처럼 직접 전달하지 않아도 됨
* 대신 환경에 등록해두고 필요한 View에서 꺼내 사용

### 선언 예시

```swift
final class UserSettings: ObservableObject {
    @Published var nickname: String = "차상진"
}
```

```swift
struct ProfileView: View {
    @EnvironmentObject var settings: UserSettings
    
    var body: some View {
        Text("닉네임: \(settings.nickname)")
    }
}
```

---

## 2. 사용하는 이유

* **전역 데이터 공유**: 앱 전체 또는 여러 화면에서 공통 상태가 필요할 때
* **직접 전달을 생략**: 하위 뷰에 일일이 객체를 넘기지 않아도 됨
* **상태 자동 반영**: ObservableObject의 `@Published` 속성이 변경되면 View가 자동 갱신됨

---

## 3. 사용 방법 요약

### 1) ObservableObject 정의

```swift
final class AppState: ObservableObject {
    @Published var isLoggedIn: Bool = false
}
```

### 2) 환경에 객체 주입

최상위 뷰에 `.environmentObject(_:)`를 통해 주입

```swift
@main
struct MyApp: App {
    var state = AppState()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(state)
        }
    }
}
```

### 3) 필요한 View에서 꺼내 사용

```swift
struct ContentView: View {
    @EnvironmentObject var state: AppState
    
    var body: some View {
        VStack {
            Text(state.isLoggedIn ? "로그인됨" : "로그아웃 상태")
            Button("로그인 토글") {
                state.isLoggedIn.toggle()
            }
        }
    }
}
```

---

## 4. 주의할 점

* `@EnvironmentObject`는 **환경에 주입되지 않으면 앱이 크래시**된다
  → 런타임에 주입된 객체가 없으면 오류 발생
* 단위 테스트나 Preview에서 직접 주입 필요

```swift
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
            .environmentObject(AppState())
    }
}
```

---

## 5. 언제 사용해야 할까?

| 상황                 | 적합한 속성                 |
| ------------------ | ---------------------- |
| 상태가 로컬(뷰 내부 전용)    | `@State`               |
| 한 뷰에서만 사용하는 외부 객체  | `@ObservedObject`      |
| 하위 뷰에서 생성/관리하는 객체  | `@StateObject`         |
| 여러 뷰에 공통으로 사용하는 객체 | `@EnvironmentObject` ✅ |

---

## 마무리

`@EnvironmentObject`는 앱 전체에서 상태를 공유할 수 있게 해주는 유용한 도구다.
하지만 무분별하게 사용하면 **데이터 흐름이 불투명**해지고 **의존성이 숨겨지는 단점**도 있다.
명확한 목적을 가지고, 앱의 전역 상태 또는 공통 데이터 모델이 필요할 때만 사용하는 것이 좋다.





