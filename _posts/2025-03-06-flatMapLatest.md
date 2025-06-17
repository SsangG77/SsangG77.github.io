---
title: flatMapLatest
date: 2025-03-06 12:00:00 +0900
categories: [개념, RxSwift]
tags: []
---

새로운 값이 들어오면, 기존에 진행 중이던 작업을 취소하고 새로운 작업을 시작하는 연산자

```swift
button.rx.tap
    .flatMapLatest { Observable.just("새로운 값 방출") }
    .subscribe(onNext: { print($0) })

```

1. 버튼을 클릭 새로운 값 방출 : `button.rx.tap`
2. 기존의 `Observable` 취소, 새로운 `Observable` 방출 : `Observable.just("새로운 값 방출")`
3. 새로운 `Observable` 을 구독하여 출력 : `.subscribe(onNext: { print($0) })`
