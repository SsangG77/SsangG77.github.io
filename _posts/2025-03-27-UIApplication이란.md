---
title: UIApplication이란
date: 2025-03-27 12:00:00 +0900
categories: [개념, Swift]
tags: [UIKit]
---

# 개요

iOS 앱을 개발하다 보면 앱 전체의 상태를 관리하고 시스템 이벤트에 반응하는 기능이 필요하다. 이 중심에 있는 객체가 바로 UIApplication이다.  
이 글에서는 UIApplication이 어떤 역할을 하고, 어떻게 사용하는지 핵심만 정리한다.  

<br>

# UIApplication이란?

UIApplication은 앱의 전반적인 상태를 제어하고 시스템 이벤트를 처리하는 중심 객체다.  
앱 내에 단 하나만 존재하는 싱글톤(Singleton) 객체이며, UIApplication.shared를 통해 접근한다.  
앱의 실행 상태, 푸시 알림 등록, 외부 URL 열기, 윈도우 제어 등 거의 모든 핵심 기능을 담당한다.  

<br>

# 주요 기능

## 1. 앱 상태 추적
앱이 현재 활성 상태인지, 백그라운드에 있는지, 비활성화 상태인지를 파악할 수 있다.


```swift
let appState = UIApplication.shared.applicationState
switch appState {
case .active:
    print("App is active")
case .background:
    print("App is in background")
case .inactive:
    print("App is inactive")
}
```

<br>


## 2. 외부 URL 열기
웹사이트, 설정 앱 등 외부 리소스를 여는 기능을 제공한다.

```swift

if let url = URL(string: "https://apple.com") {
    UIApplication.shared.open(url, options: [:], completionHandler: nil)
}
```

<br>

## 3. 원격 알림 등록
푸시 알림을 받기 위해 APNs 등록을 요청할 때 사용한다.

```swift
UIApplication.shared.registerForRemoteNotifications()
```


<br>

## 4. 상태 바 제어
상태 바의 표시 여부, 스타일 등을 조절할 수 있다. (단, 대부분의 제어는 UIViewController에서 override)


<br>


## 5. 키 윈도우 및 윈도우 리스트 관리
앱 내에서 현재 활성화된 윈도우(키 윈도우)나 전체 윈도우에 접근 가능하다.

```swift
let keyWindow = UIApplication.shared.windows.first { $0.isKeyWindow }
```

<br>

## 참고 사항
SwiftUI에서는 UIApplication을 직접 다룰 일이 줄어들긴 했지만, 시스템 기능을 사용할 때 여전히 필요하다.
앱 생명 주기(lifecycle)에 관련된 처리를 할 때는 UIApplicationDelegate와 함께 사용하는 경우가 많다.

<br>


## 마무리
UIApplication은 단순한 헬퍼 클래스가 아니다. 앱 전체의 동작 흐름을 제어하는 핵심 객체다. 앱의 상태를 파악하거나 시스템 기능을 연동할 일이 있다면, UIApplication의 역할을 반드시 이해하고 있어야 한다.


