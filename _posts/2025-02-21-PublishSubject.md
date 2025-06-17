---
title: 
date: 2025-02-21 12:00:00 +0900
categories: [개념, RxSwift]
tags: []
---


# PublishSubject란

- 이벤트가 발생하면 데이터를 구독자들에게 전달하는 역할이다
- `.onNext(())`가 호출될때마다 구독자에게 데이터가 전달된다.(이벤트가 방출된다.)

```swift
let buttonClickPublishSubject = PublishSubject<Void>()

// ✅ 이벤트가 방출되면 구독자가 반응함
buttonClickPublishSubject
    .debug("a")
    .subscribe(onNext: {
        print("dddddd")
    })
    .disposed(by: disposeBag)  // ✅ 자동 해제 추가

// ✅ 이벤트 방출 (이 코드 없으면 아무 일도 안 일어남)
buttonClickPublishSubject.onNext(())
```

---

Observable vs Subject

- Observable은 구독자가 생길때 데이터를 전달한다.
- Subject는 연결을 해놓고 원하는 시점에 `onNext()`로 데이터를 전달한다.

