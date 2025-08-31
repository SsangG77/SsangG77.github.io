---
title: URLSession이란
date: 2025-08-31 12:00:00 +0900
categories: [개념, Swift]
tags: [stream, buffer]
---

# URLSession의 기본 구조
- URLSession은 네트워크 요청을 처리하는 큰 컨테이너 객체이고, 그 안에서 개별 작업을 담당하는 것이 **URLSessionTask**

## URLSessionConfiguration
- 세션의 동작 방식을 정의 (캐시 정책, 쿠키 저장 여부, 타임아웃, 백그라운드 여부 등)
- 옵션이라 필요에 따라 사용가능함.
- 종류: `default`, `ephemeral`, `background(identifier:)`

1. `default`
캐시, 쿠키, keychain 인증 정보를 자동 저장/사용
일반적인 API 요청 시 가장 많이 사용
```
let config = URLSessionConfiguration.default
let session = URLSession(configuration: config)
```

2. `ephemeral` (임시 세션)
캐시, 쿠키, 인증 정보를 메모리에만 저장, 앱 종료 시 모두 삭제
프라이버시 강화된 통신에 적합
```
let config = URLSessionConfiguration.ephemeral
let session = URLSession(configuration: config)
```

3. `background`
앱이 종료되더라도 업로드/다운로드 지속
완료 시 시스템이 앱을 깨워 delegate 호출
큰 용량 파일 다운로드, 업로드에 사용
```
let config = URLSessionConfiguration.background(withIdentifier: "com.example.background")
let session = URLSession(configuration: config)
```

## URLSession
- 네트워크 요청을 관리하는 객체
- 실제로 Task를 생성하고 실행
- delegate를 두어 다운로드 진행률, 인증 처리, 백그라운드 완료 이벤트 등을 받을 수 있음

```
let url = URL(string: "https://jsonplaceholder.typicode.com/posts/1")!
let session = URLSession(configuration: .default)
let task = session.dataTask(with: url) { data, response, error in
    if let data = data {
        let json = try? JSONSerialization.jsonObject(with: data, options: [])
        print("응답 데이터: \(json ?? "")")
    }
}
task.resume()
```

## URLSessionTask
- 실제 네트워크 요청 단위
- 동작: 앱에서 요청 -> URLSession -> os레벨 네트워크 처리 -> 서버 -> 응답


### 종류 
1. dataTask: 메모리에 데이터 적재
- 용도: 작은 데이터(API, JSON, 텍스트) 요청
- 동작: 서버 응답을 메모리에 저장 후 한 번에 전달
```
let task = URLSession.shared.dataTask(with: URL(string:"https://example.com")!) { data, response, error in
    if let data = data {
        print("데이터 크기:", data.count)
    }
}
task.resume()
```

2. downloadTask: 파일 다운로드 (디스크 저장)
- 용도: 큰 파일 다운로드
- 동작: 서버에서 디스크에 직접 저장, 완료 후 임시 파일 URL 전달
```
let url = URL(string: "https://speed.hetzner.de/100MB.bin")!
let task = URLSession.shared.downloadTask(with: url) { tempURL, response, error in
    if let tempURL = tempURL {
        let destination = FileManager.default.temporaryDirectory.appendingPathComponent("100MB.bin")
        try? FileManager.default.moveItem(at: tempURL, to: destination)
        print("DownloadTask 완료, 저장 위치: \(destination)")
    }
}
task.resume()

```

3. uploadTask: 데이터 업로드 (파일/데이터 전송)
- 용도: 서버로 데이터/파일 업로드
- 동작: 파일이나 메모리 데이터를 서버로 전송
```
let fileURL = URL(fileURLWithPath: "/path/to/file.png")
var request = URLRequest(url: URL(string:"https://example.com/upload")!)
request.httpMethod = "POST"

let task = URLSession.shared.uploadTask(with: request, fromFile: fileURL) { data, response, error in
    if let data = data {
        print("서버 응답:", String(data: data, encoding: .utf8) ?? "")
    }
}
task.resume()
```



---

# 내부 동작 흐름

## 1. 요청 생성
URLRequest 생성 (URL, HTTPMethod, Header, Body 등 포함)
```
var request = URLRequest(url: URL(string: "https://example.com/api")!)
request.httpMethod = "GET"
```

## 2. Task 생성
```
session.dataTask(with: request) 같은 메서드로 URLSessionTask 객체를 만든다.
let task = URLSession.shared.dataTask(with: request) { data, response, error in
    if let data = data {
        print("데이터 크기: \(data.count) bytes")
    }
}
```

## 3. Task 실행
resume()을 호출해야 실제 네트워크 요청 시작
내부적으로는 CFNetwork / NSURLSessiond (애플 네트워킹 데몬)으로 요청이 전달
```
task.resume()
```
## 4. 네트워크 전송
OS 레벨에서 TCP/IP 소켓을 통해 서버와 통신
응답이 오면 Data가 조각(chunk) 단위로 들어옴

## 5. 응답 처리
콜백(completionHandler) 또는 delegate로 응답 전달
Data 혹은 임시 파일 경로(URL)가 결과 도착










