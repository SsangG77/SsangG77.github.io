---
title: 
date: 2025-03-16 12:00:00 +0900
categories: [코딩테스트]
tags: []
---


애플의 인증 관련 프레임워크

- OAuth 2.0, Sign in with Apple, 패스키 등 인증 관련 기능을 제공
- `ASWebAuthenticationSession`이 포함되어 있음.

## ASWebAuthenticationSession

- 앱 내에서 OAuth 인증을 수행하는 클래스
- 웹브라우저 기반으로 로그인 처리
- 로그인 후 앱으로 돌아올 수 있음

```swift
let session = ASWebAuthenticationSession(
    url: notionAuthURL, //웹브라우저를 통해 띄울 url
    callbackURLScheme: "yourapp"//인증후 돌아올 앱스키마 or redirect url
) { callbackURL, error in
    if let callbackURL = callbackURL { //콜백 성공시 로직 수행
        print("Redirected URL:", callbackURL)
    }
}
session.start() // 세션 시작

```
