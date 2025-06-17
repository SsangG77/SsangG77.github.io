---
title: convert(_:to:) / convert(_:from:)
date: 2025-03-09 12:00:00 +0900
categories: [개념, Swift]
tags: [UIKit]
---


# 1. convert(_:to:)

<aside>

내 뷰의 좌표를 다른뷰 기준으로의 좌표값을 반환함.

</aside>

```swift
let newRect = myView.convert(myView.bounds, to: myView.superView)

//이 말은 myView.bounds가 myView.superView 기준으로의 상대적 좌표값을 반환한다는 뜻
```

# 2. convert(_:from:)

<aside>

다른뷰의 좌표를 내 뷰 기준으로의 좌표값을 반환함.

</aside>

```swift
let newRect = myView.convert(viewB.frame, from: viewA)
//viewA 기준으로의 viewB,frame을 myView기준으로의 좌표값을 반환
```

### - `UIWindow` 기준으로 뷰 위치 찾기

### 상황

- 특정 뷰가 `UIViewController` 안에 있는데
- **이 뷰의 위치를 `UIWindow` 기준으로 변환하여, 화면 전체에서 정확한 위치를 알고 싶음**

 예제 코드

```swift
if let window = UIApplication.shared.windows.first {
    let rectInWindow = window.convert(myView.frame, from: myView.superview)
    print("뷰의 위치 (윈도우 기준): \(rectInWindow)")
}
```

### 작동 원리

- `myView.frame`은 `superview` 기준
- `window.convert(_:from:)`을 사용하여 **화면 전체(`UIWindow`) 기준으로 변환**
- 팝업, 애니메이션, 드래그 이벤트 등에서 유용하게 활용 가능
