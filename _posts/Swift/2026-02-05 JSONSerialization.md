---
title: JSONSerialization
date: 2026-02-05 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, JSON]
---




Swift의 JSONSerialization이란?
JSONSerialization은 Swift에서 JSON 데이터를 파싱하거나 생성하기 위한 Foundation 프레임워크의 클래스다.
주로 네트워크 통신 과정에서 JSON ↔︎ Swift 객체 변환을 담당한다.
JSONSerialization의 역할
JSONSerialization은 크게 두 가지 기능을 제공한다.

---

1. JSON Data → Swift 객체
서버로부터 받은 JSON Data를
➡️ Dictionary, Array 등 Swift 기본 타입으로 변환한다.

```
let data: Data = ...

let jsonObject = try JSONSerialization.jsonObject(
    with: data,
    options: []
)

if let dictionary = jsonObject as? [String: Any] {
    print(dictionary["name"])
}
```

변환 결과는 다음 타입 중 하나가 된다.
[String: Any]
[Any]
String
Number
Bool
NSNull

---

2. Swift 객체 → JSON Data
Swift의 딕셔너리 또는 배열을
➡️ JSON 형식의 Data로 변환한다.
```
let parameters: [String: Any] = [
    "name": "Sangjin",
    "age": 29
]

let data = try JSONSerialization.data(
    withJSONObject: parameters,
    options: .prettyPrinted
)
```

이렇게 생성된 Data는 API 요청 바디로 사용된다.
사용 조건과 주의사항
JSONSerialization은 아무 Swift 타입이나 변환해주지 않는다.
아래 타입만 JSON으로 변환 가능하다.
Dictionary<String, Any>
Array<Any>
String, Int, Double, Bool
NSNull
또한, 컴파일 타임 타입 체크가 불가능해
런타임 캐스팅 오류가 발생할 수 있다.
// 타입 안정성이 낮음
let age = dictionary["age"] as? Int
Codable과의 비교
Swift 4 이후에는 대부분의 경우 Codable을 더 많이 사용한다.
Codable 예시
```
struct User: Codable {
    let name: String
    let age: Int
}
let user = try JSONDecoder().decode(User.self, from: data)
JSONSerialization vs Codable
구분	JSONSerialization	Codable
```

타입 안정성	❌	✅
코드 가독성	낮음	높음
구조 변경 대응	유연	구조 고정
복잡한 모델	불편	매우 편리
JSONSerialization을 쓰는 경우
다음과 같은 상황에서는 여전히 JSONSerialization이 유용하다.
JSON 구조가 동적으로 변경되는 경우
키 이름이 매번 달라지는 응답
일부 필드만 빠르게 파싱할 때
서드파티 SDK에서 Any 타입으로 내려오는 JSON 처리
