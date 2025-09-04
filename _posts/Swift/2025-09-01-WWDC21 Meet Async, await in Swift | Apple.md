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






