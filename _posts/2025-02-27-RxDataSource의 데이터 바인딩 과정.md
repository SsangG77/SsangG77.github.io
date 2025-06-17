---
title: RxDataSource의 데이터 바인딩 과정
date: 2025-02-27 12:00:00 +0900
categories: [개념, RxSwift]
tags: []
---

## 1. RxTableViewSecionReloadDataSource 생성

- RxDataSource는 데이터의 섹션과 셀을 정의하는 클로저를 통해 `UITableView`를 관리합니다.

```swift
let dataSource = RxTableViewSectionReloadDataSource<SectionModel<String, String>>(
	configureCell: { dataSource, tableView, indexPath, item in
		let cell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
		cell.textLabel?.text = item //cell에 대한 설정
		return cell
	},
	titleForHeaderInsection: { dataSource, index in
		return dataSource.sectionModels[index].model
	}
)
```

- **configureCell**: 각 섹션의 **셀**을 어떻게 그릴지 정의합니다.
- **titleForHeaderInSection**: 각 섹션의 **헤더**를 어떻게 표시할지 정의합니다.

## 2. 데이터 준비

- RxDataSource는 Observable<[SectionModel]> 데이터 필요

```swift
let sections = Observable.just([
    SectionModel(model: "오늘 할 일", items: ["할 일 1", "할 일 2"]),
    SectionModel(model: "내일 할 일", items: ["할 일 3", "할 일 4"])
])
```

## 3. 데이터와 UITableView 연결

- RxDataSource와 UITableView를 ***연결***하여 데이터 변경 시 UI가 자동으로 업데이트되도록 설정

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    // 데이터 바인딩
    sections
        .bind(to: tableView.rx.items(dataSource: dataSource))
        .disposed(by: disposeBag)
}

```

<aside>

`tableView.rx.` 코드가 사용 가능한 이유

- RxCocoa 라이브러리를 사용하면 기존의 UITableView, UIButton, UITextField 같은 UIKit 요소에`rx`라는 이름의 확장 프로퍼티가 자동으로 추가됨.
</aside>
