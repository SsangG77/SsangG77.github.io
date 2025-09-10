---
title: WWDC21, Meet Async/await in Swift (Apple)
date: 2025-09-01 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, 비동기]
---

# 1. Completion handler를 사용하여 비동기 구현
- 이렇게 작성하면 비동기처리가 가능하지만 미묘한 버그가 끼어들 수 있음
- 가독성이 좋지 않고 코드의 길이가 길어짐
```
func fetchThumbnail(for id: String, completion: @escaping (Result<UIImage, Error>) -> Void) {
  ...
  if let error = error {
    completion(.failure(error)) //
  } else if (response as? HTTPURLResponse)?.statusCode != 200 {
    completion(.failure(FetchError.badID)) //
  } else {
    guard let image = UIImage(data: data!) else {
      completion(.failure(FetchError.badImage)) //
      return
    }
    image.prepareThumbnail(of: CGSize(width: 40, height: 40)) {
      guard let thumbnail = thumbnail else {
        completion(.failure(FetchError.abdImage)) //
        return
      }
      completion(.success(thumbnail)) //
    }
  }
}
```

# 2. Async/await 사용
```
func fetchThumbnail(for id: String) async throws -> UIImage {
  let request = thumbnailURLRequest(for: id)
  let (data, response) = try await URLSession.shared.data(for: request)
  guard (response as? HTTPURLResponse)?.statusCode = 200 else { throw FetchError.badID }
  let maybeImage = UIImage(data: data)
  guard let thumbnail = await maybeImage?.thumbnail else { throw FetchError.badImage }
  return thumbnail
}
```

## 1. `data(for:)`
```
let (data, response) = try await URLSession.shared.data(for: request)
```
- data(for:)함수를 호출한 후 재빨리 일시중단되어 스레드가 차단 해제됨. 해제되면 fetchThumbnail함수로 돌아옴
- `try` 키워드가 있는 이유는 데이터 메서드가 "throws"로 표시되기 때문, 이때 오류가 발생하면 함수가 종료되고 해당 함수를 호출한 쪽에서 do-catch로 오류를 처리

## 2. `UIImage(data: data)`
```
let maybeImage = UIImage(data: data)
```
- 다운로드한 데이터로 UIImage 생성을 시도

## 3. `await maybe.thumbnail`
```
guard let thumbnail = await maybeImage?.thumbnail else { throw FetchError.badImage }
```
- 생성된 UIImage에서 썸네일을 가져오는 동안 다른 작업을 자유롭게 수행 가능

---

## 실행 순서

1. **함수 실행 시작**

   * `fetchThumbnail(for:)` 같은 async 함수가 1번 스레드에서 실행됨.

2. **await 표현식 도달**

   * 예: `let (data, response) = try await URLSession.shared.data(for: request)`
   * 함수 실행 **중단(suspend)**, 현재 스레드(1번)는 해제되어 다른 작업 수행 가능.

3. **비동기 작업 수행**

   * `data(for:)` 내부 네트워크 요청은 **OS 네트워크 I/O, 백그라운드 스레드/런루프**에서 진행.
   * 이 동안 함수 실행은 멈춰 있고, 호출한 스레드는 자유롭게 다른 코드 실행 가능.

4. **비동기 작업 완료**

   * 네트워크 응답이 오면 Swift 런타임이 함수 상태를 복원.
   * 함수 재개(resume)는 **같은 스레드일 수도, 다른 스레드일 수도 있음**.

5. **함수 실행 재개**

   * `guard` 검사, `UIImage` 생성 등 이후 코드가 이어서 실행됨.
   * 최종적으로 값 반환 또는 오류 throw.

> 핵심 요약:
> 함수는 await에서 멈추지만 스레드는 자유.
> 비동기 작업은 별도 스레드/시스템에서 진행.
> 함수 재개 시 스레드는 런타임이 결정.


> async/await를 사용하면 비동기 코드를 더 안전하고 짧게, 의도를 더 잘 반영할 수 있음


### extention으로 추가된 `thumbnail` 속성
- 위의 코드중 `await maybeImage?.thumbnail`라는 코드가 있는데 이는 `UIImage`의 `thumbnail`속성값을 비동기로 가져오는 부분
속성값도 비동기로 가져오는 경우도 있음

```
extension UIImage {
  var thumbnail: UIImage? {
    get async {
      let size = CGSize(width: 40, height: 40)
      return await self.byPreparingThumbnail(ofSize: size)
    }
  }
}
```

1. 속성 getter에 async 표시
2. 읽기 전용속성만 비동기 가능



### for 반복문에서 await 사용
```
for await id in staticImageIDsURL.lines {
  let thumbnail = await fetchThumbnail(for: id)
  collage.add(thumbnail)
}
let result = await collage.draw()
```

> `for await id in staticImageIDsURL.lines {`
> `staticImageIDsURL.lines`에서 라인 하나를 가져올때까지 기다렸다가 가져와지면 반복문 한번 실행

> `let thumbnail = await fetchThumbnail(for: id)`
> `fetchThumbnail(for: id)`는 id로 썸네일을 응답받는 함수

> `let result = await collage.draw()`
> `collage`에 전부 할당이 되면 비동기로 `draw()`실행하여 반환


# 동기 함수 & 비동기 함수가 동작할 때 일어나는 일

## 동기 함수
<img width="1722" height="297" alt="Image" src="https://github.com/user-attachments/assets/b9027db0-4d3a-4847-af27-c617cdec3703" />
```
func thumbnailURLRequest(for id: String) -> URLRequest {
  // ...
  return request
}
```
- 스레드가 함수를 호출하면 호출한 스레드는 함수가 끝날때까지 담당
- 동기 함수가 호출되면 스레드는 다른 스레드로 해당 작업을 넘기지 않고 해당 스레드가 작업을 수행하기 때문에 계속 점유되어짐
- 해당 작업은 함수 자체의 본문에 있을 수도 있고 해당 함수가 호출하는 다른 함수에 있을 수도 있음
- 결국 해당 함수는 값을 반환하거나 오류를 발생시켜 완료 -> 제어권이 해당 함수로 다시 넘어감


## 비동기 함수
<img width="1603" height="474" alt="Image" src="https://github.com/user-attachments/assets/3a7800de-3f30-46ad-8ad1-a7e3f885f8aa" />
```
func thumbnailURLRequest(for id: String) async -> URLRequest {
  // ...
  let (data, response) = try await URLSession.shared.data(for: request)
  // ...
  return request
}
```
- 비동기 함수가 시작되면 스레드에서 함수가 동작하다가 `await`를 만나면 일시 중단되고 그때 함수 상태는 런타임에 저장, 스레드에 대한 제어권은 런타임이 회수함
- 즉 비동기 함수가 일시 중단되면 현재 스레드는 다른 작업에 자유롭게 사용되고, 함수 실행은 런타임이 나중에 다시 스레드를 할당해 이어서 실행함.
- 함수가 일시정지되고 ***비동기 작업(네트워크 요청 등)은 시스템 레벨에서 처리됨***


# 중요 사항
### 1. 
  함수를 비동기로 표시하면 수가 일시 중단되도록 허용하는 것
  함수가 자신을 일시 정지하면 호출자도 일시 중단됨. 그러므로 호출자도 비동기적이어야 함

### 2. 
  비동기 함수에서 한 번 또는 여러 번 중단될 수 있는 위치를 나타내기 위해 `await`키워드 사용
  
### 3. 
  비동기 함수가 일시 중단되어도 스레드는 차단되지 않음 
  따라서 시스템은 다른 작업을 자유롭게 일정에 넣을 수 있음
  나중에 실행되는 작업이라도 먼저 실행 가능 즉, 함수가 일시 중단된 동안 앱의 상태가 크게 변경될 수 있음
  
### 4.
  비동기 함수가 재개되면 해당 함수에서 호출한 비동기 함수에서 반환된 결과가 원래 함수로 다시 흐르고, 실행은 중단된 지점에서 계속됨
  



# XCTest에서의 비동기

- 비동기 적용 전
```
class MockViewModelSpec: XCTestCase {
  func testFetchThumbnails() throws {
    let expectiation = XCTestExpectation(description: "mock thumbnails completsion")
    self.mockViewModel.fetchThumbnail(for: mockID) {
      XCTAssertEqual(result?.size, CGSize(width: 40, height: 40))
      expectation.fulfull()
    }
    wait(for: [expectation], timeout: 5.0)
  }
}
```

- 비동기 적용 후
```
class MockViewModelSpec: XCTestCase {
  func testFetchThumbnails() async throws {
    let result = self.mockViewModel.fetchThumbnail(for: mockID)
    XCTAssertEqual(result.size, CGSize(width: 40, height: 40))
  }
}
```

# SwiftUI에서의 비동기

- 비동기 적용 전
```
struct ThumbnailView: View {
  @ObservadObject var viewModel: ViewModel
  var post: Post
  @State private var image: UIImage?

  var body: some View {
    Image(uiImage: self.image ?? placeholder)
      .onAppear {
        self.viewModel.fetchThumbnail(for: post.id) {
          self.image = result
        }
      }
  }
}
```
- 비동기 적용 후
```
struct ThumbnailView: View {
  @ObservadObject var viewModel: ViewModel
  var post: Post
  @State private var image: UIImage?

  var body: some View {
    Image(uiImage: self.image ?? placeholder)
      .onAppear {
        Task {
          self.image = try? await self.viewModel.fetchThumbnail(for: post.id)
        }
      }
  }
}
```
> `Task`는 작업을 클로저에 패키징하여 시스템으로 전송하여 다음에 사용 가능한 스레드에서 즉시 실행됩니다.
> 이는 글로벌 디스패치 큐의 비동기 함수와 같습니다.
> 여기서 가장 큰 장점은 비동기 코드를 동기화 컨텍스트 내부에서 호출할 수 있다는 것입니다.





