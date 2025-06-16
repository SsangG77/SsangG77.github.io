---
title: Dictionary란
date: 2025-03-26 12:00:00 +0900
categories: [CS, 자료구조]
tags: []
---


**Key - Value** 쌍으로 이루어진 **순서 없는 컬렉션**.

- key는 **유일(unique)** 해야 함
- 키는 Hashable해야 함 (String, Int, UUID 등 기본 타입은 모두 가능)
- value는 중복 가능
- key를 이용해 빠르게 값을 조회할 수 있음 (`O(1)`)


예제 코드

```swift
let dict: [String: Int] = [
    "apple": 3, //"apple"이 key고 3이 value
    "banana": 5 //"banana"가 key고 5가 value
]
```

- 값 추가 / 수정

```swift

scores["민수"] = 95          // 추가
scores["영희"] = 88          // 수정

```


- 조회

```swift
dict["apple"] // 결과 : 3
```

### Dictionary(uniqueKeysWithValues: )

- key가 중복되면 런타임 에러 발생
