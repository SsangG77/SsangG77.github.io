---
title: DispatchQueue
date: 2025-02-24 12:00:00 +0900
categories: [개념, Swift]
tags: [Thread]
---

작업을 비동기적으로 실행할 수 있는 GCD(Grand Central Dispatch) 큐.

멀티쓰레딩이 가능하도록 도와줌.

## DispatchQueue의 역할

- 비동기적으로 실행하게 하여 UI가 멈추지 않도록 한다.
- 백그라운드에서 실행하여 CPU 리소스를 최적화 해준다.
- 우선순위(QOS)를 설정하여 중요한 작업을 빠르게 처리한다.

### 1. `DispatchQueue.main`

- 메인쓰레드에서 실행되는 큐

⚠️ UI업데이트는 반드시 메인큐에서 실행.

```swift
DispatchQueue.main.async {
    // UI 업데이트 (메인 스레드에서 실행)
    self.label.text = "Hello, Swift!"
}
```

### 2. `DispatchQueue.global(qos:  )`

- 멀티쓰레드에서 실행되는 큐
- 무거운 작업(네트워크 요청, 데이터 처리)을 비동기적(백그라운드)으로 실행할 때 사용.

```swift
DispatchQueue.global(qos: .background).async {
    // 백그라운드 작업 (무거운 연산)
    let result = complexCalculation()
    
    DispatchQueue.main.async {
        // 결과를 UI에 반영 (메인 큐에서 실행)
        self.label.text = "\(result)"
    }
}
```

<aside>
📎

### QOS(Quality of Service)란?

- 작업의 우선순위를 지정하는 속성.
- 작업의 우선순위에 따라 최적의 CPU 리소스를 할당하여 실행함.

---

| 우선순위 | QOS 레벨 | 사용 예시 |
| --- | --- | --- |
| 가장 높음 | .userInteractive | UI 즉각 반응 (애니메이션, 제스처) |
| 높음 | .userInitiated | 데이터 로딩 |
| 중간 | .utility | 백그라운드 작업(다운로드, 네트워킹) |
| 낮음 | .background | 사용자 모르게 실행되는 작업(데이터 동기화) |
</aside>

