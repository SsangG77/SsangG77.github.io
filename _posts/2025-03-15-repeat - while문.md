---
title: repeat - while문
date: 2025-04-07 12:00:00 +0900
categories: [개념, Swift]
tags: [문법]
---

# Swift repeat-while: 최소 1회 실행 보장 루프
Swift에서 루프를 만들 때 가장 흔히 쓰는 건 for-in이나 while문이다.
하지만 조건과 상관없이 무조건 한 번은 실행돼야 하는 작업이라면?
이럴 때 쓰는 것이 repeat-while 문이다.


# repeat-while이란?
repeat-while은 조건을 나중에 검사하는 루프다.
즉, 코드 블록을 최소 한 번은 실행하고, 그 후 조건을 검사해서 반복 여부를 결정한다.  


```swift
repeat {
    // 실행할 코드
} while 조건
```

<br>

예제
1. 사용자 입력 받기 (조건과 상관없이 최소 1회 실행)

```swift
var input: String

repeat {
    print("명령어를 입력하세요:")
    input = readLine() ?? ""
} while input != "exit"
```

사용자가 "exit"을 입력할 때까지 반복

첫 실행 시 조건 검사 없이 무조건 블록 실행됨

2. 최소 1회 실행이 필요한 초기화
```swift
var attempts = 0
var success = false

repeat {
    attempts += 1
    print("시도 \(attempts)회")
    success = Bool.random()  // 랜덤 성공 여부
} while !success && attempts < 5
```

최소 1번 시도는 필요
5번까지만 시도하고, 성공하면 종료

<br>

# while과의 차이점
구문	실행 전 조건 검사	최소 1회 보장
while	O	X
repeat-while	X	O


```swift
// while: 조건 만족해야 한 번이라도 실행
while false {
    print("실행 안 됨")
}

// repeat-while: 조건이 false여도 1번은 실행
repeat {
    print("조건이 false여도 실행됨")
} while false
```

<br>

# 언제 써야 하나? 
사용자 입력 받기

랜덤 값 기반 로직

네트워크 재시도 / 최소 1회 실행 로직

초기화, 상태 갱신이 반드시 1회 필요한 경우

# 마무리
repeat-while은 자주 쓰이진 않지만, 조건보다 실행이 먼저 필요한 경우 매우 유용하다.
조건이 먼저냐, 실행이 먼저냐? 이 판단이 while과 repeat-while을 나누는 기준이다.


# 참고 링크
Swift 공식 문서 - Control Flow
