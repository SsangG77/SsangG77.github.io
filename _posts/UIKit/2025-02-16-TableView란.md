---
title: TableView란
date: 2025-02-16 12:00:00 +0900
categories: [개념, UIKit]
tags: [TableView]
---


여러 데이터들을 리스트처럼 스크롤할 수 있게 나타내는 뷰이다. 그렇기 때문에 ScrollView를 상속받는다.

많은 데이터를 나타낼때 모든 데이터를 메모리에 올리지 않고 화면에 나타나는 데이터들만 메모리에 올라가기때문에 메모리 관리에 용이하다.

## 1. 사용 예제

- 테이블뷰가 있는 뷰와 뷰컨트롤러를 연결시킨다.
- 테이블뷰에 필요한 클래스, 프로토콜을 상속받는다. (UITableView를 관리하기 위한 객체로 설정하기 위해서)

```swift
class NewVC: UIViewController, UITableVIewDataSource, UITableViewDelegate
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
    let cell = UITableViewCell(style: .subtitle, reuseIdentifier: "MyCell") //UITableViewCell - 셀을 생성
    cell.textLabel?.text = dummyList[indexPath.row] // 셀의 textLabel에 배열 텍스트 하나를 할당
    return cell
}
```

> **indexPath의 역할**
> 
> 
> `cellForRowAt indexPath: IndexPath`의 `indexPath` 매개변수는 **어떤 셀을 생성해야 하는지 위치를 지정하는 역할**을 한다.
> 
> - `indexPath.section`: 섹션 번호 (어떤 섹션의 셀인지)
> - `indexPath.row`: 행 번호 (해당 섹션 내에서 몇 번째 행인지)

- 테이블에서 해당 셀이 선택되었을때 호출되는 함수

```swift
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        print(#fileID, #function, #line, "- ")
    }
```

### 📌 실행 흐름

1. 사용자가 **테이블 뷰의 셀을 탭**하면 `didSelectRowAt`이 호출됨.
2. `indexPath.row`와 `indexPath.section` 값을 이용해 **선택한 셀의 위치를 알 수 있음**.
3. `print()` 문이 실행되어 **디버깅 정보가 콘솔에 출력됨**.

> 예제 코드
> 
> 
> ```swift
> func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
>     let selectedItem = dummyList[indexPath.row] // 선택한 데이터 가져오기
>     let detailVC = DetailViewController()
>     detailVC.data = selectedItem
>     navigationController?.pushViewController(detailVC, animated: true) // 화면 이동
> }
> ```
> 

---

- 전체 코드

```swift
import Foundation
import UIKit

class NewVC : UIViewController, UITableViewDataSource, UITableViewDelegate {
   
    
    
    @IBOutlet var myTableView: UITableView!
    
    
    var dummyList : [String] = [
        "dummy data 1",
        "dummy data 2",
        "dummy data 3",
    ]
    
    override func viewDidLoad() {
    
        super.viewDidLoad()
        
        myTableView.dataSource = self
        myTableView.delegate = self
        
    }
    
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        dummyList.count // 데이터의 갯수
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        let cell = UITableViewCell(style: .subtitle, reuseIdentifier: "MyCell")
        cell.textLabel?.text = dummyList[indexPath.row]
        return cell
        
    }
    
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        print(#fileID, #function, #line, "- ")
    }
    
}

```
