---
title: ViewController 생명주기(Life Cycle)
date: 2025-02-13 12:00:00 +0900
categories: [개념, UIKit]
tags: [생명주기, ViewController]
---

## **📌 ViewController 생명주기 개념**

UIViewController는 특정 이벤트에 따라 자동으로 호출되는 **생명주기 메서드**를 가짐.
뷰가 생성되고 사라지는 일련의 매커니즘

| 메서드 | 호출 시점 | 특징 |
| --- | --- | --- |
| `viewDidLoad()` | 뷰가 처음 메모리에 로드될 때 | 한번만 호출됨. |

### **예제 코드**

```swift
class LifeCycleViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        print("viewDidLoad - 뷰가 메모리에 로드됨")
        view.backgroundColor = .white
    }
}
```

| 메서드 | 호출 시점 | 특징 |
| --- | --- | --- |
| `viewWillAppear()` | 뷰가 화면에 나타나기 직전 | 화면이 여러 번 나타날 때마다 호출되므로, UI 업데이트나 데이터 로딩 등의 작업 |

### **예제 코드**

```swift
class LifeCycleViewController: UIViewController {

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        print("viewWillAppear - 뷰가 나타나기 직전")
    }
}
```

| 메서드 | 호출 시점 | 특징 |
| --- | --- | --- |
| `viewDidAppear()` | 뷰가 화면에 나타난 후 | 애니메이션을 시작하거나, 사용자 상호작용 활성화 등의 작업 |

### **예제 코드**

```swift
class LifeCycleViewController: UIViewController {

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        print("viewDidAppear - 뷰가 나타난 후")
    }
}
```

| 메서드 | 호출 시점 |
| --- | --- |
| `viewWillDisappear()` | 뷰가 사라지기 직전 |

### ** 예제 코드**

```swift
class LifeCycleViewController: UIViewController {

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        print("viewWillDisappear - 뷰가 사라지기 직전")
    }
}
```

| 메서드 | 호출 시점 |
| --- | --- |
| `viewDidDisappear()` | 뷰가 사라진 후 |

### **예제 코드**

```swift
class LifeCycleViewController: UIViewController {

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        print("viewDidDisappear - 뷰가 사라진 후")
    }
}
```
