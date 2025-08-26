---
title: Alamofire에 대해서 알아보자
date: 2025-08-26 12:00:00 +0900
categories: [Swift]
tags: [라이브러리, Alamofire]
---

## 개요

Alamofire는 Swift로 작성된 HTTP 네트워킹 라이브러리로, Apple의 URLSession과 Foundation 네트워킹을 기반으로 구축된 고급 라이브러리. 
URLSession의 복잡성을 추상화하여 더 간단하고 직관적인 API를 제공

## 설치 방법
`https://github.com/Alamofire/Alamofire.git` 주소를 Xcode file -> Add Package Dependencies로 추가


## 핵심 아키텍처

### 주요 컴포넌트

**Session**
- 모든 네트워킹 작업의 중심점
- URLSession을 래핑하여 설정과 관리 담당
- 기본 세션(`AF`)과 커스텀 세션 생성 가능

**Request 타입들**
- `DataRequest`: 일반적인 HTTP 요청
- `DownloadRequest`: 파일 다운로드 요청
- `UploadRequest`: 파일 업로드 요청
- `DataStreamRequest`: 스트리밍 요청

**Response 처리**
- 응답 검증, 직렬화, 에러 처리 담당
- JSON, String, Data 등 다양한 형태로 응답 파싱

### 내부 동작 원리

1. **Request 생성**: URL과 파라미터를 바탕으로 URLRequest 객체 생성
2. **Session 처리**: URLSession을 통해 실제 네트워크 요청 실행
3. **Response 검증**: 상태 코드, 콘텐츠 타입 등 검증
4. **데이터 직렬화**: 응답 데이터를 원하는 형태로 변환
5. **Completion Handler 실행**: 결과를 메인 큐 또는 지정된 큐에서 처리

## 기본 HTTP 요청

### GET 요청
```swift
import Alamofire

// 기본 GET 요청
AF.request("https://httpbin.org/get")
    .response { response in
        debugPrint(response)
    }

// JSON 응답 처리
AF.request("https://jsonplaceholder.typicode.com/users")
    .responseJSON { response in
        switch response.result {
        case .success(let value):
            print("JSON: \(value)")
        case .failure(let error):
            print("Error: \(error)")
        }
    }

// Codable로 직접 파싱
struct User: Codable {
    let id: Int
    let name: String
    let email: String
}

AF.request("https://jsonplaceholder.typicode.com/users/1")
    .responseDecodable(of: User.self) { response in
        switch response.result {
        case .success(let user):
            print("User: \(user.name)")
        case .failure(let error):
            print("Error: \(error)")
        }
    }
```

### POST 요청
```swift
// JSON 파라미터
let parameters: [String: Any] = [
    "name": "John Doe",
    "email": "john@example.com",
    "age": 30
]

AF.request("https://httpbin.org/post",
           method: .post,
           parameters: parameters,
           encoding: JSONEncoding.default)
    .responseJSON { response in
        print(response.result)
    }

// URL 인코딩 파라미터
AF.request("https://httpbin.org/post",
           method: .post,
           parameters: parameters,
           encoding: URLEncoding.default)
    .responseString { response in
        print(response.result)
    }
```

### PUT, PATCH, DELETE 요청
```swift
// PUT 요청
AF.request("https://jsonplaceholder.typicode.com/users/1",
           method: .put,
           parameters: parameters,
           encoding: JSONEncoding.default)
    .responseJSON { response in
        print(response.result)
    }

// PATCH 요청
AF.request("https://jsonplaceholder.typicode.com/users/1",
           method: .patch,
           parameters: ["name": "Updated Name"],
           encoding: JSONEncoding.default)
    .responseJSON { response in
        print(response.result)
    }

// DELETE 요청
AF.request("https://jsonplaceholder.typicode.com/users/1",
           method: .delete)
    .response { response in
        print("Status Code: \(response.response?.statusCode ?? 0)")
    }
```

## 고급 기능

### 헤더 설정
```swift
// 기본 헤더
let headers: HTTPHeaders = [
    "Authorization": "Bearer your-token",
    "Content-Type": "application/json",
    "Accept": "application/json"
]

AF.request("https://api.example.com/data",
           headers: headers)
    .responseJSON { response in
        print(response.result)
    }

// 동적 헤더 추가
var dynamicHeaders = HTTPHeaders()
dynamicHeaders.add(name: "X-API-Key", value: "your-api-key")
dynamicHeaders.add(name: "X-Request-ID", value: UUID().uuidString)
```

### 파라미터 인코딩

```swift
// URL 파라미터 (쿼리 스트링)
let urlParameters = ["page": 1, "limit": 20]
AF.request("https://api.example.com/users",
           parameters: urlParameters,
           encoding: URLEncoding.queryString)

// JSON 바디
let jsonParameters = ["user": ["name": "John", "age": 30]]
AF.request("https://api.example.com/users",
           method: .post,
           parameters: jsonParameters,
           encoding: JSONEncoding.default)

// 커스텀 인코딩
struct CustomParameterEncoding: ParameterEncoding {
    func encode(_ urlRequest: URLRequestConvertible, 
                with parameters: Parameters?) throws -> URLRequest {
        var urlRequest = try urlRequest.asURLRequest()
        // 커스텀 인코딩 로직
        return urlRequest
    }
}
```

### 응답 검증
```swift
AF.request("https://httpbin.org/status/200")
    .validate(statusCode: 200..<300)
    .validate(contentType: ["application/json"])
    .responseJSON { response in
        switch response.result {
        case .success:
            print("Validation Successful")
        case .failure(let error):
            print("Validation Failed: \(error)")
        }
    }

// 커스텀 검증
AF.request("https://api.example.com/data")
    .validate { request, response, data in
        // 커스텀 검증 로직
        if response.statusCode == 200 {
            return .success(Void())
        } else {
            return .failure(AFError.responseValidationFailed(reason: .unacceptableStatusCode(code: response.statusCode)))
        }
    }
    .responseJSON { response in
        print(response.result)
    }
```

### 파일 업로드
```swift
// 단일 파일 업로드
let fileURL = Bundle.main.url(forResource: "image", withExtension: "jpg")!

AF.upload(fileURL, to: "https://httpbin.org/post")
    .uploadProgress { progress in
        print("Upload Progress: \(progress.fractionCompleted)")
    }
    .responseJSON { response in
        print(response.result)
    }

// 멀티파트 업로드
AF.upload(multipartFormData: { multipartFormData in
    multipartFormData.append(fileURL, withName: "file")
    multipartFormData.append("John Doe".data(using: .utf8)!, withName: "name")
}, to: "https://httpbin.org/post")
.uploadProgress { progress in
    print("Upload Progress: \(progress.fractionCompleted)")
}
.responseJSON { response in
    print(response.result)
}

// 데이터 업로드
let imageData = UIImage(named: "profile")?.jpegData(compressionQuality: 0.8)
AF.upload(imageData!, to: "https://httpbin.org/post")
    .responseJSON { response in
        print(response.result)
    }
```

### 파일 다운로드
```swift
// 기본 다운로드
AF.download("https://httpbin.org/image/png")
    .downloadProgress { progress in
        print("Download Progress: \(progress.fractionCompleted)")
    }
    .response { response in
        if let data = response.value {
            let image = UIImage(data: data)
            // 이미지 처리
        }
    }

// 특정 위치에 다운로드
let destination: DownloadRequest.Destination = { _, _ in
    let documentsURL = FileManager.default.urls(for: .documentDirectory, 
                                              in: .userDomainMask)[0]
    let fileURL = documentsURL.appendingPathComponent("downloaded_file.png")
    return (fileURL, [.removePreviousFile, .createIntermediateDirectories])
}

AF.download("https://httpbin.org/image/png", to: destination)
    .response { response in
        if response.error == nil, let imagePath = response.fileURL?.path {
            print("File downloaded to: \(imagePath)")
        }
    }

// 이어받기 다운로드
let resumeData: Data? = // 이전에 저장된 resume data
if let resumeData = resumeData {
    AF.download(resumingWith: resumeData)
        .response { response in
            print("Resume download completed")
        }
}
```

## 인증 처리

### Basic Authentication
```swift
let user = "username"
let password = "password"

AF.request("https://httpbin.org/basic-auth/\(user)/\(password)")
    .authenticate(username: user, password: password)
    .responseJSON { response in
        print(response.result)
    }
```

### Bearer Token
```swift
let token = "your-bearer-token"

AF.request("https://api.example.com/protected",
           headers: ["Authorization": "Bearer \(token)"])
    .responseJSON { response in
        print(response.result)
    }
```

### OAuth 2.0
```swift
class OAuth2Handler: AuthenticationCredential {
    let accessToken: String
    let refreshToken: String
    let expiration: Date
    
    var requiresRefresh: Bool {
        Date(timeIntervalSinceNow: 60) > expiration
    }
    
    init(accessToken: String, refreshToken: String, expiration: Date) {
        self.accessToken = accessToken
        self.refreshToken = refreshToken
        self.expiration = expiration
    }
}

let credential = OAuth2Handler(accessToken: "access-token",
                              refreshToken: "refresh-token",
                              expiration: Date(timeIntervalSinceNow: 3600))

let authenticator = OAuth2Authenticator()
let interceptor = AuthenticationInterceptor(authenticator: authenticator,
                                          credential: credential)

AF.request("https://api.example.com/protected",
           interceptor: interceptor)
    .responseJSON { response in
        print(response.result)
    }
```

## 세션 관리

### 커스텀 세션 생성
```swift
// 기본 설정 커스터마이징
let configuration = URLSessionConfiguration.default
configuration.timeoutIntervalForRequest = 30
configuration.timeoutIntervalForResource = 60
configuration.httpAdditionalHeaders = ["User-Agent": "MyApp/1.0"]

let session = Session(configuration: configuration)

session.request("https://api.example.com/data")
    .responseJSON { response in
        print(response.result)
    }
```

### 인터셉터 사용
```swift
// 요청 어댑터
class APIKeyAdapter: RequestAdapter {
    private let apiKey: String
    
    init(apiKey: String) {
        self.apiKey = apiKey
    }
    
    func adapt(_ urlRequest: URLRequest, 
               for session: Session, 
               completion: @escaping (Result<URLRequest, Error>) -> Void) {
        var urlRequest = urlRequest
        urlRequest.setValue(apiKey, forHTTPHeaderField: "X-API-Key")
        completion(.success(urlRequest))
    }
}

// 요청 재시도
class RetryPolicy: RequestRetrier {
    func retry(_ request: Request, 
               for session: Session, 
               dueTo error: Error, 
               completion: @escaping (RetryResult) -> Void) {
        if let response = request.task?.response as? HTTPURLResponse,
           response.statusCode == 401 {
            completion(.retryWithDelay(1.0))
        } else {
            completion(.doNotRetry)
        }
    }
}

let adapter = APIKeyAdapter(apiKey: "your-api-key")
let retrier = RetryPolicy()
let interceptor = Interceptor(adapter: adapter, retrier: retrier)

let session = Session(interceptor: interceptor)
```

## 응답 처리 및 직렬화

### 커스텀 Response Serializer
```swift
extension DataRequest {
    @discardableResult
    func responseCustom<T: Decodable>(
        of type: T.Type,
        queue: DispatchQueue = .main,
        completionHandler: @escaping (AFDataResponse<T>) -> Void
    ) -> Self {
        return responseDecodable(of: type, queue: queue) { response in
            // 커스텀 처리 로직
            completionHandler(response)
        }
    }
}

// 사용 예
struct APIResponse<T: Codable>: Codable {
    let success: Bool
    let data: T
    let message: String?
}

AF.request("https://api.example.com/users")
    .responseCustom(of: APIResponse<[User]>.self) { response in
        switch response.result {
        case .success(let apiResponse):
            if apiResponse.success {
                print("Users: \(apiResponse.data)")
            } else {
                print("API Error: \(apiResponse.message ?? "Unknown error")")
            }
        case .failure(let error):
            print("Network Error: \(error)")
        }
    }
```

### Combine 지원
```swift
import Combine

class NetworkService {
    private var cancellables = Set<AnyCancellable>()
    
    func fetchUsers() -> AnyPublisher<[User], AFError> {
        return AF.request("https://jsonplaceholder.typicode.com/users")
            .publishDecodable(type: [User].self)
            .value()
            .eraseToAnyPublisher()
    }
    
    func loadData() {
        fetchUsers()
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { completion in
                    switch completion {
                    case .finished:
                        print("Request completed successfully")
                    case .failure(let error):
                        print("Request failed: \(error)")
                    }
                },
                receiveValue: { users in
                    print("Received \(users.count) users")
                }
            )
            .store(in: &cancellables)
    }
}
```

## Router 패턴

### API Router 구현
```swift
enum APIRouter: URLRequestConvertible {
    case getUsers
    case getUser(id: Int)
    case createUser(parameters: Parameters)
    case updateUser(id: Int, parameters: Parameters)
    case deleteUser(id: Int)
    
    static let baseURLString = "https://jsonplaceholder.typicode.com"
    
    var method: HTTPMethod {
        switch self {
        case .getUsers, .getUser:
            return .get
        case .createUser:
            return .post
        case .updateUser:
            return .put
        case .deleteUser:
            return .delete
        }
    }
    
    var path: String {
        switch self {
        case .getUsers:
            return "/users"
        case .getUser(let id):
            return "/users/\(id)"
        case .createUser:
            return "/users"
        case .updateUser(let id, _):
            return "/users/\(id)"
        case .deleteUser(let id):
            return "/users/\(id)"
        }
    }
    
    var parameters: Parameters? {
        switch self {
        case .createUser(let parameters), .updateUser(_, let parameters):
            return parameters
        default:
            return nil
        }
    }
    
    func asURLRequest() throws -> URLRequest {
        let url = try APIRouter.baseURLString.asURL()
        var urlRequest = URLRequest(url: url.appendingPathComponent(path))
        urlRequest.httpMethod = method.rawValue
        
        switch self {
        case .getUsers, .getUser, .deleteUser:
            urlRequest = try URLEncoding.default.encode(urlRequest, with: parameters)
        case .createUser, .updateUser:
            urlRequest = try JSONEncoding.default.encode(urlRequest, with: parameters)
        }
        
        return urlRequest
    }
}

// Router 사용
class APIService {
    func getUsers(completion: @escaping ([User]?) -> Void) {
        AF.request(APIRouter.getUsers)
            .responseDecodable(of: [User].self) { response in
                completion(response.value)
            }
    }
    
    func createUser(name: String, email: String, completion: @escaping (User?) -> Void) {
        let parameters: Parameters = ["name": name, "email": email]
        AF.request(APIRouter.createUser(parameters: parameters))
            .responseDecodable(of: User.self) { response in
                completion(response.value)
            }
    }
}
```

## 에러 처리

### 상세한 에러 처리
```swift
AF.request("https://api.example.com/data")
    .responseJSON { response in
        switch response.result {
        case .success(let value):
            print("Success: \(value)")
        case .failure(let error):
            if let afError = error.asAFError {
                switch afError {
                case .invalidURL(let url):
                    print("Invalid URL: \(url)")
                case .parameterEncodingFailed(let reason):
                    print("Parameter encoding failed: \(reason)")
                case .multipartEncodingFailed(let reason):
                    print("Multipart encoding failed: \(reason)")
                case .responseValidationFailed(let reason):
                    print("Response validation failed: \(reason)")
                case .responseSerializationFailed(let reason):
                    print("Response serialization failed: \(reason)")
                default:
                    print("Other AF Error: \(afError)")
                }
            } else {
                print("Unknown error: \(error)")
            }
        }
    }
```

### 커스텀 에러 타입
```swift
enum APIError: Error, LocalizedError {
    case invalidResponse
    case noData
    case decodingError
    case serverError(Int)
    case networkError(Error)
    
    var errorDescription: String? {
        switch self {
        case .invalidResponse:
            return "Invalid response"
        case .noData:
            return "No data received"
        case .decodingError:
            return "Failed to decode response"
        case .serverError(let code):
            return "Server error with code: \(code)"
        case .networkError(let error):
            return "Network error: \(error.localizedDescription)"
        }
    }
}

extension DataRequest {
    func responseAPI<T: Codable>(
        of type: T.Type,
        completion: @escaping (Result<T, APIError>) -> Void
    ) -> Self {
        return responseData { response in
            switch response.result {
            case .success(let data):
                do {
                    let decodedObject = try JSONDecoder().decode(type, from: data)
                    completion(.success(decodedObject))
                } catch {
                    completion(.failure(.decodingError))
                }
            case .failure(let error):
                if let statusCode = response.response?.statusCode, statusCode >= 400 {
                    completion(.failure(.serverError(statusCode)))
                } else {
                    completion(.failure(.networkError(error)))
                }
            }
        }
    }
}
```

## 캐싱

### URL 캐시 설정
```swift
let cache = URLCache(memoryCapacity: 20 * 1024 * 1024, // 20MB
                     diskCapacity: 100 * 1024 * 1024,  // 100MB
                     diskPath: "alamofire_cache")

let configuration = URLSessionConfiguration.default
configuration.urlCache = cache
configuration.requestCachePolicy = .returnCacheDataElseLoad

let session = Session(configuration: configuration)

session.request("https://api.example.com/data")
    .cacheResponse(using: ResponseCacher.cache)
    .responseJSON { response in
        print(response.result)
    }
```

## 성능 최적화

### 연결 풀링 및 재사용
```swift
let configuration = URLSessionConfiguration.default
configuration.httpMaximumConnectionsPerHost = 5
configuration.urlCache = URLCache(memoryCapacity: 50 * 1024 * 1024,
                                  diskCapacity: 200 * 1024 * 1024)

let session = Session(configuration: configuration)
```

### 요청 취소
```swift
let request = AF.request("https://api.example.com/data")
    .responseJSON { response in
        print(response.result)
    }

// 5초 후 요청 취소
DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
    request.cancel()
}
```

### 동시 요청 제한
```swift
class RequestManager {
    private let queue = OperationQueue()
    
    init() {
        queue.maxConcurrentOperationCount = 3
    }
    
    func performRequest(url: String, completion: @escaping (Result<Data, Error>) -> Void) {
        queue.addOperation {
            AF.request(url)
                .responseData { response in
                    DispatchQueue.main.async {
                        completion(response.result.mapError { $0 as Error })
                    }
                }
        }
    }
}
```

## 실제 사용 예제

### MVVM 패턴과 함께 사용
```swift
import Combine

class UserViewModel: ObservableObject {
    @Published var users: [User] = []
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    private var cancellables = Set<AnyCancellable>()
    private let apiService = APIService()
    
    func loadUsers() {
        isLoading = true
        errorMessage = nil
        
        apiService.fetchUsers()
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] users in
                    self?.users = users
                }
            )
            .store(in: &cancellables)
    }
}

class APIService {
    func fetchUsers() -> AnyPublisher<[User], Error> {
        return AF.request(APIRouter.getUsers)
            .publishDecodable(type: [User].self)
            .value()
            .mapError { $0 as Error }
            .eraseToAnyPublisher()
    }
}
```

### 이미지 다운로드 및 캐싱
```swift
class ImageLoader: ObservableObject {
    @Published var image: UIImage?
    @Published var isLoading = false
    
    private static let cache = NSCache<NSString, UIImage>()
    
    func loadImage(from url: String) {
        let cacheKey = NSString(string: url)
        
        if let cachedImage = Self.cache.object(forKey: cacheKey) {
            self.image = cachedImage
            return
        }
        
        isLoading = true
        
        AF.request(url)
            .responseData { [weak self] response in
                DispatchQueue.main.async {
                    self?.isLoading = false
                    
                    switch response.result {
                    case .success(let data):
                        if let image = UIImage(data: data) {
                            Self.cache.setObject(image, forKey: cacheKey)
                            self?.image = image
                        }
                    case .failure(let error):
                        print("Image loading failed: \(error)")
                    }
                }
            }
    }
}
```

## 모니터링 및 로깅

### EventMonitor 사용
```swift
class NetworkLogger: EventMonitor {
    let queue = DispatchQueue(label: "network.logger")
    
    func requestDidResume(_ request: Request) {
        print("🚀 Request started: \(request.description)")
    }
    
    func request<Value>(_ request: DataRequest, didParseResponse response: DataResponse<Value, AFError>) {
        print("📥 Response received:")
        print("   URL: \(request.request?.url?.absoluteString ?? "Unknown")")
        print("   Status Code: \(response.response?.statusCode ?? 0)")
        print("   Duration: \(response.metrics?.taskInterval.duration ?? 0)s")
        
        if let error = response.error {
            print("   ❌ Error: \(error)")
        } else {
            print("   ✅ Success")
        }
    }
}

let logger = NetworkLogger()
let session = Session(eventMonitors: [logger])
```

## 테스팅

### Mock 세션 생성
```swift
class MockSession {
    static func create(with data: Data?, statusCode: Int = 200) -> Session {
        let configuration = URLSessionConfiguration.af.default
        configuration.protocolClasses = [MockURLProtocol.self]
        
        MockURLProtocol.responseData = data
        MockURLProtocol.statusCode = statusCode
        
        return Session(configuration: configuration)
    }
}

class MockURLProtocol: URLProtocol {
    static var responseData: Data?
    static var statusCode: Int = 200
    
    override class func canInit(with request: URLRequest) -> Bool {
        return true
    }
    
    override class func canonicalRequest(for request: URLRequest) -> URLRequest {
        return request
    }
    
    override func startLoading() {
        let response = HTTPURLResponse(url: request.url!,
                                     statusCode: Self.statusCode,
                                     httpVersion: nil,
                                     headerFields: nil)!
        
        client?.urlProtocol(self, didReceive: response, cacheStoragePolicy: .notAllowed)
        
        if let data = Self.responseData {
            client?.urlProtocol(self, didLoad: data)
        }
        
        client?.urlProtocolDidFinishLoading(self)
    }
    
    override func stopLoading() {}
}

// 테스트에서 사용
func testAPICall() {
    let mockData = """
    [{"id": 1, "name": "John", "email": "john@example.com"}]
    """.data(using: .utf8)!
    
    let mockSession = MockSession.create(with: mockData)
    
    mockSession.request("https://api.example.com/users")
        .responseDecodable(of: [User].self) { response in
            XCTAssertTrue(response.result.isSuccess)
            XCTAssertEqual(response.value?.count, 1)
        }
}
```

## 보안 고려사항

### SSL Pinning
```swift
let evaluators = [
    "api.example.com": PinnedCertificatesTrustEvaluator(),
    "cdn.example.com": PublicKeysTrustEvaluator()
]

let manager = ServerTrustManager(evaluators: evaluators)
let session = Session(serverTrustManager: manager)
```

### Certificate Pinning
```swift
class CustomTrustEvaluator: ServerTrustEvaluating {
    func evaluate(_ trust: SecTrust, forHost host: String) throws {
        // 커스텀 인증서 검증 로직
        let policy = SecPolicyCreateSSL(true, host as CFString)
        SecTrustSetPolicies(trust, policy)
        
        var result: SecTrustResultType = .invalid
        let status = SecTrustEvaluate(trust, &result)
        
        guard status == errSecSuccess else {
            throw AFError.serverTrustEvaluationFailed(reason: .trustEvaluationFailed(error: nil))
        }
        
        guard result == .unspecified || result == .proceed else {
            throw AFError.serverTrustEvaluationFailed(reason: .trustEvaluationFailed(error: nil))
        }
    }
}
```

## 성능 모니터링

### 네트워크 메트릭 수집
```swift
class PerformanceMonitor: EventMonitor {
    let queue = DispatchQueue(label: "performance.monitor")
    
    func request<Value>(_ request: DataRequest, didParseResponse response: DataResponse<Value, AFError>) {
        guard let metrics = response.metrics else { return }
        
        let taskMetrics = metrics.taskMetrics.first
        print("📊 Performance Metrics:")
        print("   Domain Lookup: \(taskMetrics?.domainLookupTime ?? 0)s")
        print("   Connect Time: \(taskMetrics?.connectTime ?? 0)s")
        print("   Secure Connection: \(taskMetrics?.secureConnectionTime ?? 0)s")
        print("   Request Time: \(taskMetrics?.requestTime ?? 0)s")
        print("   Response Time: \(taskMetrics?.responseTime ?? 0)s")
        print("   Total Time: \(metrics.taskInterval.duration)s")
        
        if let transferSize = taskMetrics?.responseTransferSize {
            print("   Transfer Size: \(transferSize) bytes")
        }
    }
}
