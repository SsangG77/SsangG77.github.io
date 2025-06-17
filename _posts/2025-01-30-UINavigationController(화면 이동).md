---
title: UINavigationController(화면 이동)
date: 2025-01-30 12:00:00 +0900
categories: [개념, UIKit]
tags: [ViewController]
---


계층적 콘텐츠를 탐색하기 위한 스택기반 체계의 뷰 컨트롤러이다.

어떤 아이템을 클릭하면 상세 페이지로 넘어가면서 이전 페이지와 현재 페이지 사이의 계층이 생긴다.

`UINavigationController`는 여러 개의 `UIViewController`를 **스택(Stack) 자료구조** 형태로 관리하는 **컨테이너 뷰 컨트롤러다**. 내부적으로 ViewController들을 배열 형태로 관리한다.

즉,**"LIFO(Last In, First Out)" 원칙(스택 자료구조와 같은 방식)**으로 동작하며, **새로운 화면을 추가(push), 이전 화면을 제거(pop)**하는 방식으로 작동한다.

### 1️⃣ Navigation Stack이란

- `UINavigationController` 는 내부적으로 `ViewController`들을 배열 형태로 관리
- 이 배열을 Navigation Stack이라고 부르고 `viewControllers` 속성으로 확인 가능

```swift
print(navigationController?.viewControllers ?? [])
```

> 📌 **즉, Navigation Controller는 `viewControllers` 배열을 가지고 있으며, 새로운 화면이 push될 때 배열에 추가되고, pop될 때 제거됨!**
> 

### **2️⃣ Push: 새로운 ViewController를 Stack에 추가**

- `pushViewController(_:animated:)`를 호출하면 **새로운 ViewController가 스택의 맨 위에 추가됨**.
- **이전 ViewController는 사라지지 않고, 뒤에 남아 있음**.
    
    ```swift
    let secondVC = SecondViewController()
    navigationController?.pushViewController(secondVC, animated: true)
    ```
    

### **3️⃣ Pop: 현재 ViewController를 Stack에서 제거**

- `popViewController(animated:)`를 호출하면 **현재 화면이 스택에서 제거됨**.
- 이전 화면이 다시 나타남.

```swift
navigationController?.popViewController(animated: true)
```

### **4️⃣ 여러 개의 ViewController를 한 번에 Pop**

- **루트 ViewController(첫 화면)까지 모두 pop하기**
    - 현재까지 Push된 모든 화면이 사라지고, 가장 처음 화면(루트)만 남게 됨.

```swift
navigationController?.popToRootViewController(animated: true)
```

- **특정 ViewController까지 Pop하기**
    - 특정 화면으로 돌아가고, 그 뒤에 있는 화면들은 전부 제거됨.

```swift
if let firstVC = navigationController?.viewControllers.first {
    navigationController?.popToViewController(firstVC, animated: true)
}
```

### **5️⃣ Stack 직접 변경하기 (`viewControllers` 속성)**

- `viewControllers` 배열을 직접 변경해서 특정 화면만 남기거나, 특정 순서로 정렬 가능.

```swift
// 스택을 직접 변경하여 FirstVC와 ThirdVC만 남기기
navigationController?.viewControllers = [firstVC, thirdVC]

```

> 📌 즉, viewControllers 배열을 조작하면 원하는 화면만 남길 수 있음.
> 

## 실행 흐름

1. 앱 실행 → `FirstViewControlleer` 표시됨
2. “다음 화면” 버튼 클릭 → `SecondViewController`로 `pushViewController` (스택: [FirstViewController, SecondViewController]
3. “뒤로 가기”버튼 클릭 → popViewController (스택: [FirstViewController])

### 1. FirstViewController

```swift
import UIKit

class FirstViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        title = "첫 번째 화면"
        
        let nextButton = UIButton(type: .system)
        nextButton.setTitle("다음 화면", for: .normal)
        nextButton.addTarget(self, action: #selector(goToSecondVC), for: .touchUpInside)

        nextButton.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(nextButton)

        NSLayoutConstraint.activate([
            nextButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            nextButton.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }
    
    @objc func goToSecondVC() {
        let secondVC = SecondViewController()
        navigationController?.pushViewController(secondVC, animated: true) // Push
    }
}

```

### 2. SecondViewController

```swift
import UIKit

class SecondViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .lightGray
        title = "두 번째 화면"
        
        let backButton = UIButton(type: .system)
        backButton.setTitle("뒤로 가기", for: .normal)
        backButton.addTarget(self, action: #selector(goBack), for: .touchUpInside)

        let rootButton = UIButton(type: .system)
        rootButton.setTitle("처음 화면으로 가기", for: .normal)
        rootButton.addTarget(self, action: #selector(goToRoot), for: .touchUpInside)

        let stackView = UIStackView(arrangedSubviews: [backButton, rootButton])
        stackView.axis = .vertical
        stackView.spacing = 10
        stackView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(stackView)

        NSLayoutConstraint.activate([
            stackView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            stackView.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }
    
    @objc func goBack() {
        navigationController?.popViewController(animated: true) // Pop (이전 화면으로 돌아가기)
        printStack()
    }

    @objc func goToRoot() {
        navigationController?.popToRootViewController(animated: true) // Root로 이동
        printStack()
    }
}

```

### 3. AppDelegate 설정

```swift
import UIKit

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        window = UIWindow(frame: UIScreen.main.bounds)
        let navController = UINavigationController(rootViewController: FirstViewController()) // 네비게이션 컨트롤러 설정
        window?.rootViewController = navController
        window?.makeKeyAndVisible()
        
        return true
    }
}
```

