---
title: NotificationCenger에 대해서 알아보기
date: 2025-12-09 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, Notification]
---


### NotificationCenter가 필요한 이유
- 객체끼리 직접 참조하여 콜백을 보내면 강한 결합이 생김.
- NotificationCenter에서는 발신자, 수신자가 서로 몰라도 통신 가능

### 특징
- 특정 데이터 변경 시 여러 뷰에서 동시 업데이트 가능

---

# 1. 기본 개념
### 1. NotificationCenter.default
가장 많이 쓰는 기본 NotificationCenter 인스턴스
```
NotificationCenter.default
```

### 2. Notification 이름 정의
보통 extension 사용:
```
extension Notification.Name {
    static let userDidLogin = Notification.Name("userDidLogin")
}
```

### 3. 알림 보내기(post)
```
NotificationCenter.default.post(
    name: .userDidLogin,
    object: nil,
    userInfo: ["userId": 123]
)
```
name: 어떤 알림인지 구분용
object: 보낸 객체 (필요 없으면 nil)
userInfo: 부가 데이터 딕셔너리

### 4. 알림 받기(addObserver)
SwiftUI / Combine 없이 UIKit 방식
```
NotificationCenter.default.addObserver(
    self,
    selector: #selector(handleLogin),
    name: .userDidLogin,
    object: nil
)

@objc func handleLogin(_ notification: Notification) {
    print("로그인됨:", notification.userInfo?["userId"] ?? "")
}
```

### 5. 옵저버 제거(removeObserver)
iOS 9 이후엔 deinit에서 자동 제거 BUT
```
selector 기반 addObserver는 직접 제거하는 게 안전함.
deinit {
    NotificationCenter.default.removeObserver(self)
}
```

---


# SwiftUI에서

NotificationCenter 대신 보통 Combine으로 처리:
```
.onReceive(NotificationCenter.default.publisher(for: .userDidLogin)) { notification in
    print("SwiftUI에서 받음!")
}
```






