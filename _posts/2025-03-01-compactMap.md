---
title: compactMap
date: 2025-03-01 12:00:00 +0900
categories: [개념, RxSwift]
tags: []
---

`compactMap`은 Swift의 컬렉션(`Array`, `Set`, `Dictionary` 등)에서 조건에 맞체 변환을 시도하여 nil이 나오는 값을 제외함

예제 코드

```swift
let numbers = ["1", "2", "three", "4"]

let converted = numbers.compactMap { Int($0) } // 조건 : 인자를 정수로 변환
print(converted) // [1, 2, 4]

```
