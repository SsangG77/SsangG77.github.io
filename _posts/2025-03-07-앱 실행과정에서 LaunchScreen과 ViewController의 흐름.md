---
title: 
date: 2025-03-07 12:00:00 +0900
categories: [개념, Swift]
tags: [ViewController]
---

1. **앱 실행 → `LaunchScreen.storyboard` 표시**
    - `LaunchScreen.storyboard`의 레이아웃이 표시되지만, **코드는 실행되지 않음**.
    - 앱이 실행되면서 시스템이 `LaunchScreen`을 먼저 보여주는 이유는 **앱이 로드될 동안 부드러운 사용자 경험(UX)을 제공**하기 위함.
2. **앱의 메인 진입점(`rootViewController`)을 로드**
    - `SceneDelegate.swift` (iOS 13+) 또는 `AppDelegate.swift` (iOS 12 이하)에서 `rootViewController`가 설정됨.
    - `rootViewController`는 `LoginViewController` 또는 `MainViewController` 등 사용자가 지정한 첫 번째 화면.
3. **`LaunchScreen` → `rootViewController`로 전환**
    - `LaunchScreen`이 사라지고, `rootViewController`가 나타남.
    - `rootViewController.viewDidLoad()`가 실행되면서 UI가 준비됨.

---

## **LaunchScreen과 ViewController의 차이**

| **특징** | **LaunchScreen.storyboard** | **첫 번째 ViewController (예: LoginViewController)** |
| --- | --- | --- |
| **역할** | 앱 로딩 중 사용자 경험 제공 | 실제 앱 화면을 표시하고 기능 제공 |
| **코드 실행 가능 여부** | ❌ 불가능 | ✅ 가능 (`viewDidLoad()`, `viewWillAppear()` 등) |
| **설정 방식** | 스토리보드에서만 설정 가능 | 코드 및 스토리보드 둘 다 가능 |
| **전환 방식** | 시스템이 자동으로 `rootViewController`로 전환 | 직접 네비게이션 가능 (e.g., `pushViewController`) |

---

## **`LaunchScreen`에서 코드 실행이 불가능한 이유**

- `LaunchScreen`은 **iOS 시스템이 직접 렌더링**하므로, 코드 실행이 불가능.
- 커스텀 애니메이션이나 네트워크 요청 같은 작업은 **앱의 첫 번째 `UIViewController`에서 처리**해야 함.

### **해결 방법 (LaunchScreen에서 애니메이션 효과를 주고 싶다면?)**

1. **LaunchScreen을 그대로 유지**
2. **앱의 첫 번째 `UIViewController`에서 페이드인 애니메이션 적용**

```swift
class LoginViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
    }

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)

        // LaunchScreen과 유사한 로딩 화면을 뷰에 추가
        let launchView = UIImageView(frame: view.bounds)
        launchView.image = UIImage(named: "launch_screen_image") // 런치 스크린과 같은 이미지
        view.addSubview(launchView)

        // 0.5초 후 페이드아웃 애니메이션 적용
        UIView.animate(withDuration: 0.5, animations: {
            launchView.alpha = 0
        }) { _ in
            launchView.removeFromSuperview()
        }
    }
}

```

**결과:**

- `LaunchScreen.storyboard`에서 표시되던 화면이 부드럽게 첫 번째 `ViewController`로 전환됨.
- `LaunchScreen`과 일치하는 이미지를 `UIImageView`로 잠시 표시한 후 페이드아웃 효과 적용.

---

## **결론**

- `LaunchScreen`은 앱이 실행될 때 자동으로 표시되며, 시스템이 직접 관리하므로 **코드 실행이 불가능**.
- 실제 앱 화면(첫 번째 `UIViewController`)은 `LaunchScreen`이 사라진 후에 `rootViewController`로 나타남.
- `LaunchScreen`과 동일한 UI 전환 효과를 원하면, 첫 번째 `UIViewController`에서 애니메이션을 적용해야 함.
