---
title: NotificationCenter란
date: 2025-03-23 12:00:00 +0900
categories: [개념, Swift]
tags: []
---

객체 간의 이벤트를 전달하는 시스템

## 기능

- 객체 간의 직접적인 연결 없이 이벤트 전달 가능
- 뷰 컨트롤러 간 데이터나 이벤트 전달 가능
- 비동기적으로 실행 가능하여 UI업데이트에 활용 가능

## 기본 개념

1. post() - 이벤트 발생
    - 특정 이벤트가 발생하면 알림을 보내 다른 객체에 전달함
    
    ```swift
    NotificationCenter.default.post(name: Notification.Name("CustomEvent"), object: nil)
    
    ```
    
2. addObserver() - 이벤트 감지
    - 이벤트 발생시 감지하고 특정 동작 수행
    
    ```swift
    NotificationCenter.default.addObserver(self, selector: #selector(handleCustomEvent), name: Notification.Name("CustomEvent"), object: nil)
    
    @objc func handleCustomEvent() {
        print("📢 CustomEvent 발생!")
    }
    
    ```
    
3. removeIbserver() - 이벤트 제거
    - 뷰 컨트롤러가 해제될 때 옵저버를 제거
    
    ```swift
    deinit {
        NotificationCenter.default.removeObserver(self, name: Notification.Name("CustomEvent"), object: nil)
    }
    
    ```
    

<aside>
⚠️

`Notification.Name("CustomEvent")`에 입력된 이름으로 인식하여 이벤트를 주고받기 때문에 이름이 같아야 함.

</aside>
