---
title: Custom URL Scheme
date: 2025-03-17 12:00:00 +0900
categories: [개념, Swift]
tags: []
---

앱의 특정 URL을 통해 실행될 수 있도록 하는 기능
웹 페이지, 다른 앱에서 URL을 호출하면 해당 URL을 처리할 수 있는 앱이 자동으로 열림.

<br>

## 1. 앱 스키마 구조


```bash
myapp://auth/callback?success=true
```

- `myapp://` : 앱에서 설정한 Custom URL Scheme
- `auth/callback` : 특정 화면에서 처리하기 위한 경로 설정
- `?success=true` : 추가로 전달할 데이터 (선택사항)

<br>

## 2. 앱 스키마 사용방법

- 프로젝트 `Info.plist`에 추가
    - URL Type 섹션 추가
    - `URL Identifier`: 앱 번들 ID 입력 (예: `com.example.myphotoapp`)
    - `URL Schemes`: 사용할 Scheme 입력 (예: `myphotoapp`)
    - **역할(Role) 선택**
        - `Editor`: 내가 정의한 Custom URL Scheme
        - `Viewer`: 내가 정의하지 않은 Scheme을 지원하는 경우
    
    ![Image](https://github.com/user-attachments/assets/8f891601-f71c-4ddf-bbe4-f389be496c47)
    

- AppDelegate에서 URL 처리 (iOS 13 이하)

<aside>

앱이 URL Scheme를 통해 실행되면, 시스템이 AppDelegate의 application(_:open:options:) 메서드를 호출합니다.

</aside>

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

    if url.scheme == "myapp", url.host == "auth", url.path == "/callback" {
        print("Received URL: \(url.absoluteString)")
        
        // 쿼리 파라미터 확인 (예: ?success=true)
        if let components = URLComponents(url: url, resolvingAgainstBaseURL: false),
           let queryItems = components.queryItems {
            for item in queryItems {
                print("\(item.name) = \(item.value ?? "")")
            }
        }
        
        // ✅ 웹뷰 닫고 인증 완료 처리
        NotificationCenter.default.post(name: Notification.Name("AuthCompleted"), object: nil)
        
        return true
    }
    return false
}

```

- url 확인하는 부분에서 리다이렉트 URL과 일치시켜줘야함. (예 : myapp://auth/callback?success=true)


- SceneDelegate에서 처리

```swift
func scene(_ scene: UIScene, 
           willConnectTo session: UISceneSession, 
           options connectionOptions: UIScene.ConnectionOptions) {

    // URL을 통해 실행된 경우 처리
    if let urlContext = connectionOptions.urlContexts.first {
        let sendingAppID = urlContext.options.sourceApplication
        let url = urlContext.url
        print("URL을 보낸 앱: \(sendingAppID ?? "Unknown")")
        print("실행된 URL: \(url)")
    }
}

```

<aside>

`scene(_:willConnectTo:options:)` → 앱이 실행되지 않은 상태에서 URL을 감지할 때 호출

`connectionOptions.urlContexts.first` → URL 정보를 가져와 분석

</aside>
