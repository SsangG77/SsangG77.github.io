---
title: Getter, Setter 
date: 2025-01-27 12:00:00 +0900
categories: [개념, Swift]
tags: []
---

# Swift Getter & Setter 완벽 가이드

## 목차
1. [개요](#개요)
2. [기본 개념](#기본-개념)
3. [Computed Properties](#computed-properties)
4. [Property Observers](#property-observers)
5. [실제 활용 사례](#실제-활용-사례)
6. [성능 고려사항](#성능-고려사항)

## 개요

Swift에서 Getter와 Setter는 클래스, 구조체, 열거형의 프로퍼티에 접근하고 값을 설정하는 메커니즘입니다. 이들은 데이터 캡슐화와 유효성 검증을 통해 더 안전하고 유지보수가 용이한 코드를 작성할 수 있게 해줍니다.

## 기본 개념

### Stored Properties vs Computed Properties

Swift에는 두 가지 주요 프로퍼티 타입이 있습니다:

**Stored Properties (저장 프로퍼티)**
```swift
struct Person {
    var name: String        // 저장 프로퍼티
    let birthYear: Int      // 상수 저장 프로퍼티
}
```

**Computed Properties (연산 프로퍼티)**
```swift
struct Person {
    var name: String
    let birthYear: Int
    
    // 연산 프로퍼티 - getter만 있는 읽기 전용
    var age: Int {
        return Calendar.current.component(.year, from: Date()) - birthYear
    }
    
    // 연산 프로퍼티 - getter와 setter 모두 있음
    var displayName: String {
        get {
            return "Mr./Ms. \(name)"
        }
        set {
            // newValue는 암시적으로 제공되는 매개변수
            name = newValue.replacingOccurrences(of: "Mr./Ms. ", with: "")
        }
    }
}
```

## Computed Properties

### 기본 문법

```swift
var propertyName: DataType {
    get {
        // getter 로직
        return someValue
    }
    set(newValue) {
        // setter 로직
        // newValue 매개변수는 생략 가능 (기본값: newValue)
    }
}
```

### 읽기 전용 연산 프로퍼티

```swift
struct Circle {
    var radius: Double
    
    // 읽기 전용 연산 프로퍼티 - 간략한 문법
    var area: Double {
        return Double.pi * radius * radius
    }
    
    // 또는 더 간단하게
    var circumference: Double {
        Double.pi * 2 * radius
    }
}

let circle = Circle(radius: 5.0)
print(circle.area)          // 78.53981633974483
print(circle.circumference) // 31.41592653589793
```

### 읽기/쓰기 연산 프로퍼티

```swift
struct Temperature {
    private var celsius: Double = 0.0
    
    var fahrenheit: Double {
        get {
            return (celsius * 9.0 / 5.0) + 32.0
        }
        set {
            celsius = (newValue - 32.0) * 5.0 / 9.0
        }
    }
    
    var kelvin: Double {
        get {
            return celsius + 273.15
        }
        set {
            celsius = newValue - 273.15
        }
    }
}

var temp = Temperature()
temp.fahrenheit = 100.0
print(temp.celsius)   // 37.77777777777778
print(temp.kelvin)    // 310.92777777777775
```

## Property Observers

프로퍼티 옵저버는 프로퍼티 값의 변화를 감지하고 응답할 수 있게 해줍니다.

### willSet과 didSet

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet {
            print("총 걸음 수가 \(totalSteps)에서 \(newValue)로 변경될 예정입니다.")
        }
        didSet {
            if totalSteps > oldValue {
                print("\(totalSteps - oldValue)걸음이 추가되었습니다.")
            }
        }
    }
}

let stepCounter = StepCounter()
stepCounter.totalSteps = 100
// 출력:
// 총 걸음 수가 0에서 100로 변경될 예정입니다.
// 100걸음이 추가되었습니다.

stepCounter.totalSteps = 150
// 출력:
// 총 걸음 수가 100에서 150로 변경될 예정입니다.
// 50걸음이 추가되었습니다.
```

### 사용자 정의 매개변수명

```swift
class BankAccount {
    var balance: Double = 0.0 {
        willSet(newBalance) {
            print("잔액이 $\(balance)에서 $\(newBalance)로 변경됩니다.")
        }
        didSet(oldBalance) {
            print("이전 잔액: $\(oldBalance), 현재 잔액: $\(balance)")
            if balance < 0 {
                print("⚠️ 잔액이 마이너스입니다!")
            }
        }
    }
}
```

## 실제 활용 사례

### 1. 데이터 유효성 검증

```swift
struct User {
    private var _email: String = ""
    
    var email: String {
        get {
            return _email
        }
        set {
            if isValidEmail(newValue) {
                _email = newValue
            } else {
                print("유효하지 않은 이메일 주소입니다: \(newValue)")
            }
        }
    }
    
    private func isValidEmail(_ email: String) -> Bool {
        let emailRegex = #"^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$"#
        return NSPredicate(format: "SELF MATCHES %@", emailRegex).evaluate(with: email)
    }
}

var user = User()
user.email = "test@example.com"  // 유효함
user.email = "invalid-email"     // 유효하지 않은 이메일 주소입니다: invalid-email
```

### 2. UI 업데이트 자동화

```swift
class ProgressView {
    var progress: Float = 0.0 {
        didSet {
            // 값 범위 제한
            progress = max(0.0, min(1.0, progress))
            
            // UI 업데이트
            DispatchQueue.main.async {
                self.updateProgressBar()
                self.updatePercentageLabel()
            }
        }
    }
    
    private func updateProgressBar() {
        // 프로그레스 바 UI 업데이트 로직
        print("프로그레스 바 업데이트: \(Int(progress * 100))%")
    }
    
    private func updatePercentageLabel() {
        // 퍼센티지 라벨 업데이트 로직
        print("퍼센티지 라벨 업데이트: \(Int(progress * 100))%")
    }
}
```

### 3. 레이지 로딩 패턴

```swift
class DataManager {
    private var _expensiveData: [String]?
    
    var expensiveData: [String] {
        get {
            if _expensiveData == nil {
                print("데이터를 로딩 중...")
                _expensiveData = loadExpensiveData()
            }
            return _expensiveData!
        }
        set {
            _expensiveData = newValue
        }
    }
    
    private func loadExpensiveData() -> [String] {
        // 시간이 많이 걸리는 데이터 로딩 시뮬레이션
        Thread.sleep(forTimeInterval: 2.0)
        return ["데이터1", "데이터2", "데이터3"]
    }
}
```

### 4. 프로퍼티 변환

```swift
struct APIResponse {
    private var _data: Data?
    
    var jsonString: String? {
        get {
            guard let data = _data else { return nil }
            return String(data: data, encoding: .utf8)
        }
        set {
            _data = newValue?.data(using: .utf8)
        }
    }
    
    var dictionary: [String: Any]? {
        get {
            guard let data = _data else { return nil }
            return try? JSONSerialization.jsonObject(with: data) as? [String: Any]
        }
        set {
            guard let dict = newValue else {
                _data = nil
                return
            }
            _data = try? JSONSerialization.data(withJSONObject: dict)
        }
    }
}
```

## 성능 고려사항

### 1. 연산 프로퍼티의 비용

```swift
struct PerformanceExample {
    let items: [Int]
    
    // 매번 계산되므로 비용이 높을 수 있음
    var expensiveSum: Int {
        return items.reduce(0, +)
    }
    
    // 캐싱을 통한 최적화
    private var _cachedSum: Int?
    var optimizedSum: Int {
        if _cachedSum == nil {
            _cachedSum = items.reduce(0, +)
        }
        return _cachedSum!
    }
}
```

### 2. 무한 재귀 방지

```swift
// ❌ 잘못된 예제 - 무한 재귀 발생
struct BadExample {
    var value: Int {
        get {
            return value  // 자기 자신을 호출하여 무한 재귀!
        }
        set {
            value = newValue  // 자기 자신을 호출하여 무한 재귀!
        }
    }
}

// ✅ 올바른 예제
struct GoodExample {
    private var _value: Int = 0
    
    var value: Int {
        get {
            return _value
        }
        set {
            _value = newValue
        }
    }
}
```


## 결론

Swift의 Getter와 Setter는 단순한 접근자 메서드를 넘어서 강력한 프로퍼티 시스템을 제공합니다. 연산 프로퍼티와 프로퍼티 옵저버를 적절히 활용하면 다음과 같은 이점을 얻을 수 있습니다:

- **데이터 캡슐화**: 내부 구현을 숨기고 안전한 인터페이스 제공
- **유효성 검증**: 잘못된 값의 설정 방지
- **자동 업데이트**: 값 변경 시 관련 작업 자동 수행
- **지연 계산**: 필요할 때만 값 계산하여 성능 최적화

올바르게 사용하면 코드의 가독성, 유지보수성, 그리고 안정성을 크게 향상시킬 수 있는 Swift의 핵심 기능 중 하나입니다.
