---
title: map 연산자, flatmap 연산자
date: 2025-03-05 12:00:00 +0900
categories: [개념, RxSwift]
tags: [map, flatMap]
---

## 1. `map` 연산자

<aside>

방출된 데이터들을 순차적으로 연산한 후 다시 Observable로 반환하는 체이닝 함수

</aside>

예제 코드

```swift
Observable.of(1, 2, 3)
    .map { $0 * $0 }
    .subscribe(onNext: { print($0) })
// 출력: 1, 4, 9
```

결과값

```swift
map result: RxSwift.Observable<Swift.Int>
map result: RxSwift.Observable<Swift.Int>
map result: RxSwift.Observable<Swift.Int>
```

## 2. `flatMap` 연산자:

<aside>

- 클로저의 인자값은 실제 값(Int 등)
    - 연산 후 반드시 Observable로 감싸서 반환해야 함
    - 중첩된 Observable들을 하나의 Observable로 평평하게 만들어줌
</aside>

예제 코드

```swift
Observable.of(1, 2, 3)
    .flatMap { num in 
        Observable.of(num, num * 10)
    }
    .subscribe(onNext: { value in
        print("flatMap result: \(value)")
    })
    .disposed(by: disposeBag)
```

결과값

```swift
flatMap result: 1
flatMap result: 10
flatMap result: 2
flatMap result: 20
flatMap result: 3
flatMap result: 30
```
