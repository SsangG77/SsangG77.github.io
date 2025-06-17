---
title: addTarget(_ : action : for : )
date: 2025-02-18 12:00:00 +0900
categories: [개념, UIKit]
tags: [Obj-c]
---

UIKit에서 버튼, 제스처, 타이머 등의 이벤트가 발생했을때 특정 메서드를 실행하는 방식이다.

주로 `UIButton`, `UIControl`, `UITapGestureRecognizer` 같은 UI 요소에서 사용된다.

### 🚧 기본 구조

```swift
button.addTarget(self, action: #selector(methodName(_:)), for: .touchUpInside)
```

- target : 이벤트를 받을 객체 (`self` 또는 `nil`)
- action : 실행할 메서드 (`#selector(메서드이름)`)
- for : 어떤 이벤트를 감지할지 (`.touchUpInside`, `.valueChanged` 등)

## **✅ 매개변수 설명**

### **🔹 (1) `target: Any?` → 실행할 메서드가 있는 객체**

- 보통 `self`를 사용해서 **현재 클래스 내의 메서드를 실행**하도록 설정함.
- `nil`을 사용하면 이벤트를 받아도 아무 동작을 하지 않음.

### **🔹 (2) `action: Selector` → 실행할 메서드 지정 (`#selector`)**

- `@objc func methodName(_ sender: UIControl)` 형식의 메서드를 실행해야 함.
- 반드시 `@objc` 키워드를 붙여야 `#selector`에서 사용할 수 있음.
- 매개변수로 `UIButton`, `UISwitch`, `UISlider` 같은 `UIControl`을 받을 수 있음.

### **🔹 (3) `for event: UIControl.Event` → 어떤 이벤트에서 실행할지 설정**

- `UIControl.Event`은 다양한 이벤트 타입을 제공함.

| 이벤트 | 설명 |
| --- | --- |
| `.touchUpInside` | 버튼을 눌렀다가 손을 뗄 때 (가장 일반적인 클릭 이벤트) |
| `.touchDown` | 버튼을 누르는 순간 |
| `.valueChanged` | UISwitch, UISlider 등의 값이 변경될 때 |
| `.editingChanged` | UITextField의 텍스트가 변경될 때 |

```swift
button.addTarget(self, action: #selector(buttonTapped(_:)), for: .touchUpInside) ✅
```

👉 **가장 많이 쓰이는 `.touchUpInside`는 "버튼을 눌렀다 손을 뗄 때" 실행됨.**

### 예제 코드

```swift
import UIKit

class ViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let button = UIButton(type: .system)
        button.setTitle("클릭", for: .normal)
        button.addTarget(self, action: #selector(buttonTapped(_:)), for: .touchUpInside)

        button.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(button)
        
        NSLayoutConstraint.activate([
            button.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            button.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }
    
    @objc func buttonTapped(_ sender: UIButton) {
        print("\(sender.currentTitle ?? "버튼")이 눌렸습니다.")
    }
}

```




