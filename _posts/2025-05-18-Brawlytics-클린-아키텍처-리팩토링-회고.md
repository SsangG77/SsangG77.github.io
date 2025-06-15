---
title: 
date: 2025-06-12 12:00:00 +0900
categories: [회고록]
tags: [Swift, Brawlytics]
---

# 📌 해당 앱은 모바일 게임 브롤스타즈 유저를 위한 재화 조회 앱이다.

---

## 리팩토링을 결정한 이유

<aside>

해당 앱의 현재 기능은 

- 재화 조회
- API로 요청되지 않는 아이템(하이퍼차지) 체크 기능 - 해당 아이템도 재화 조회를 위한 서브 기능

이 두가지의 기능으로 구성되어 있다. 

하지만 앱의 기능을 좀 더 확장하고 나아가서는 OP.GG같은 서비스로 발전시키기 위해서는 우선 관심사를 분리시켜서 기능 확장, 유지보수를 용이하게 하기 위함이다.

</aside>

## 클린 아키텍처?

<aside>

클린 아키텍처란 **“변경에 유연하고 테스트하기 쉬운 구조”**를 만들기 위한 소프트웨어 설계 원칙이다. 핵심 목표는 **의존성의 방향을 안쪽(비즈니스 로직)으로 향하게 유지**하는 것이다.라고 한다.

사실 말로 들어서는 무슨 말인지 모르겠다… 안쪽으로 향한다는게 무슨 말이지. 예제 코드로 이해하는 것이 빠를 것 같다.

---

## ✅ 예시 (간단한 검색 기능)

### Entity

```swift
struct Brawler: Decodable {
    let id: Int
    let name: String
}
```

### UseCase

```swift
protocol SearchBrawlerUseCase {
    func execute(query: String, completion: @escaping ([Brawler]) -> Void)
}

class SearchBrawlerUseCaseImpl: SearchBrawlerUseCase {
    private let repository: BrawlerRepository

    init(repository: BrawlerRepository) {
        self.repository = repository
    }

    func execute(query: String, completion: @escaping ([Brawler]) -> Void) {
        repository.search(query: query, completion: completion)
    }
}
```

### Repository

```swift
protocol BrawlerRepository {
    func search(query: String, completion: @escaping ([Brawler]) -> Void)
}

class BrawlerRepositoryImpl: BrawlerRepository {
    func search(query: String, completion: @escaping ([Brawler]) -> Void) {
        // URLSession 등 실제 API 호출
    }
}
```

### ViewModel

```swift
class BrawlerSearchViewModel: ObservableObject {
    @Published var results: [Brawler] = []

    private let searchUseCase: SearchBrawlerUseCase

    init(searchUseCase: SearchBrawlerUseCase) {
        self.searchUseCase = searchUseCase
    }

    func search(_ text: String) {
        searchUseCase.execute(query: text) { [weak self] brawlers in
            DispatchQueue.main.async {
                self?.results = brawlers
            }
        }
    }
}
```

---

해당 코드를 보니 View → ViewModel → UseCase → Repository → Entity 의 방향으로 의존한다는 것을 알 수 있다.

### 근데 왜 항상 바깥 → 안쪽 방향으로 의존 방향이 흐르도록 해야 할까?

### 1. 비즈니스 로직 보호

만약 서로 의존하거나 방향이 반대로 되어있으면 어떻게 될까? UI, 네트워크, DB는 변화가 잦기 때문에 외부가 변하면 메인 로직도 변경해야 할 수도 있다. 

그렇기 때문에 의존성 방향을 바깥에서 안쪽으로 흐르도록 해야 한다.

![image.png](attachment:fe5d287d-2dcd-4dd3-b639-32b56f6eba7c:image.png)

### 2. 테스트 용이성

Mock 객체로 쉽게 대체 가능하고 단위 테스트가 수월해진다.

### 3. **관심사 분리 (Separation of Concerns)**

- UI는 UI만
- 로직은 로직만
- 저장은 저장만

이렇게 책임을 분리하면 → 유지보수성과 확장성 증가한다.

### 4. **의존성 역전 원칙 (DIP)**

- 상위 계층(UseCase)이 하위 구현(예: APIService)이 아닌 **인터페이스**에만 의존하게 한다.
- 구체적인 구현은 바깥에 감춰지고 → 안쪽은 안정적인 추상화만 바라본다.
</aside>

# 회고

<aside>
📖

> **다른글**
> 
> 
> [제목 없음](https://www.notion.so/191ee035a51f80f693d0e2582484a734?pvs=21)
> 
</aside>

해당 설계 원칙을 바탕으로 진행하기 위해서 일단 프로젝트 전체 구조를 파악하는 것이 먼저라고 생각해서 구조를 파악하기 시작했다.

기존에 MVVM 방식으로 설계되어 있던터라 구조를 파악하는 것이 크게 어렵지는 않았지만 아직 클린 아키텍처에 대한 개념이 흐릿하게만 있었기 때문에 어디서부터 시작해야 할지 막막했다.

그래서 하나의 뷰모델을 먼저 타겟으로 삼고 그것을 천천히 뜯어가면서 작업을 진행했다. 해당 클래스를 어떤 역할을 담당하는지 파악하고 1차적으로 viewModel과 useCase로 분리하였다. 한번 분리를 하고 나니 그 다음은 또 어떻게 관심사를 나눌지 좀 더 선명하게 보였던 것 같다.

나눈 요소들을 또 다시 repository와 dataSource로 나누었고 의존성 역전 원칙을 지키기 위해 서로의 의존성을 연결하니 하나의 구조가 완성이 되었다.

그렇게 다른 객체들도 관심사를 분리하면서 역할이 일치하는 것들은 통합하기도 하며 재구조화를 진행하면서 클린 아키텍처의 개념이 훨씬 선명하게 다가왔다. 원래 클린 아키텍처를 이론적인 것만 배울때는 ‘굳이 이렇게까지 나눠야 하나?’라고 생각을 했지만 관심사들을 분리하고 나니 왜 이런 구조가 필요한지에 대해 체감하게 되었고, 테스트 코드를 작성하기도 수월한 이유를 너무 잘 알게 되었다.

그리고 앞으로 이 앱에 기능을 더 추가할 공간들이 보여지는 듯한 느낌이 들어서 프로젝트의 구조가 얼마나 중요한지 꺠닫게 되었다.
