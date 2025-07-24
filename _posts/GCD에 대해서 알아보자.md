---
title: GCD에 대해서 알아보자
date: 2025-07-24 12:00:00 +0900
categories: [개념, Swift, iOS]
tags: [GCD, iOS]
---

#개요
GCD(Grand Central Dispatch)는 iOS에서 멀티스레딩을 쉽게 구현하기 위한 Apple의 라이브러리.
자동으로 비동기, 동시성 처리, 작업 큐 관리 등을 효율적으로 수행할 수 있게 해준다.

# 우선적으로 알아야 하는 개념들

### 큐
작업들을 저장하고 순서대로 처리하는 작업 저장소이다.
FIFO(First In First Out) 구조를 가진다.

### 큐의 종류
- 직렬 큐 : 한번에 하나씩 실행

  ```
  let serialQueue = DispatchQueue(label: "serial")
  serialQueue.async {
      print("A")
  }
  serialQueue.async {
      print("B")
  }
  ```

- 병렬 큐 : 여러 작업을 동시에 실행
  
  ```
  let concurrentQueue = DispatchQueue(label: "concurrent", attributes: .concurrent)
  concurrentQueue.async {
      print("A")
  }
  concurrentQueue.async {
      print("B")
  }
  ```

### 호출 스택
- 함수가 호출되는 순서를 기록하고 추적하는 메모리 공간이다.
- 스레드마다 하나씩 존재하며, 실행중인 함수와 그 함수를 호출한 함수들이 쌓여있는 구조다. (Stack = LIFO)
- 함수가 호출되면 스택에 "프레임(함수 정보)"이 쌓인다.
- 함수가 리턴되면 해당 프레임이 제거된다.

  ```
  func a() {
      b()
  }
  
  func b() {
      c()
  }
  
  func c() {
      print("끝")
  }
  
  a()
  ```

### 스레드
- 프로세스(프로그램이 메모리에 올라온 상태에서 CPU, OS가 관리하는 실행 단위) 내에서 실제로 코드를 실행하는 최소 실행 단위
- 하나의 프로세스는 하나 이상의 스레드를 가진다.
- 하나의 스레드는 독립된 호출 스택과 레지스터를 가진다.
- 모든 스레드는 같은 힙 메모리를 공유한다.

```
// 백그라운드 스레드
DispatchQueue.global().async {
    print("이건 백그라운드 스레드에서 실행됨")
}

// 메인 스레드
DispatchQueue.main.async {
    print("이건 메인 스레드에서 실행됨")
}
```

| 항목          | 메인 스레드           | 백그라운드 스레드              |
| ----------- | ---------------- | ---------------------- |
| 역할          | UI 처리, 사용자 입력 처리 | 데이터 처리, 네트워크, 무거운 작업 등 |
| 병렬 처리       | ❌ 단일 스레드         | ✅ 여러 개 가능              |
| UI 접근 가능 여부 | ✅ 가능             | ❌ 직접 접근 불가 (크래시 발생)    |

모든 작업을 순서대로 처리하면 무거운 연산을 하는동안 다른 작업(UI 작업 등)은 멈추게 되기 떄문에 스레드가 필요함.



# 정리
GCD는 작업(클로저)를 큐에다가 할당하고 적절한 시점에 스레드를 사용해서 그 클로저를 실행한다.

  
