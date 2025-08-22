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


### 📌 실행 흐름

1. 뷰컨트롤러가 초기화 되며 테이블뷰 생성
2. `viewDidLoad`호출되면 테이블뷰에 `datasource`,`delegate` 세팅 / 테이블뷰에 셀 등록
3. 테이블뷰 constraints 적용
4. 델리게이트 패턴으로 인해 테이블뷰 내부에서 특정 함수들을 호출하면 책임을 위임받은 뷰컨트롤러에 정의된 함수들이 실행됨
5. 데이터의 개수만큼 정의된 `tableview(tableView:cellForRowAt:)`함수가 동작하며 정의한 커스텀 셀을 재사용하거나 새로운 셀마다 데이터 입력





