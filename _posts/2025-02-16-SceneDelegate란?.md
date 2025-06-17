---
title: SceneDelegate란?
date: 2025-02-16 12:00:00 +0900
categories: [개념, UIKit]
tags: []
---



# SceneDelegate란?

iOS 13부터 도입된 객체로, **앱의 UI 생명주기를 관리**하는 역할을 한다.
기존에는 `AppDelegate`가 앱 전체의 생명주기를 관리했지만, **멀티 윈도우(예: iPad Safari)** 기능을 위해 UI 관련 생명주기를 `SceneDelegate`가 분리해 맡게 되었다.

---

## 주요 역할

* 앱의 **화면(UI) 생명주기** 관리
* \*\*여러 개의 윈도우(Scene)\*\*를 관리 (ex. iPad의 다중 창 지원)
* 씬이 실행될 때 **루트 뷰 컨트롤러 설정 등 초기 세팅** 수행

> 일반적인 iPhone 앱은 하나의 scene만 사용하지만, iPad나 멀티태스킹을 지원하는 앱은 여러 개의 scene을 가질 수 있다.

---

## 주요 생명주기 메서드

| 메서드                               | 호출 시점               | 설명                                                        |
| --------------------------------- | ------------------- | --------------------------------------------------------- |
| `scene(_:willConnectTo:options:)` | 앱 실행 시              | 새로운 scene이 생성되어 UI를 화면에 띄울 준비가 되었을 때. 루트 뷰 설정 등 초기화 작업 수행 |
| `sceneDidBecomeActive(_:)`        | 포그라운드 진입 후          | scene이 활성화되었을 때 (사용자 상호작용 가능 상태)                          |
| `sceneWillResignActive(_:)`       | 일시 중단 전             | scene이 비활성화되기 직전 (예: 전화 수신, 알림 등)                         |
| `sceneWillEnterForeground(_:)`    | 백그라운드 → 포그라운드 전환 직전 | UI 갱신 등의 준비 작업                                            |
| `sceneDidEnterBackground(_:)`     | 백그라운드 진입 시          | 데이터 저장, 작업 중단 등 처리 필요                                     |

---

## 예제 코드

```swift
import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    var window: UIWindow?

    func scene(_ scene: UIScene, 
               willConnectTo session: UISceneSession, 
               options connectionOptions: UIScene.ConnectionOptions) {
        // 루트 뷰 설정 등 초기 UI 구성
    }

    func sceneDidBecomeActive(_ scene: UIScene) {
        print("앱이 활성화됨")
    }

    func sceneWillResignActive(_ scene: UIScene) {
        print("앱이 비활성화됨")
    }

    func sceneDidEnterBackground(_ scene: UIScene) {
        print("앱이 백그라운드로 감")
    }

    func sceneWillEnterForeground(_ scene: UIScene) {
        print("앱이 포그라운드로 복귀됨")
    }
}
```

---

## 참고

* `SceneDelegate`는 **UI 생명주기 전용**
* `AppDelegate`는 앱 전체의 **비UI 생명주기** 담당 (예: 푸시 알림 등록, 백그라운드 작업 등)
* SwiftUI를 사용하는 경우, `@main`의 `App` 구조체에서 `Scene`을 직접 구성하므로 `SceneDelegate`를 사용하지 않을 수도 있음

---

