---
title: @AppStorage에 문자열 배열을 사용하기 위한 Array에 Extension하기 
date: 2025-02-01 12:00:00 +0900
categories: [가이드]
tags: []
---


### 전체 코드

```swift
extension Array: RawRepresentable where Element: Codable {
    public init?(rawValue: String) {
        guard let data = rawValue.data(using: .utf8),
              let result = try? JSONDecoder().decode([Element].self, from: data)
        else {
            return nil
        }
        self = result
    }

    public var rawValue: String {
        guard let data = try? JSONEncoder().encode(self),
              let result = String(data: data, encoding: .utf8)
        else {
            return "[]"
        }
        return result
    }
}
```

```swift
extension Array: RawRepresentable
```

Array 타입을 `RawRepresentable` 프로토콜을 준수하도록 확장하는 것.

이 프토토콜은 `rawValue`라는 연산 프로퍼티를 요구한다. 

예시 코드에서는 배열을 String(JSON)으로 변환하도록 구현하고 있음.

```swift
 where Element: Codable
```

Array는 제네릭 타입인데 `Array<Element>`에서 `Element`가 어떤 타입이든 올 수 있지만 `where Element: Codable`을 추가해서 배열의 요소가 `Codable`을 준수하는 타입에만 `RawRepresentable`을 적용할 수 있도록 제한한다.

<aside>
<img src="/icons/dialogue_yellow.svg" alt="/icons/dialogue_yellow.svg" width="40px" />

여기서 where이란?

- **제네릭 타입에 추가적인 조건을 부여할 때**
- **프로토콜 확장(Protocol Extension) 시 특정 조건에서만 동작하게 할 때**
- **패턴 매칭 및 조건부 제약을 걸 때**

사용하는 제네릭 문법이다.

예시 코드에서는 배열의 Element가 Codable 프로토콜을 준수해야 한다는 뜻이다.

</aside>


