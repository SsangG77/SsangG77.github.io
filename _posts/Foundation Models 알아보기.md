---
title: Foundation Models 알아보기
date: 2025-07-17 12:00:00 +0900
categories: [WWDC]
tags: [iOS, Swift, WWDC, AI]
---



# Foundation Models 알아보기

* 이번 렛어스고 찍먹톤에서는 WWDC2025에 나온 기술들을 활용하여 프로젝트를 개발하는 미션인데 팀원들과 상의한 끝에 사용자의 감정을 분석하여 행동 기반 피드백을 주는 AI 프로젝트를 개발하기로 결정하였다.
* 그에 따라 이번 WWDC에 나온 온디바이스 대규모 언어모델에 접근 가능한 프레임워크인 Foundation Models에 대해 알아보고자 한다.


## 개요
온디바이스이기 때문에 오프라인에서 가능하고 보안 측면에서도 유리하다.


### 프롬프트 보내기
해당 코드는 모델에 프롬프트를 보내는 예제이다.
session을 생성하여 .respond 메소드에 프롬프트를 전달하여 응답을 받을 수 있다.
```
import FoundationModels
import Playgrounds

#Playground {
    let session = LanguageModelSession()
    let response = try await session.respond(to: "What's a good name for a trip to Japan? Respond only with a title")
}
```

<br>

### 가이드 기반 생성
기본적으로 언어 모델은 비구조적 자연어를 출력하는데 뷰에 이것을 바로 적용시키기는 어렵다.
일반적인 해결 방법은 JSON이나 CSV같은 분석하기 쉬운 형태로 요청하는 것인데 그렇게 하면 요청사항들을 계속해서 추가해줘야 하는 문제가 또 발생한다.
이를 해결하기 위해 @Generable, @Guide를 활용하여 가이드 기반 생성을 하는 것이다.

```
@Generable
struct SearchSuggestions {
  @Guide(description: "가이드 설명글 작성 부분", .count(4))
  var searchTerms: [String]
}

let prompt = """
  Generate a list of suggested search terms for an app about visiting famous landmarks.
"""

let response = try await session.respond(
  to: prompt,
  generating: SearchSuggestions.self
)
```
이렇게 작성하면 프롬프트에 출력 형식을 따로 지정할 필요가 없게 되어 간결하게 사용이 가능하다.
Generable 타입은 부동소수점 십진수, 불리언, 문자열, 배열로 구성이 가능하며 재귀 타입도 지원한다.










