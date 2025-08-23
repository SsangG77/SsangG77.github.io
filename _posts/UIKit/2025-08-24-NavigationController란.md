---
title: NavigationController란
date: 2025-08-24 12:00:00 +0900
categories: [개념, UIKit]
tags: [NavigationController]
---


## 상세페이지로 이동

1. sceneDelegate 수정
   - 뷰컨트롤러를 네비게이션컨트롤러로 감싸서 상세페이지로 이동이 가능하도록 설정
     
   - 앱의 라이프사이클을 관리하는 객체인 `UIScene`을 ios 13 이후로는 여러개의 윈도우를 가질 수 있게 되면서 `UIWindowScene`타입으로 캐스팅
     ```
      guard let windowScene = (scene as? UIWindowScene) else { return }
     ```
     
   - 앱의 콘텐츠를 담을 가장 바깥쪽 컨테이너인 윈도우를 생성
     ```
      let window = UIWindow(windowScene: windowScene)
     ```
     
   - 실제 뷰 컨트롤러 인스턴스 생성
     ```
      let mainVC = ViewController()
     ```
     
   - 네비게이션컨트롤러의 rootViewController 할당
     ```
      let navigationController = UINavigationController(rootViewController: mainVC)
     ```
     
   - 네비게이션컨트롤러를 `window`의 rootViewController로 할당
     ```
      window.rootViewController = navigationController
     ```
     
   - 새로 생성한 윈도우를 메모리에 유지하기 위해 앱의 속성 `window`에 할당
     ```
      self.window = window
     ```
     
   - 윈도우를 앱의 주요 윈도우로 만들고 화면에 표시
     ```
      window.makeKeyAndVisible()
     ```

- 전체 코드
```
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        let window = UIWindow(windowScene: windowScene)
        let mainVC = ViewController()
        let navigationController = UINavigationController(rootViewController: mainVC)
        window.rootViewController = navigationController
        self.window = window
        window.makeKeyAndVisible()
    }
```


2. 상세페이지 구현
```
class DetailViewController: UIViewConroller {
  ...
}
```

3-1. 버튼으로 상세페이지 화면 전환
```
// ViewController.swift
class ViewController: UIViewController {
    
    // 버튼 생성 및 설정
    let myButton: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("상세 페이지로 이동", for: .normal)
        button.translatesAutoresizingMaskIntoConstraints = false
        return button
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.addSubview(myButton)
        
        // 버튼의 레이아웃 설정
        myButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        myButton.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
        
        // 버튼의 탭 이벤트에 대한 액션 추가
        myButton.addTarget(self, action: #selector(didTapButton), for: .touchUpInside)
    }

    // 버튼이 탭되면 호출되는 메서드
    @objc func didTapButton() {
        // 다음 화면(상세 페이지)을 생성
        let detailVC = DetailViewController()
        
        // 화면 전환
        navigationController?.pushViewController(detailVC, animated: true)
    }
}
```

3-2. `UITabGestureRecognizer`사용하기
- 버튼이 아닌 일반적인 UIView나 UIImageView 등을 탭했을 때 동작하게 하려면 제스처 인식기를 사용합니다.

```
// ViewController.swift
class ViewController: UIViewController {
    
    // 탭 가능한 뷰 생성
    let tappableImageView: UIImageView = {
        let imageView = UIImageView(image: UIImage(systemName: "star.fill"))
        imageView.translatesAutoresizingMaskIntoConstraints = false
        // 사용자 상호작용을 허용
        imageView.isUserInteractionEnabled = true
        return imageView
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.addSubview(tappableImageView)
        
        // 이미지 뷰의 레이아웃 설정
        tappableImageView.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        tappableImageView.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
        
        // 탭 제스처 인식기 생성
        let tapGesture = UITapGestureRecognizer(target: self, action: #selector(didTapItem))
        
        // 이미지 뷰에 제스처 인식기 추가
        tappableImageView.addGestureRecognizer(tapGesture)
    }
    
    // 탭 이벤트 발생 시 호출되는 메서드
    @objc func didTapItem() {
        // 상세 페이지로 이동하는 로직
        let detailVC = DetailViewController()
        navigationController?.pushViewController(detailVC, animated: true)
    }
}
```









