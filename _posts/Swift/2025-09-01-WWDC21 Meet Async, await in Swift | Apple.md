---
title: WWDC21: Meet Async/await in Swift | Apple
date: 2025-09-01 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, 비동기]
---

# 1. Completion handler를 사용하여 비동기 구현
- 이렇게 작성하면 비동기처리가 가능하지만 미묘한 버그가 끼어들 수 있음
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
