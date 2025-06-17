---
title: NavigationSplitView란
date: 2024-10-31 12:00:00 +0900
categories: [개념, SwiftUI]
tags: []
---



## 개요

`NavigationSplitView`는 SwiftUI에서 iPadOS, macOS 등 **큰 화면에서 마스터-디테일 구조를 구현할 수 있는 기본 제공 UI 컴포넌트**다.
iOS 16+, macOS 13+부터 지원되며, 기존의 `NavigationView`보다 훨씬 명확한 **3단 분할 구조**를 지원한다.

---

## 기본 구조

```swift
NavigationSplitView {
    // 사이드바 (1단)
} content: {
    // 리스트나 콘텐츠 (2단)
} detail: {
    // 상세 화면 (3단)
}
```

각각은 선택적으로 생략 가능하며, 사이드바와 콘텐츠 영역만 쓰거나 콘텐츠-디테일만 쓰는 것도 가능하다.

---

## 예제 코드

```swift
struct ContentView: View {
    @State private var selectedFruit: String? = nil
    let fruits = ["Apple", "Banana", "Cherry"]

    var body: some View {
        NavigationSplitView {
            List(fruits, id: \.self, selection: $selectedFruit) { fruit in
                Text(fruit)
            }
        } content: {
            if let fruit = selectedFruit {
                Text("\(fruit) is selected")
            } else {
                Text("Select a fruit")
            }
        } detail: {
            if let fruit = selectedFruit {
                Text("Detail view for \(fruit)")
                    .font(.largeTitle)
            } else {
                Text("No fruit selected")
            }
        }
    }
}
```

---

## 주요 특징

### 1. 3단계 UI 구성

* **Sidebar**: 카테고리, 리스트 등
* **Content**: 선택한 항목에 대한 중간 요약
* **Detail**: 실제 상세 내용

### 2. 플랫폼 대응

* iPad와 macOS에서 3단 구조를 지원
* iPhone에서는 자동으로 적절하게 축소되어 `NavigationStack` 기반으로 동작

### 3. 상태 바인딩

`@State` 또는 `@Binding`으로 선택된 항목을 바인딩하여 콘텐츠/디테일 영역을 자동으로 변경 가능

---

## iOS 16 이전과의 차이점

| 항목      | `NavigationView` | `NavigationSplitView` |
| ------- | ---------------- | --------------------- |
| 구조      | 2단               | 최대 3단                 |
| 방향성     | push/pop (stack) | 선택 기반 (split)         |
| 플랫폼 최적화 | iOS 중심           | iPad/macOS 중심         |
| 사용 방식   | 전통적 네비게이션        | 선택 + 바인딩 중심           |

---

## 언제 쓰는가?

* 아이패드나 맥에서 **분할 뷰** UI가 필요할 때
* 마스터-디테일 구조가 뚜렷한 앱 (예: 메일, 노트, 설정 등)
* 선택 기반 UI에서 선택 항목에 따라 콘텐츠를 구성하고 싶을 때

---

## 마무리

`NavigationSplitView`는 큰 화면에서의 사용자 경험을 개선하기 위한 SwiftUI의 새로운 접근이다.
데이터 중심으로 동작하며, 바인딩을 기반으로 뷰가 유기적으로 변한다.

**iPad 및 macOS 앱을 고려한다면 필수로 알아둬야 할 구성 요소**다.

> 구조적 UI가 필요한 앱에서는 `NavigationSplitView`를 기본 도구로 삼자.
> 뷰 계층의 복잡도를 줄이고, 사용자 경험을 직관적으로 만들 수 있다.
