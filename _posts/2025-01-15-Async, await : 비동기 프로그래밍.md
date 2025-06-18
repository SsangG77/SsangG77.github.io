---
title: "Async / await : 비동기 프로그래밍"
date: 2025-01-15 12:00:00 +0900
categories: [개념, Swift]
tags: []
---



# Swift Async/Await 핵심 가이드

Swift 5.5에서 도입된 async/await는 복잡한 콜백 지옥을 해결하고 직관적인 비동기 코드를 작성할 수 있게 해줍니다.

## async의 역할

`async` 키워드는 **함수가 비동기적으로 실행될 수 있음**을 선언합니다. 이는 함수가 실행 중간에 멈출 수 있고, 다른 작업에게 실행 기회를 줄 수 있다는 의미입니다.

```swift
// 기존 콜백 방식
func fetchUser(completion: @escaping (User?) -> Void) {
    URLSession.shared.dataTask(with: url) { data, _, _ in
        // 복잡한 중첩 구조
        let user = parseUser(data)
        completion(user)
    }.resume()
}

// async 함수
func fetchUser() async throws -> User {
    let (data, _) = try await URLSession.shared.data(from: url)
    return parseUser(data)
}
```

**주요 특징:**
- 컴파일 타임에 비동기 호출 규칙을 강제
- 함수 실행 중 여러 지점에서 중단 가능
- 다른 작업들이 실행될 기회를 제공

## await의 역할

`await` 키워드는 **비동기 함수의 결과를 기다리는 지점**을 표시합니다. 이 지점에서 현재 함수 실행이 멈추고, 다른 작업이 실행될 수 있습니다.

```swift
func loadUserData() async throws {
    print("데이터 로딩 시작")
    
    // 여기서 실행이 중단되고 다른 작업 실행 가능
    let user = try await fetchUser()
    
    // 위 작업 완료 후 실행 재개
    print("사용자: \(user.name)")
}
```

**핵심 동작:**
- 실행 컨텍스트를 보존하면서 일시 중단
- 비동기 작업 완료 후 정확히 중단된 지점에서 재개
- 에러를 자연스럽게 전파

**병렬 처리:**
```swift
func fetchAllData() async throws -> (User, [Post]) {
    // 순차 실행 (느림)
    let user = try await fetchUser()
    let posts = try await fetchPosts()
    
    // 병렬 실행 (빠름)
    async let userTask = fetchUser()
    async let postsTask = fetchPosts()
    
    return try await (userTask, postsTask)
}
```

## Task의 역할

`Task`는 **비동기 작업의 실행 단위**입니다. 작업의 생명주기를 관리하고, 취소와 우선순위를 제어할 수 있습니다.

```swift
// 기본 Task 생성
Task {
    let result = await someAsyncWork()
    print(result)
}

// Task 참조 보관
let task = Task {
    return await heavyComputation()
}

// 나중에 결과 사용
let result = await task.value
```

**주요 기능:**

**1. 우선순위 설정**
```swift
Task(priority: .high) {
    await criticalWork()
}

Task(priority: .background) {
    await backgroundSync()
}
```

**2. 취소 처리**
```swift
class ViewController {
    private var dataTask: Task<Void, Never>?
    
    func loadData() {
        dataTask?.cancel() // 기존 작업 취소
        
        dataTask = Task {
            do {
                let data = try await fetchData()
                await updateUI(data)
            } catch is CancellationError {
                print("작업 취소됨")
            }
        }
    }
}
```

**3. TaskGroup으로 동적 병렬 처리**
```swift
func downloadImages(_ urls: [URL]) async throws -> [UIImage] {
    try await withThrowingTaskGroup(of: UIImage.self) { group in
        var images: [UIImage] = []
        
        for url in urls {
            group.addTask {
                try await downloadImage(from: url)
            }
        }
        
        for try await image in group {
            images.append(image)
        }
        
        return images
    }
}
```

## 실무 활용 패턴

### ViewModel과 통합
```swift
@MainActor
class UserViewModel: ObservableObject {
    @Published var user: User?
    @Published var isLoading = false
    
    private var loadTask: Task<Void, Never>?
    
    func loadUser() {
        loadTask?.cancel()
        
        loadTask = Task {
            isLoading = true
            
            do {
                user = try await userService.fetchUser()
            } catch {
                print("에러: \(error)")
            }
            
            isLoading = false
        }
    }
    
    deinit {
        loadTask?.cancel()
    }
}
```

### 에러 처리와 재시도
```swift
func robustNetworkCall<T>(
    operation: () async throws -> T,
    maxRetries: Int = 3
) async throws -> T {
    for attempt in 0..<maxRetries {
        do {
            return try await operation()
        } catch {
            if attempt == maxRetries - 1 { throw error }
            try await Task.sleep(nanoseconds: 1_000_000_000)
        }
    }
    fatalError("Unreachable")
}
```

## 정리

- **async**: 함수가 비동기 실행 가능함을 선언
- **await**: 비동기 결과를 기다리며 실행 양보
- **Task**: 비동기 작업의 실행 단위, 생명주기 관리

이 세 가지 요소를 조합하면 복잡한 비동기 로직을 간단하고 안전하게 구현할 수 있습니다.
