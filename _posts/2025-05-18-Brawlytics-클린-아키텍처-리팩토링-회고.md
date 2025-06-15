---
title: 
date: 2025-06-12 12:00:00 +0900
categories: [회고록]
tags: [Swift, Brawlytics]
---

# 📌 해당 앱은 모바일 게임 브롤스타즈 유저를 위한 재화 조회 앱이다.

## 리팩토링을 결정한 이유

해당 앱의 현재 기능은 

- 재화 조회
- API로 요청되지 않는 아이템(하이퍼차지) 체크 기능 - 해당 아이템도 재화 조회를 위한 서브 기능

이 두가지의 기능으로 구성되어 있다. 

하지만 앱의 기능을 좀 더 확장하고 나아가서는 OP.GG같은 서비스로 발전시키기 위해서는 우선 관심사를 분리시켜서 기능 확장, 유지보수를 용이하게 하기 위함이다.

</br>

# 클린 아키텍처?
클린 아키텍처란 “변경에 유연하고 테스트하기 쉬운 구조”를 만들기 위한 소프트웨어 설계 원칙이다. 핵심 목표는 의존성의 방향을 안쪽(비즈니스 로직)으로 향하게 유지하는 것이라고 한다.

사실 말로 들어서는 무슨 말인지 모르겠다… 안쪽으로 향한다는게 무슨 말이지. 예제 코드로 이해하는 것이 빠를 것 같다.


</br>


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

해당 코드를 보니 View → ViewModel → UseCase → Repository → Entity 의 방향으로 의존한다는 것을 알 수 있다.

</br>

### 근데 왜 항상 바깥 → 안쪽 방향으로 의존 방향이 흐르도록 해야 할까?


### 1. 비즈니스 로직 보호


만약 서로 의존하거나 방향이 반대로 되어있으면 어떻게 될까? UI, 네트워크, DB는 변화가 잦기 때문에 외부가 변하면 메인 로직도 변경해야 할 수도 있다. 


그렇기 때문에 의존성 방향을 바깥에서 안쪽으로 흐르도록 해야 한다.


![Image](https://github.com/user-attachments/assets/9d6a7f20-ab2d-4d9a-bb0c-b130e971c5f4)




