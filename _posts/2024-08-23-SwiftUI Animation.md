---
title: SwiftUI Animation
date: 2024-08-23 12:00:00 +0900
categories: [개념, SwiftUI]
tags: [Animation]
---

## 애니메이션 적용하기


> 변경된 값을 기반으로 View의 변경 사항을 애니메이션으로 생성할 수 있다.
이렇게 하려면 애니메이션을 생성하려는 View에 .animation(_:value:) 제어자를 추가하고 변경 사항을 모니터링할 값과 Animation을 선택한다.
> 

```swift
@State var isOn = false

Circle()
   .scaleEffect( isOn ? 1.0 : 0.75 )
	 .animation(.default, value: isOn)
```



