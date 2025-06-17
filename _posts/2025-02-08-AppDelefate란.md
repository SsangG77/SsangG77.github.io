---
title: AppDelefate란
date: 2025-02-08 12:00:00 +0900
categories: [개념, UIKit]
tags: []
---


ios 앱의 생명 주기를 관리하는 객체.

앱이 시작, 종료, 백그라운드 전환될 때 등 이벤트를 처리함.

## 생명주기란

---

iOS 앱은 실행 중 다음과 같은 상태(State)를 거칩니다

1.  **Not Running (실행되지 않음)**
    - 앱이 완전히 종료된 상태 (앱이 실행되지 않았거나 강제 종료됨)
    
2. **Inactive (비활성)**
    - 앱이 실행 중이지만 **사용자의 입력을 받지 않는 상태**
    - 예: 전화 수신, 알림창이 떠 있는 경우
    
3. **Active (활성)**
    - 앱이 **포그라운드에서 실행 중이며 사용자 입력을 받을 수 있는 상태**
    - 일반적으로 **메인 화면에서 사용자가 앱을 조작할 때 해당됨**
    
4. **Background (백그라운드)**
    - 앱이 **화면에 보이지 않지만 실행 중인 상태**
    - 예: 음악 앱이 배경에서 음악을 재생할 때
    - 대부분의 앱은 **일정 시간 후 Suspended 상태로 전환됨**
    
5. **Suspended (일시 정지)**
    - 백그라운드에 있지만 **더 이상 CPU를 사용하지 않는 상태**
    - 시스템 메모리가 부족하면 앱이 이 상태에서 종료될 수도 있음

## 예시 코드

---

```swift
import UIKit

@main
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        print("앱이 시작됨!")
        return true
    }

    func applicationDidEnterBackground(_ application: UIApplication) {
        print("앱이 백그라운드로 전환됨!")
    }

    func applicationWillEnterForeground(_ application: UIApplication) {
        print("앱이 다시 포그라운드로 옴!")
    }

    func applicationWillTerminate(_ application: UIApplication) {
        print("앱이 종료됨!")
    }
}

```

