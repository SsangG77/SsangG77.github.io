---
title: TableView란
date: 2025-02-16 12:00:00 +0900
categories: [개념, UIKit]
tags: [TableView]
---


여러 데이터들을 리스트처럼 스크롤할 수 있게 나타내는 뷰이다. 그렇기 때문에 ScrollView를 상속받는다.
많은 데이터를 나타낼때 모든 데이터를 메모리에 올리지 않고 화면에 나타나는 뷰들만 메모리에 올라가기때문에 메모리 관리에 용이하다.

## 개념별 예제

- 테이블뷰와 뷰컨트롤러를 연결시킨다.
- 테이블뷰에 필요한 클래스, 프로토콜을 상속받는다. (UITableView를 관리하기 위한 객체로 설정하기 위해서)

```swift
class ViewController: UIViewController {

  // 더미 데이터
  var dummyList = ["1", "2", "3"]

  // 테이블뷰 정의
  var myTableView: UITableView = {
      let tableView = UITableView()
      tableView.translatesAutoresizingMaskIntoConstraints = false
      return tableView
  }()

  override func viewDidLoad() {
        super.viewDidLoad()
        
        myTableView.dataSource = self
        myTableView.delegate = self
        myTableView.register(MyCell.self, forCellReuseIdentifier: MyCell.identifier) // 커스텀셀, 셀 id로 테이블뷰에 등록
        
        view.addSubview(myTableView)
        
        NSLayoutConstraint.activate([
            myTableView.topAnchor.constraint(equalTo: view.topAnchor),
            myTableView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            myTableView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            myTableView.bottomAnchor.constraint(equalTo: view.bottomAnchor)
        ])
    }
}
```

- 테이블뷰에 셀 등록
```
...
  myTableView.register(MyCell.self, forCellReuseIdentifier: MyCell.identifier) // 커스텀셀, 셀 id로 테이블뷰에 등록
...
```


### UITableViewDataSource 프로토콜
```
extension ViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return dummyList.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard let cell = tableView.dequeueReusableCell(withIdentifier: MyCell.identifier, for: indexPath) as? MyCell else { return UITableViewCell() }
        cell.label.text = dummyList[indexPath.row]
        return cell
    }
}
```

- 셀의 개수를 반환할 코드를 정의한다.
    - UITableViewDataSource 프로토콜의 필수 메서드중 하나로, 특정 섹션에 표시할 셀의 개수를 반환하는 역할.

```swift
 func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    dummyList.count // 섹션이 하나라면 그냥 데이터의 개수를 반환
}
```

> 섹션이란?
테이블 뷰에서 여러 개의 그룹을 가질 수 있는 개념이다.
> 

- 테이블 뷰의 특정 위치(IndexPath)에 셀을 생성하고 반환하는 역할을 한다.

```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    guard let cell = tableView.dequeueReusableCell(withIdentifier: MyCell.identifier, for: indexPath) as? MyCell else { return UITableViewCell() }
    cell.label.text = dummyList[indexPath.row]
    return cell
}
```
> dequeueReusableCell : 재사용 큐(Reuse Queue)에서 셀을 가져오는 메서드


> **indexPath의 역할**
> 
> `cellForRowAt indexPath: IndexPath`의 `indexPath` 매개변수는 **어떤 셀을 생성해야 하는지 위치를 지정하는 역할**을 한다.
> 
> - `indexPath.section`: 섹션 번호 (어떤 섹션의 셀인지)
> - `indexPath.row`: 행 번호 (해당 섹션 내에서 몇 번째 행인지)


### UITableViewDelegate 프로토콜
```
extension ViewController: UITableViewDelegate {
  func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    print(#fileID, #function, #line, "- ")
  }

  func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
    // 1. indexPath를 사용하여 특정 셀의 데이터에 접근합니다.
    let item = dataArray[indexPath.row]
  
    // 2. 데이터에 따라 동적으로 높이를 계산하거나 지정합니다.
    // 예를 들어, 텍스트가 길면 더 높은 셀을 반환합니다.
    if item.isLongText {
        return 120.0 // 긴 텍스트가 있는 셀의 높이
    } else {
        return 60.0 // 일반 셀의 높이
    }
  }
  
}
```


- 테이블에서 해당 셀이 선택되었을때 호출되는 함수

```swift
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    print(#fileID, #function, #line, "- ")
}
```


- 셀의 높이를 커스터마이징하는 함수

```swift
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
  // 1. indexPath를 사용하여 특정 셀의 데이터에 접근합니다.
  let item = dataArray[indexPath.row]

  // 2. 데이터에 따라 동적으로 높이를 계산하거나 지정합니다.
  // 예를 들어, 텍스트가 길면 더 높은 셀을 반환합니다.
  if item.isLongText {
      return 120.0 // 긴 텍스트가 있는 셀의 높이
  } else {
      return 60.0 // 일반 셀의 높이
  }
}
```


### Cell
- 테이블뷰에 아이템들을 나타내기 위한 요소

1. 셀 재사용
- 스크롤에서 사라진 셀은 "재사용 큐"에 보관됨
- 스크롤을 내려 새로운 셀이 필요해지면 `UITableView`는 데이터 소스에게 `dequeueReusableCell(withIdentifier:for:`메서드로 재사용 가능한 셀 요청
- 셀이 전달되면, UI는 그대로 두고 내부의 데이터만 새롭게 업데이트하여 반환

2. 셀 커스텀
- `UITableViewCell`을 상속받는 클래스를 만들어 사용
- 여러 ui요소들을 적용
예제
```
class MyCell: UITableViewCell {
    
    static let identifier: String = "MyCell" // 셀 고유 식별자 정의
    
    let label: UILabel = {
       let label = UILabel()
        label.backgroundColor = .gray
        label.textColor = .black
        label.translatesAutoresizingMaskIntoConstraints = false
        return label
    }()
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        contentView.addSubview(label)
        
        NSLayoutConstraint.activate([
           label.leadingAnchor.constraint(equalTo: contentView.leadingAnchor),
           label.trailingAnchor.constraint(equalTo: contentView.trailingAnchor),
           label.topAnchor.constraint(equalTo: contentView.topAnchor),
           label.bottomAnchor.constraint(equalTo: contentView.bottomAnchor),
           label.heightAnchor.constraint(equalToConstant: 44) // 셀의 높이를 고정
       ])
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

```

## 객체 데이터 셀별로 입력
1. 객체(class) 정의
```
class Item {
    var title: String
    var description: String
    
    init(title: String, description: String) {
        self.title = title
        self.description = description
    }
}
```

2. 객체를 입력받을 `configure(with item:)` 정의
```
class MyCell: UITableViewCell {
  ...

  func configure(with item: Item) {
      self.titleLabel.text = item.title
      self.descriptionLabel.text = item.description
  }

  ...
}
```

3. `UITableViewDatasource`의 `tableView(cellForRowAt:)`함수 수정
```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard let cell = tableView.dequeueReusableCell(withIdentifier: MyCell.identifier, for: indexPath) as? MyCell else { return UITableViewCell() }
        cell.configure(with: dummyList[indexPath.row]) // 데이터를 configure를 통해 할당
        return cell
    }
```


## 셀 클릭 시 상세페이지로 이동

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

3. `UITableViewDelegate`프로토콜의 `tableView(_ :didSelectRowAt:)`함수 구현(`UINavigationController`로 구현)
```
  extension ViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        
        // 탭한 셀의 데이터에 접근합니다.
        let selectedItem = dummyList[indexPath.row]
        
        // 다음으로 넘어갈 새 뷰 컨트롤러를 만듭니다.
        let detailVC = DetailViewController()
        
        // 다음 뷰에 필요한 데이터를 전달합니다.
        // detailVC.data = selectedItem
        
        // UINavigationController가 이 뷰 컨트롤러를 스택에 추가하여 화면을 전환합니다.
        // 이 코드가 정상 작동하려면 ViewController가 UINavigationController 안에 포함되어 있어야 합니다.
        navigationController?.pushViewController(detailVC, animated: true)
        
        tableView.deselectRow(at: indexPath, animated: true)
    }
}
```




### 📌 실행 흐름

1. 뷰컨트롤러가 초기화 되며 테이블뷰 생성
2. `viewDidLoad`호출되면 테이블뷰에 `datasource`,`delegate` 세팅 / 테이블뷰에 셀 등록
3. 테이블뷰 constraints 적용
4. 델리게이트 패턴으로 인해 테이블뷰 내부에서 특정 함수들을 호출하면 책임을 위임받은 뷰컨트롤러에 정의된 함수들이 실행됨
5. 데이터의 개수만큼 정의된 `tableview(tableView:cellForRowAt:)`함수가 동작하며 정의한 커스텀 셀을 재사용하거나 새로운 셀마다 데이터 입력





