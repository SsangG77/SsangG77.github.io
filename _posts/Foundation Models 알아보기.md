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



## 예제

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



