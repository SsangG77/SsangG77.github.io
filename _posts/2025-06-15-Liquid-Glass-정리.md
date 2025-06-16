---
title: Liquid Glass 정리
date: 2025-06-15 00:34:00 +0900
categories: [Conference, WWDC]
tags: [WWDC]
---

애플의 UI가 또 한번 크게 바뀌었는데 그 중에 가장 눈에 띄었던 Liquid Glass에 대해서 알아보자.

## Liquid Glass
![Image](https://github.com/user-attachments/assets/d630f8a7-d0a9-4b78-bf1b-859b548a99c5)
Max OS X의 Aqua UI, iOS 실시간 흐림, 다이나믹 아일랜드 등 애플의 다양한 생태계의 디자인들을 유연하게 도입한 디자인.
동적으로 조명을 휘게하고 가벼운 액체와 같이 유기적으로 움직인다.

</br>

### 이 세션에서는 핵심 종적 특성을 검토할 것이다.
Liquid Glass는 Lensing을 통해 시각적 외관을 정의한다.

![Image](https://github.com/user-attachments/assets/27ba3cfb-e5d4-40e4-87f3-beac23f76927)
이런식으로 빛을 굴절시켜 백그라운드 콘텐츠를 확인하면서 버튼들을 명확히 구분한다.

![Image](https://github.com/user-attachments/assets/5676eb45-26e0-44dd-adf3-63da29e16b2c)

또한 이러한 유연성 덕분에 사용자의 생각과 움직임이 유사한 인터페이스 경험을 제공해주는 장점이 있다.

</br>

### Liquid Glass가 여러 환경의 플랫폼에 적응하는 방법
필요한 경우에는 독립적으로 밝은 모드와 다크모드를 전환시킨다.

![Image](https://github.com/user-attachments/assets/47afff39-a9a2-4756-b1b6-37763b4816f0)
메뉴를 표시하는 경우에는 소재의 특성을 변형시켜, 빛의 굴절 정도와 투명도를 조절하여 가독성을 높여준다.

또한 Mac, iPad에서도 동일한 경험을 위해 백그라운드를 자연스럽게 보이게 하며 소재 특성을 돋보이게 한다.


- 스크롤 엣지 효과
움직이는 콘텐츠 위로 글래스를 들어 올린 시각효과를 주어 더 확장된 화면을 경험하게 해준다.
콘텐츠가 어둡게 전환되면 이 효과는 지능적으로 전환하여 디밍 효과를 적용한다.


- 활용법, 사용법
Liquid Glass 아래에 테이블뷰를 구성하면 다른 요소와 맞닿아 레이어를 흐리게 할 것이기 때문에, 명확성ㅇ르 유지하고 싶다면 콘텐츠 레이어에 두어야 한다.
</br>

![Image](https://github.com/user-attachments/assets/8e5f7d70-c7ca-4b9f-b854-be8c801114a5)

요소를 위아래로 겹치게 구성하면 인터페이스를 답답하고 혼란스럽게 만들 수 있다.


---
[원본 영상 보러 가기](https://developer.apple.com/kr/videos/play/wwdc2025/219/)
