---
title: viewDidLayoutSubviews
date: 2025-03-09 12:00:00 +0900
categories: [개념, UIKit]
tags: []
---

viewDidLayoutSubviews는 iOS의 UIViewController 생명주기 메서드 중 하나로, 뷰의 서브뷰들이 레이아웃된 직후 호출된다. 주로 레이아웃 조정이 필요한 작업을 이 시점에 처리한다.

# 정의

```swift
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    
    // 레이아웃 관련 작업
}
```


# 호출 시점

**viewDidLoad → viewWillLayoutSubviews → viewDidLayoutSubviews**

오토레이아웃이나 frame 수정을 수반하는 변화가 생기면 자동으로 다시 호출됨
화면 회전, 뷰 크기 변경 등에서도 반복 호출됨

# 주요 사용 예시

## 1. 서브뷰의 frame 직접 조정
```swift
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    button.frame = CGRect(x: 20, y: 100, width: view.bounds.width - 40, height: 50)
}
```

오토레이아웃을 쓰지 않고 frame 기반 레이아웃을 할 경우 이 시점이 적절하다.


## 2. 동적 레이아웃 조정
컬렉션뷰 셀 사이즈 갱신
스크롤뷰 컨텐츠 크기 갱신
서브뷰 위치/크기 재조정

### ⚠️ 주의할 점
불필요한 레이아웃 변경은 피해야 함
viewDidLayoutSubviews는 자주 호출되므로, 무의미한 frame 변경은 성능 저하 유발 가능

내부에서 layoutIfNeeded() 호출 → 무한 루프 위험

```swift
// ❌ 잘못된 예시
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    view.setNeedsLayout()
    view.layoutIfNeeded()  // 무한 루프 유발 가능
}
```
