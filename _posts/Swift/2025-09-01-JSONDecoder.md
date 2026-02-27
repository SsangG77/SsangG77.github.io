---
title: JSONDecoder
date: 2025-09-01 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, JSONDecoder, json]
---

# 1. JSONDecoder란?
JSONDecoder는 Foundation 프레임워크에 포함된 클래스로, JSON 데이터를 Swift의 타입(Struct, Class)으로 변환해 주는 객체입니다.

핵심은 **'자동화'**입니다. 우리가 정의한 모델이 Codable 프로토콜을 채택하고 있다면, JSONDecoder는 JSON의 키(Key)와 모델의 프로퍼티 이름을 매칭시켜 값을 자동으로 주입해 줍니다.

# 2. 기본 사용법
가장 이상적인 상황입니다. 서버가 내려주는 JSON 키 이름과 내 모델의 변수명이 완벽하게 일치하는 경우입니다.

JSON 데이터

JSON
```
{
  "title": "Swift 정복하기",
  "views": 150,
  "isPublic": true
}
```

Swift 모델 & 디코딩

```
import Foundation

// 1. Codable 채택
struct Post: Codable {
    let title: String
    let views: Int
    let isPublic: Bool
}

// 2. 가상의 JSON 데이터 (Data 타입)
let jsonString = """
{
  "title": "Swift 정복하기",
  "views": 150,
  "isPublic": true
}
"""
let jsonData = jsonString.data(using: .utf8)!

// 3. 디코딩 실행
do {
    let decoder = JSONDecoder()
    let post = try decoder.decode(Post.self, from: jsonData)
    
    print("제목: \(post.title)") // 출력: 제목: Swift 정복하기
} catch {
    print("파싱 실패: \(error)")
}
```

# 3. 실무 꿀팁: 자주 마주하는 3가지 상황
실무에서는 JSON 형태가 제각각입니다. JSONDecoder의 강력한 옵션들로 이를 우아하게 해결할 수 있습니다.

### 1: Snake Case vs Camel Case

서버 개발자분들은 보통 snake_case(user_name)를 선호하고, iOS는 camelCase(userName)를 씁니다. 변수명을 서버에 맞춰 user_name으로 짓기엔 Swift 스타일 가이드에 어긋납니다.

해결책: keyDecodingStrategy

```
let jsonString = """
{ "user_name": "Kim", "created_at": "2023-10-01" }
"""

struct User: Codable {
    let userName: String  // Swift 스타일 유지
    let createdAt: String
}

let decoder = JSONDecoder()
// 이 한 줄이면 snake_case를 camelCase로 자동 변환해줍니다!
decoder.keyDecodingStrategy = .convertFromSnakeCase 

let user = try decoder.decode(User.self, from: jsonData)
```
### 상황 2: 날짜가 String으로 올 때

JSON에는 Date 타입이 없습니다. 보통 ISO 8601 포맷의 문자열("2023-12-05T10:00:00Z")로 내려옵니다. 이를 String으로 받아서 다시 DateFormatter로 변환하는 건 번거롭습니다.

해결책: dateDecodingStrategy

```
struct Event: Codable {
    let name: String
    let date: Date // 바로 Date 타입으로 선언
}

let decoder = JSONDecoder()
// .iso8601 옵션을 주면 알아서 Date로 변환합니다.
decoder.dateDecodingStrategy = .iso8601 

let event = try decoder.decode(Event.self, from: jsonData)
```

### 상황 3: 키 이름이 완전히 다를 때

서버에서는 NM이라고 보내는데, 앱에서는 명확하게 name이라고 쓰고 싶다면? 혹은 예약어(default, operator 등)가 키 이름으로 올 때는?

해결책: CodingKeys 열거형

```
struct Product: Codable {
    let name: String
    let price: Int
    
    // CodingKeys 열거형을 정의하여 1:1 매핑
    enum CodingKeys: String, CodingKey {
        case name = "NM"    // JSON의 "NM"을 name으로 매핑
        case price = "PRC"  // JSON의 "PRC"를 price로 매핑
    }
}
```



### 4. 에러 핸들링 (Debugging)
디코딩이 실패했을 때 catch 블록에서 단순히 print(error)만 찍으면 원인을 찾기 힘듭니다. DecodingError를 활용해 구체적인 원인을 파악하세요.

```
do {
    let data = try decoder.decode(User.self, from: jsonData)
} catch DecodingError.keyNotFound(let key, let context) {
    print("키를 찾을 수 없음: \(key.stringValue)")
    print("경로: \(context.codingPath)")
} catch DecodingError.typeMismatch(let type, let context) {
    print("타입이 맞지 않음: \(type) 타입이어야 하는데 다른게 옴")
    print("경로: \(context.codingPath)")
} catch {
    print("그 외 에러: \(error)")
}
```
