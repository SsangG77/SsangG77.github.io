---
title: Cannot find 'present' in scope 오류 발생
date: 2025-03-14 12:00:00 +0900
categories: [프로젝트, 트러블 슈팅]
tags: [Notion-Mind]
---

## 1. 문제 발생 상황

`present(_:animated:completion:)` 메서드를 호출했을 때 **"Cannot find 'present' in scope"** 오류가 발생함.

## 2. 원인 분석

### 2.1 `present`는 `UIViewController`의 인스턴스 메서드

- `present` 메서드는 `UIViewController` 내부에서만 호출 가능함.
- `UIView`, `NSObject`, 또는 다른 클래스에서 `present`를 직접 호출하면 오류 발생.

### 2.2 `handleTap()` 메서드가 `UIViewController` 내부에 없음

- `handleTap()`이 `UIView` 또는 다른 클래스에서 정의되어 있을 가능성이 높음.
- `UIView`는 `present` 메서드를 제공하지 않으므로 직접 호출할 수 없음.

## 3. 해결 방법

### 방법 1: `UIViewController` 내부에서 호출

`handleTap()`이 `UIViewController` 내부에 있다면, 아래처럼 수정하면 정상적으로 동작함.

```swift
class MyViewController: UIViewController {
    @objc func handleTap() {
        let nodeDetailVC = NodeDetailViewController()
        let navController = UINavigationController(rootViewController: nodeDetailVC)
        navController.modalPresentationStyle = .fullScreen
        present(navController, animated: true, completion: nil) // 오류 해결
    }
}
```

### 방법 2: `UIView`에서 `UIViewController` 참조를 받아 호출

- `UIView`에서 `present`를 호출하려면, `UIViewController`를 참조해야 함.
- `UIViewController`의 인스턴스를 `UIView`에 전달하고, 이를 통해 `present` 호출.

```swift
class CustomView: UIView {
    weak var viewController: UIViewController? // 뷰 컨트롤러 참조

    @objc func handleTap() {
        let nodeDetailVC = NodeDetailViewController()
        let navController = UINavigationController(rootViewController: nodeDetailVC)
        navController.modalPresentationStyle = .fullScreen
        viewController?.present(navController, animated: true, completion: nil) // 오류 해결
    }
}

```

### 방법 3: `UIViewController`에서 `UIView`에 참조 설정

- `UIViewController`에서 `UIView`의 `viewController` 속성을 설정하여, `UIView`가 `present`를 호출할 수 있도록 함.

```swift
class MyViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        let customView = CustomView()
        customView.viewController = self // 뷰 컨트롤러 참조 전달
        view.addSubview(customView)
    }
}

```

## 4. 결론

- `present`는 `UIViewController`에서만 호출 가능하므로, `UIView`에서 호출할 경우 `UIViewController` 참조를 받아야 함.
- `UIViewController`에서 실행되는지 확인 후, `UIView`에서 호출할 경우 `viewController?.present(...)` 방식으로 해결 가능.
