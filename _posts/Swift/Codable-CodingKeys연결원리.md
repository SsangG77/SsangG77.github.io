---
title: Swift Codable과 CodingKeys는 어떻게 연결될까?
date: 2025-09-01 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, Codable]
---

— enum만 정의했는데 어떻게 매핑을 알아먹는지?
Swift에서 JSON을 모델로 디코딩하다 보면 이런 코드를 많이 작성합니다.
struct LikeListItemModel: Codable {
    var targetIdx: Int
    var mName: String
    var mNickname: String
    var mAge: String
    var mArea: String
    var mProfileImage1: String
    var iLike: String
    var otherLike: String
    var myLikeDate: String?
    var otherLikeDate: String?

    enum CodingKeys: String, CodingKey {
        case targetIdx = "target_idx"
        case mName = "m_name"
        case mNickname = "m_nickname"
        case mAge = "m_age"
        case mArea = "m_area"
        case mProfileImage1 = "m_profile_image1"
        case iLike = "i_like"
        case otherLike = "other_like"
        case myLikeDate = "my_like_date"
        case otherLikeDate = "other_like_date"
    }
}
여기서 흔히 드는 의문:
“CodingKeys라는 enum만 적어놨는데, Swift는 어떻게 이 매핑 정보를 알고 디코딩을 하지?”
이 글에서는 바로 그 “마법”이 어떻게 작동하는지 설명한다.
📌 Codable은 어떻게 동작하는가?
Codable은 사실 Encodable + Decodable의 타입 별칭이다.
Swift는 struct에 Codable을 선언하면, 자동으로 JSON ↔ struct 변환 코드를 Synthesized(자동 생성) 한다.
즉, 아래 코드를 직접 작성하지 않아도
init(from decoder: Decoder) throws
func encode(to encoder: Encoder) throws
Swift 컴파일러가 자동으로 만들어준다.
그럼 자동 생성 코드는 어디에 기반해서 만들어질까?
→ 바로 CodingKeys enum 이다.
📌 CodingKeys의 역할: JSON key ↔ property name 매핑 테이블
CodingKeys는 내부적으로 다음과 같은 역할을 한다:
struct의 각 프로퍼티가 JSON의 어떤 키와 연결되는지 정의하는 일종의 사전(dictionary) 이다.
CodingKey 프로토콜을 따르는 enum은 Swift가 자동 생성한 디코딩/인코딩 코드에서 사용된다.
즉, 아래는 “타입 이름만 다를 뿐 사실상 매핑 테이블”이다.
Swift 프로퍼티	JSON 키
targetIdx	target_idx
mName	m_name
mNickname	m_nickname
…	…
Swift는 자동 생성한 init(from:)에서 이런 작업을 한다:
let container = try decoder.container(keyedBy: CodingKeys.self)
self.targetIdx = try container.decode(Int.self, forKey: .targetIdx)
여기서 .targetIdx는 CodingKeys의 케이스를 의미한다.
즉, Swift는 다음 사실을 알고 있다:
CodingKeys.targetIdx → "target_idx"
CodingKeys.mName → "m_name"
...
따라서 JSON의 “target_idx”라는 이름의 필드를 자동으로 읽고 → struct의 targetIdx 변수에 넣는다.
📌 “그럼 CodingKeys를 안 적으면 어떻게 되나?”
CodingKeys를 생략하면 Swift는 struct의 프로퍼티 이름을 그대로 JSON 키로 사용한다.
예:
struct User: Codable {
    let name: String
    let age: Int
}
이 경우 Swift는 내부적으로 다음을 자동 생성한다:
enum CodingKeys: String, CodingKey {
    case name
    case age
}
즉, JSON이 아래와 같아야 한다:
{
  "name": "Tom",
  "age": 30
}
만약 JSON 키가 snake_case라면?
{
  "user_name": "Tom"
}
→ CodingKeys 없이는 매핑할 수 없다.
그래서 JSON 키와 Swift 프로퍼티 이름이 다르다면 반드시 CodingKeys가 필요하다.
📌 정리 — 왜 enum만 선언해도 디코딩이 되는가?
Swift 컴파일러는 다음 작업을 자동으로 수행한다:
struct가 Codable일 경우 init(from:) / encode(to:) 메서드를 자동 생성한다.
자동 생성할 때 CodingKeys enum을 검사한다.
enum에 적힌 case = JSON key를 기반으로 디코딩 매핑 테이블을 만든다.
container.decode을 호출할 때 이 테이블을 이용해 JSON 키를 찾아서 값 매핑을 수행한다.
즉, 우리가 코드를 직접 짜지 않아도 Swift가 내부에서 이렇게 한다:
JSON key "target_idx" → CodingKeys.targetIdx → targetIdx 프로퍼티에 할당
이런 일련의 과정을 “Synthesized Codable”이라고 부른다.
