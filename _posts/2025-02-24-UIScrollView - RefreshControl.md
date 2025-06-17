---
title: 
date: 2025-02-24 12:00:00 +0900
categories: [개념, Swift]
tags: [ScrollView]
---

UIScrollView의 내부 함수로써 뷰를 밑으로 당기는 동작을 했을때 나타나는 로딩 인디케이터 (:loading:)

## 내부 코드

```swift
@MainActor open class UIRefreshControl : UIControl {

    public init()

    open var isRefreshing: Bool { get }

    open var tintColor: UIColor!

    open var attributedTitle: NSAttributedString?

    @available(iOS 6.0, *)
    open func beginRefreshing()

    @available(iOS 6.0, *)
    open func endRefreshing()
}
```

### **isRefreshing: Bool**

- 현재 새로고침 상태인지 여부를 나타내는 읽기 전용 속성.
- `true`이면 새로고침이 진행 중, `false`이면 새로고침이 종료된 상태.

### **tintColor: UIColor!**

- 새로고침 컨트롤의 스피너 색상을 설정하는 속성.

### **attributedTitle: NSAttributedString?**

- 새로고침 컨트롤에 표시할 텍스트(`NSAttributedString`).
- 주로 새로고침 상태를 설명하는 문구를 표시할 때 사용.

### **func beginRefreshing()**

- 새로고침 상태를 수동으로 시작하는 메서드.
- 새로고침을 프로그램적으로 트리거할 때 사용.

### **func endRefreshing()**

- 새로고침 상태를 종료하는 메서드.
- 데이터 로드가 끝난 후 UI를 원래 상태로 복귀시킬 때 사용.
