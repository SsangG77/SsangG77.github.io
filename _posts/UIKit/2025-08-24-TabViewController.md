---
title: UITabBarController
date: 2025-08-24 12:00:00 +0900
categories: [개념, UIKit]
tags: [UITabBarController]
---


# UITabBarController의 역할
`UITabBarController`는 여러 뷰 컨트롤러를 관리하는 컨테이너 뷰 컨트롤러
콘텐츠를 직접 표시하는 것이 아닌, 다른 뷰 컨트롤러들을 담아 보여주는 역할

# 만드는 방법
1. `UITabBarController` 인스턴스 생성
   - 앱의 진입점인 `SceneDelegate.swift`파일에서 `UITabBarController`를 생성하고, 이를 앱의 루트 뷰 컨트롤러로 설정
```
// SceneDelegate.swift
import UIKit

func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.Options) {
    guard let windowScene = (scene as? UIWindowScene) else { return }
    let window = UIWindow(windowScene: windowScene)
    
    // 탭 바 컨트롤러 생성
    let tabBarController = UITabBarController()
    
    // 윈도우의 루트 뷰 컨트롤러로 설정
    window.rootViewController = tabBarController
    window.makeKeyAndVisible()
    self.window = window
}
```

2. 각 탭에 들어갈 뷰 컨트롤러 만들기
   - 각 탭에 표시할 뷰컨트롤러들 생성 (아래는 예시)
```
// FirstViewController.swift
import UIKit

class FirstViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemRed
        title = "첫 번째 탭"
    }
}

// SecondViewController.swift
import UIKit

class SecondViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemBlue
        title = "두 번째 탭"
    }
}
```

3. 탭 바 컨트롤러에 뷰 컨트롤러들 추가
   - `SceneDelegate.swift`에서 두 개의 뷰컨트롤러를 `TabBarController`에 할당 및 탭 타이틀, 아이콘 지정
```
// SceneDelegate.swift
// ... (기존 코드)

func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.Options) {
    // ... (기존 코드)

    let tabBarController = UITabBarController()
    
    // 탭에 들어갈 뷰 컨트롤러들을 만듭니다.
    let firstVC = FirstViewController()
    firstVC.tabBarItem = UITabBarItem(title: "탭 1", image: UIImage(systemName: "house"), tag: 0)
    
    let secondVC = SecondViewController()
    secondVC.tabBarItem = UITabBarItem(title: "탭 2", image: UIImage(systemName: "gear"), tag: 1)
    
    // 뷰 컨트롤러들을 탭 바 컨트롤러의 viewControllers 속성에 배열로 할당합니다.
    tabBarController.viewControllers = [firstVC, secondVC]
    
    // 윈도우의 루트 뷰 컨트롤러로 설정
    window.rootViewController = tabBarController
    window.makeKeyAndVisible()
    self.window = window
}
```

