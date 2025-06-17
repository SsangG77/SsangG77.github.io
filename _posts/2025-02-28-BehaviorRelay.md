---
title: BehaviorRelay
date: 2025-02-28 12:00:00 +0900
categories: [개념, RxSwift]
tags: [BehaviorRelay]
---

### RxSwift BehaviorRelay란?

- `BehaviorRelay`는 최신 값을 항상 저장하고, `.value`로 읽을 수 있는 RxSwift의 래퍼(Wrapper) 객체입니다.
- 값을 변경할 때는 `.accept()`를 사용하며, `BehaviorSubject`와 다르게 `.completed` 또는 `.error` 상태가 없습니다.

- *RxSwift에서 `BehaviorRelay`는 `BehaviorSubject`의 대체재*
- *초기값을 설정해야 하며, 항상 최신 값을 유지*

```swift
let relay = BehaviorRelay(value: "초기값")

```

- `*.accept()`를 사용하여 값을 업데이트 (`.onNext()` 대신)*

```swift
relay.accept("새로운 값")
```

- *에러 없이 계속 동작 (`.completed`, `.error` 없음)*
- *구독 시 최신 값을 바로 전달함*

```swift
// 구독하여 값 변경 감지
let disposeBag = DisposeBag()
relay.subscribe {
    print("구독된 값: \($0)")
}.disposed(by: disposeBag)
```

# UI에서의 사용 예제

- **UI 상태 관리 (예: `ViewModel`의 데이터 바인딩)**
    
    ```swift
    class ViewModel {
        let username = BehaviorRelay(value: "사용자 이름")
    }
    
    let viewModel = ViewModel()
    viewModel.username.subscribe { print("이름 변경: \($0)") }
        .disposed(by: disposeBag)
    
    viewModel.username.accept("새 이름") // 출력: 이름 변경: 새 이름
    
    ```
    
- **RxSwift에서 끊기지 않는 스트림을 유지하고 싶을 때**
    - `BehaviorSubject`는 `.completed` 또는 `.error`가 발생하면 더 이상 이벤트를 받을 수 없음
    - `BehaviorRelay`는 **끊기지 않기 때문에 UI에서 지속적으로 상태를 관리하는 데 적합**
    - `print("이름 변경: \($0)")` 이 코드는 `username`에 값이 변경될때마다 동작함.

# `didSet`과의 차이점

| 차이점 | `didSet` | `BehaviorRelay` |
| --- | --- | --- |
| **초기값 감지 여부** | ❌ 감지 안 됨 | ✅ 감지됨 (구독 시 바로 전달) |
| **비동기 처리 가능 여부** | ❌ 불가능 (동기적 실행) | ✅ 가능 (`observeOn`, `subscribeOn` 활용 가능) |
| **여러 곳에서 감지 가능 여부** | ❌ 불가능 (1개만 감지) | ✅ 가능 (여러 구독자가 감지 가능) |
| **UI 바인딩 가능 여부** | ❌ 직접 구현해야 함 | ✅ `bind(to:)`, `subscribe(onNext:)`로 가능 |
| **RxSwift 연동 가능 여부** | ❌ 불가능 | ✅ 가능 |

<aside>
📖

> **다른글**
> 
> 
> [제목 없음](https://www.notion.so/191ee035a51f80f693d0e2582484a734?pvs=21)
> 
</aside>

## 1. 초기값 감지 여부

### `didSet` (초기값 감지 ❌)

```swift
class ViewModel {
    var username: String = "사용자 이름" {
        didSet {
            print("이름 변경: \(username)")
        }
    }
}

let viewModel = ViewModel()
// 초기값 출력 없음
viewModel.username = "새 이름"  
// 출력: 이름 변경: 새 이름
```

### `BehaviorRelay` (초기값 감지 ✅)

```swift
import RxSwift
import RxRelay

class ViewModel {
    let username = BehaviorRelay(value: "사용자 이름")
}

let disposeBag = DisposeBag()
let viewModel = ViewModel()

// 구독 시 초기값 자동 방출됨
viewModel.username.subscribe {
    print("이름 변경: \($0)")
}.disposed(by: disposeBag)

// 값 변경
viewModel.username.accept("새 이름")

```

## 2. 비동기 처리 가능 여부

### `didSet` (비동기 처리 ❌) - DispatchQueue를 사용해야 가능

```swift
class ViewModel {
    var username: String = "사용자 이름" {
        didSet {
            DispatchQueue.global().async {
                print("이름 변경: \(self.username)")
            }
        }
    }
}

let viewModel = ViewModel()
viewModel.username = "새 이름"
```

### `BehaviorRelay` (비동기 처리 ✅)

```swift
import RxSwift
import RxRelay

class ViewModel {
    let username = BehaviorRelay(value: "사용자 이름")
}

let disposeBag = DisposeBag()
let viewModel = ViewModel()

// 구독 (비동기 처리 가능)
viewModel.username
    .observe(on: MainScheduler.instance) // 메인 스레드에서 실행
    .subscribe {
        print("이름 변경: \($0)")
    }
    .disposed(by: disposeBag)

// 백그라운드에서 값 변경
DispatchQueue.global().async {
    viewModel.username.accept("새 이름")
}

```

## 3. 여러 곳에서 감지 가능 여부

### `didSet` (한 곳에서만 감지 가능)

```swift
class ViewModel {
    var username: String = "사용자 이름" {
        didSet {
            print("이름 변경: \(username)")
        }
    }
}

let viewModel = ViewModel()
viewModel.username = "새 이름"  // ✅ 값 변경 감지
// 하지만 두 번째 감지기는 추가할 수 없음 (한 번만 실행됨)
```

### `BehaviorRelay` (여러 곳에서 감지 가능)

```swift
import RxSwift
import RxRelay

class ViewModel {
    let username = BehaviorRelay(value: "사용자 이름")
}

let disposeBag = DisposeBag()
let viewModel = ViewModel()

// a파일
viewModel.username.subscribe {
    print("구독 1: \($0)")
}.disposed(by: disposeBag)

// b파일
viewModel.username.subscribe {
    print("구독 2: \($0)")
}.disposed(by: disposeBag)

// 값 변경
viewModel.username.accept("새 이름")

```

```swift
//출력 결과 - 여러곳에서 감지 가능
구독 1: 사용자 이름 // a 파일
구독 2: 사용자 이름 // b 파일
구독 1: 새 이름 // a 파일
구독 2: 새 이름 // b 파일
```

## 4. UI 바인딩 가능 여부

### `didSet` (UI 바인딩 직접 구현 필요)

```swift
class ViewModel {
    var username: String = "사용자 이름" {
        didSet {
            label.text = username // UI 업데이트 (직접 해야 함)
        }
    }
}

// 사용 예시 (UIKit)
let label = UILabel()
let viewModel = ViewModel()
viewModel.username = "새 이름"

```

### `BehaviorRelay` (UI 바인딩 가능)

```swift
import RxSwift
import RxRelay
import UIKit

class ViewModel {
    let username = BehaviorRelay(value: "사용자 이름")
}

let disposeBag = DisposeBag()
let viewModel = ViewModel()
let label = UILabel()

// Rx를 활용한 UI 바인딩
viewModel.username
    .bind(to: label.rx.text)
    .disposed(by: disposeBag)

// 값 변경
viewModel.username.accept("새 이름")

```
