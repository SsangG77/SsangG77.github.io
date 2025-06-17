---
title: present(_:animated:completion:)란?
date: 2025-03-22 12:00:00 +0900
categories: [개념, Swift]
tags: [UIKit]
---

`present`는 **UIViewController에서 다른 UIViewController를 표시하는 메서드**로, 현재 화면에 새로운 뷰 컨트롤러를 모달 방식으로 표시할 때 사용됨.

---

## 1. 기본 문법

```swift
func present(_ viewControllerToPresent: UIViewController,
             animated flag: Bool,
             completion: (() -> Void)? = nil)

```

### 매개변수 설명

- `viewControllerToPresent`: 표시할 뷰 컨트롤러 객체 (`UIViewController` 타입)
- `animated`: 화면 전환 애니메이션 사용 여부 (`true`: 애니메이션 적용, `false`: 즉시 표시)
- `completion`: 화면 전환이 완료된 후 실행할 클로저 (옵셔널, 기본값 `nil`)

---

## 2. 사용 예제

### 예제 1: 기본적인 `present` 사용

```swift
let newVC = NewViewController()
present(newVC, animated: true, completion: nil)

```

- `newVC`를 현재 뷰 컨트롤러에서 모달 방식으로 표시함.
- `animated: true`로 설정하면 화면 전환 애니메이션이 적용됨.

### 예제 2: 네비게이션 컨트롤러와 함께 사용

```swift
let newVC = NewViewController()
let navController = UINavigationController(rootViewController: newVC)
present(navController, animated: true, completion: nil)

```

- `UINavigationController`를 포함한 형태로 `present`하면, 모달 화면 내에서 네비게이션 기능을 사용할 수 있음.

### 예제 3: `completion` 클로저 활용

```swift
let newVC = NewViewController()
present(newVC, animated: true) {
    print("화면 전환 완료")
}

```

- 화면 전환이 끝난 후 추가 작업을 수행할 때 사용.

---

## 3. `push`와 `present`의 차이

| 구분 | `present` | `push` |
| --- | --- | --- |
| 사용 방식 | 모달 방식 | 네비게이션 스택 방식 |
| 필요 조건 | `UIViewController`에서 직접 호출 가능 | `UINavigationController` 내부에서만 사용 가능 |
| 화면 이동 | 독립적인 새로운 화면 | 이전 화면이 스택에 남아 있음 |
| 사용 예 | 로그인 화면, 설정 화면 | 앱 내 페이지 이동 |

**예제: `pushViewController`와 비교**

```swift
// present 방식 (모달)
let newVC = NewViewController()
present(newVC, animated: true, completion: nil)

// push 방식 (네비게이션)
let newVC = NewViewController()
navigationController?.pushViewController(newVC, animated: true)
```

- `present`는 `UIViewController`에서 바로 호출 가능하지만,
- `pushViewController`는 `UINavigationController` 내에서만 동작함.

---

## 4. `present`를 사용할 수 없는 경우

- `UIView`, `NSObject` 등 `UIViewController`가 아닌 클래스에서 `present` 호출 시 **"Cannot find 'present' in scope"** 오류 발생.
- 해결 방법:
    - `UIViewController` 내부에서 호출하거나,
    - `UIViewController`를 `weak var`로 참조하여 `present`를 호출해야 함.

---

### 결론

- `present`는 현재 뷰 컨트롤러에서 **새로운 화면을 모달 방식으로 표시할 때 사용**.
- 네비게이션이 필요한 경우 `UINavigationController`를 포함한 상태에서 `present` 가능.
- `UIViewController` 외부에서는 `present` 호출이 불가능하므로, `UIViewController` 참조를 받아야 함.
