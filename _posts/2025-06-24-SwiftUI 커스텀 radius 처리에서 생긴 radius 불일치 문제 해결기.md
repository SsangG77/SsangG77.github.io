---
title: SwiftUI 커스텀 radius 처리에서 생긴 radius 불일치 문제 해결기
date: 2025-06-24 12:00:00 +0900
categories: [프로젝트, 트러블 슈팅]
tags: [Swift]
---

좋다. 지금 상황은 **`stroke`가 배경보다 더 둥글게 보이는 문제**다.
즉, **동일한 radius 값을 줬는데도 stroke가 더 둥글게 보이는 현상**이 발생한 것.
이건 SwiftUI 내부 동작 방식 때문에 생기는 시각적 차이로, 그걸 중심으로 기술 블로그용 트러블슈팅 글을 다시 정리한다.

---

## SwiftUI에서 `stroke`가 `background`보다 더 둥글게 보이는 이유

### 배경

카드 UI를 만들면서 아래와 같은 커스텀 modifier를 작성했다:

```swift
extension View {
    func roundedCornerWithBorder(
        lineWidth: CGFloat = 5,
        borderColor: Color = .black,
        backgroundColor: Color = Color.blue.opacity(0.3),
        radius: CGFloat = 20
    ) -> some View {
        self
            .background(backgroundColor)
            .cornerRadius(radius)
            .overlay(
                RoundedRectangle(cornerRadius: radius)
                    .stroke(borderColor, lineWidth: lineWidth)
            )
    }
}
```

`cornerRadius(radius)`로 뷰의 모서리를 둥글게 만들고,
`RoundedRectangle`의 `stroke`로 테두리를 입혔다.

하지만 예상과 달리, 다음과 같은 **이상한 시각적 현상**이 나타났다.

---

### 문제 현상

* 배경(`backgroundColor`)은 원하는 `radius`로 잘 적용됨
* 그런데 **stroke 테두리만 배경보다 더 둥글게** 보임
  → 즉, **곡률이 더 심한 것처럼 보임**

예: radius 20을 줬는데
stroke는 radius 24처럼 더 둥글게 보임

---

### 원인 분석

#### ✅ 핵심 원인: stroke는 경계선 중심 기준으로 그려진다

`stroke(borderColor, lineWidth:)`는
**도형의 경계선을 중심으로 안팎으로 퍼지며 그려짐**.

즉, `lineWidth`가 5이면:

* **2.5pt는 도형 바깥쪽**,
* **2.5pt는 도형 안쪽**으로 퍼짐

그 결과:

* 바깥쪽 stroke 곡률은 실제 도형보다 **더 멀리 확장**
* 이때 곡률 반지름도 자연히 더 커 보임 → **더 둥글게 보이는 현상 발생**

---

### 해결 방법

#### ✅ 방법 1: `strokeBorder()` 사용

SwiftUI에는 이 문제를 해결하기 위한 대안으로
\*\*stroke 대신 `strokeBorder`\*\*를 사용할 수 있다.

```swift
.overlay(
    RoundedRectangle(cornerRadius: radius)
        .strokeBorder(borderColor, lineWidth: lineWidth)
)
```

`strokeBorder()`는 **stroke가 도형 안쪽으로만 그려짐**
→ 배경과 radius 곡률이 정확히 일치함

---

### 결론

* SwiftUI에서 `stroke()`는 선이 도형 경계선을 기준으로 **양쪽으로 그려진다**
* 이로 인해 radius가 동일해도 **stroke 쪽 곡률이 더 크게 보이는 문제**가 발생
* 시각적으로 정확한 테두리를 만들고 싶다면 `strokeBorder()`를 사용해야 한다

---

### 한 줄 요약

> **stroke는 경계 중심으로 그려지므로 더 둥글게 보인다. 시각적으로 정확하게 맞추려면 strokeBorder를 써야 한다.**

