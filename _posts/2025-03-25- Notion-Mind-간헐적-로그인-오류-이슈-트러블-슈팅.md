---
title: Notion-Mind 간헐적 로그인 오류 이슈 트러블 슈팅
date: 2025-03-25 12:00:00 +0900
categories: [프로젝트, 트러블 슈팅]
tags: [Notion-mind, 로그인]
---


문제 : 빌드를 한 후 첫 로그인은 성공하지만 로그아웃 후 다시 로그인을 하면 메인뷰컨트롤러로 넘어가지 않는 문제 발생.

## 기존 방식

1. connection to notion 버튼 탭
2. 노션 인증 페이지 사파리로 열기
3. 사파리에서 인증완료되면 서버로 요청한 후 앱 스키마로 리다이렉트
4. 앱 스키마로 sceneDelegate에서 url 쿼리 처리
5. 쿼리에서 로그인 결과가 true면 loginViewModel의 PublishRelay에 값 할당
6. PublishRelay를 구독중이 개체가 감지하면 메인화면으로 이동

→ 이방식은 첫 로그인은 가능하지만 설정에서 로그아웃 후 재로그인할때는 `PublishRelay`에 값을 할당 하더라도 그 결과를 이미 인스턴스가 생성된 `loginViewController`에서 받지 못했기 때문에 재로그인이 안됨.

개선 방식

→ 프로세스는 변경하지 않았지만 `loginViewModel`을 싱글톤으로 변경하여 `PublishRelay`의 값 변경을 다른 객체에도 전달할 수 있도록 변경해서 로그인이 언제든지 가능하도록 함.

