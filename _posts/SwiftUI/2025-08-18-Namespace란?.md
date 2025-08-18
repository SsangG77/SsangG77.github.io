---
title: JAVA - Override
date: 2025-08-18 12:00:00 +0900
categories: [SwuftUI, 개념]
tags: [SwiftUI]
---


# @Namespace에 대해서 알아보자
- 애니메이션 전환을 매끄럽게 만들기 위해 사용되는 속성 래퍼. 특히 `matchedGeometryEffet`와 함께 자주 쓰임
- 두 뷰를 서로 연결하여 한뷰에서 다른 뷰로 부드럽게 형태/위치/크기가 변하는 애니메이션을 만들어줌


### 사용법

1. `@Namespace`변수를 뷰에 선언
```
@Namespace var animation
```

2. 두개의 뷰(출발지, 도착지)에 `matchedGeometryEffect(id:..., in: namespace)`적용
```
... // 출발지 뷰
.matchedGeometryEffect(id: "box", in: animation) // "box"는 예제 아이디

... // 도착지 뷰
.matchedGeometryEffect(id: "box", in: animation)
```

3. SwiftUI가 애니메이션 중 두 뷰를 "같은 객체"처럼 인식해서 부드럽게 전환

### 전체 코드
```
import SwiftUI

struct NamespaceExample: View {
    @Namespace private var animation
    @State private var isExpanded = false
    
    var body: some View {
        VStack {
            if !isExpanded {
                RoundedRectangle(cornerRadius: 12)
                    .fill(Color.blue)
                    .frame(width: 100, height: 100)
                    .matchedGeometryEffect(id: "box", in: animation)
                    .onTapGesture {
                        withAnimation(.spring()) {
                            isExpanded.toggle()
                        }
                    }
            } else {
                RoundedRectangle(cornerRadius: 12)
                    .fill(Color.blue)
                    .frame(width: 300, height: 300)
                    .matchedGeometryEffect(id: "box", in: animation)
                    .onTapGesture {
                        withAnimation(.spring()) {
                            isExpanded.toggle()
                        }
                    }
            }
        }
    }
}
```

> 작은 파랑색 박스 탭 -> 큰 박스로 자연스럽게 확장
