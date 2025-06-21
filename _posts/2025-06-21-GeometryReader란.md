---
title: GeometryReader란
date: 2025-06-21 12:00:00 +0900
categories: [개념, Swift]
tags: [View, container]
---


## SwiftUI - GeometryReader란?

### 개요

`GeometryReader`는 SwiftUI에서 **상위 뷰(부모 뷰)의 크기와 위치 정보를 하위 뷰에 전달**해주는 컨테이너 뷰입니다.
즉, 부모의 `frame`, `safeArea`, `좌표계` 등을 하위 뷰에서 참조할 수 있게 해줍니다.

---

### 기본 사용법

```swift
GeometryReader { geometry in
    Text("Width: \(geometry.size.width)")
}
```

* `geometry`는 `GeometryProxy` 타입입니다.
* `geometry.size` → 부모 뷰의 크기
* `geometry.frame(in:)` → 지정된 좌표계에서의 프레임 정보

---

### 핵심 속성

| 속성                            | 설명              |
| ----------------------------- | --------------- |
| `geometry.size`               | 부모 뷰의 너비와 높이    |
| `geometry.safeAreaInsets`     | Safe Area 여백    |
| `geometry.frame(in: .global)` | 전역 좌표계 기준 위치/크기 |
| `geometry.frame(in: .local)`  | 로컬 좌표계 기준 위치/크기 |

---

### 사용 예시: 외부 크기 기반으로 내부 뷰 크기 설정

```swift
struct ResponsiveView: View {
    var body: some View {
        GeometryReader { geometry in
            Rectangle()
                .fill(Color.blue)
                .frame(width: geometry.size.width * 0.8,
                       height: geometry.size.height * 0.3)
        }
    }
}
```

* 부모 뷰의 크기에 비례한 UI를 만들 때 유용합니다.
* 반응형 디자인 구현에 필수적입니다.

---

### 주의점

1. **GeometryReader는 가능한 모든 공간을 차지하려고 함**
   → 내부 뷰가 원치 않게 화면 전체를 채울 수 있음
   → 해결: `.frame(...)`으로 제한하거나 `.background(...)` 등에 국한시켜 사용

2. **레이아웃 우선순위에 영향 줌**
   → 내부 뷰가 예상보다 크거나 작게 나올 수 있음

---

### 실제 적용 예: 부모 뷰의 `.frame(width:)` 전달받기

```swift
struct OuterView: View {
    var body: some View {
        GeometryReader { proxy in
            let width = proxy.size.width
            VStack {
                Text("부모 너비: \(Int(width))")
                RoundedRectangle(cornerRadius: 10)
                    .fill(Color.green)
                    .frame(width: width * 0.8)
            }
        }
        .frame(width: 300, height: 200)
    }
}
```

---

## 결론

`GeometryReader`는 **부모 뷰의 레이아웃 정보를 자식 뷰에 전달**하는 도구입니다.
동적 크기 계산, 반응형 UI, 위치 기반 애니메이션 등을 구현할 때 매우 유용하지만,
의도치 않게 레이아웃 전체를 확장시킬 수 있으므로 **주의해서 제한된 범위에 적용**하는 것이 중요합니다.
