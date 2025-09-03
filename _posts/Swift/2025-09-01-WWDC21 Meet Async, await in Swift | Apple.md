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

1. data(for:)
```
let (data, response) = try await URLSession.shared.data(for: request)
```
- data(for:)함수를 호출한 후 재빨리 일시중단되어 스레드가 차단 해제됨. 해제되면 fetchThumbnail함수로 돌아옴
- `try` 키워드가 있는 이유는 데이터 메서드가 "throws"로 표시되기 때문, 이때 오류가 발생하면 함수가 종료되고 해당 함수를 호출한 쪽에서 do-catch로 오류를 처리

