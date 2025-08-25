---
title: CollectionView란
date: 2025-02-16 12:00:00 +0900
categories: [개념, UIKit]
tags: [CollectionView]
---


# 컬렉션뷰란
그리드 형태나 사용자 정의 레이아웃으로 데이터를 표시할 때 사용하는 뷰

# 컬렉션 뷰의 구성 요소
1. `UIColletionViewCell` : 화면에 표시되는 개별 아이템


2. `UICollectionViewDataSource`: 컬렉션 뷰에 데이터를 제공하는 객체
- `UICollectionViewDataSource`는 컬렉션 뷰(`UICollectionView`)에 **어떤 데이터를 표시할지**를 알려주는 역할을 하는 프로토콜입니다. 델리게이트 패턴의 일부로서, 컬렉션 뷰가 데이터를 직접 관리하지 않고, 데이터 소스 객체(주로 뷰 컨트롤러)에게 데이터 관련 정보를 위임하는 방식입니다.
- 주요 메서드
> **`collectionView(_:numberOfItemsInSection:)`**
> 컬렉션 뷰의 특정 섹션에 **몇 개의 아이템(셀)을 표시**할지 알려줍니다. 예를 들어, 배열의 요소 개수를 반환하여 컬렉션 뷰가 그 수만큼의 셀을 준비하도록 합니다.

> **`collectionView(_:cellForItemAt:)`**
> 주어진 `indexPath`에 해당하는 **셀 객체**를 반환합니다. 이 메서드에서 재사용 큐(`dequeueReusableCell`)에서 셀을 가져와 데이터로 채운 뒤 반환하면, 컬렉션 뷰가 이 셀을 화면에 표시합니다. 

  
3. `UICollectionViewLayout`: 컬렉션 뷰의 **레이아웃(뷰의 위치, 크기, 간격 등)**을 정의하는 객체
   - UICollectionViewDelegateFlowLayout은 컬렉션 뷰의 레이아웃을 세밀하게 제어하는 데 사용되는 프로토콜입니다.
   - 역할 : UICollectionViewDelegateFlowLayout의 주요 역할은 UICollectionViewFlowLayout 객체가 컬렉션 뷰의 크기와 간격을 계산할 때 필요한 정보를 제공하는 것입니다. 이 프로토콜의 메서드들을 구현하여 각 셀, 헤더, 푸터의 크기와 섹션의 간격 등을 동적으로 설정할 수 있습니다.
   - 주요 메서드
> **`collectionView(_:layout:sizeForItemAt:)`**
> 이 메서드는 각 아이템(셀)의 크기를 반환합니다. 이 메서드를 구현하면 모든 셀의 크기를 동일하게 만들거나, indexPath에 따라 각기 다른 크기를 가질 수 있도록 만들 수 있습니다.

> **`collectionView(_:layout:insetForSectionAt:)`**
> 이 메서드는 **각 섹션의 여백(inset)**을 반환합니다. UIEdgeInsets를 사용하여 섹션의 상하좌우 여백을 설정할 수 있습니다.

> **`collectionView(_:layout:minimumLineSpacingForSectionAt:)`**
> 이 메서드는 같은 섹션 내의 행(line) 사이의 최소 간격을 반환합니다.

> **`collectionView(_:layout:minimumInteritemSpacingForSectionAt:)`**
> 이 메서드는 같은 행 내의 아이템(item) 사이의 최소 간격을 반환합니다.

UICollectionViewDelegateFlowLayout을 사용하면 뷰의 크기 변화에 따라 컬렉션 뷰의 레이아웃을 유연하게 조정할 수 있어, 다양한 디바이스와 방향에 대응하는 반응형 UI를 만들 수 있습니다.


# 예제 코드

## 뷰 컨트롤러
```
class ViewController: UIViewController {
  private var items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

  private let cellIdentifier = "cell"

  private let collectionView: UICollectionView = {
    // UICollectionViewFlowLayout을 사용하여 기본적인 그리드 레이아웃 설정
    let layout = UICollectionViewFlowLayout()
    layout.minimumLineSpacing = 10 // 행 간 간격
    layout.minimumInterititemSpacing = 10 // 아이템 간 간격
    layout.sectionInset = UIEdgeInsets(top: 10, left: 10, bottom: 10, right: 10)

    let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
    collectionView.translatesAutoresizingMaskIntoConstraints = false
    
    return collectionView
  }()

  override func viewDidLoad() {
    super.viewDidLoad()
    view.addSubView(collectionView)
    NSLayoutConstraint.activate([
      ...
    ])

    // 데이터 소스 및 델리게이트 설정
    collectionView.dataSource = self
    collectionView.delegate = self

    // 셀 클래스 등록
    collectionView.register(MyCollectionViewCell.self, forCellWithReuseIdentifier: cellIdentifier)
    
  }
}
```

## UICollectionViewDataSource 구현
```
extension ViewController: UICollectionViewDataSource {
  // 표시할 셀의 개수
  func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
      return items.count
  }

  // 각 셀에 표시할 내용
  func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
      // 셀 재사용
      let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellIdentifier, for: indexPath) as! MyCollectionViewCell
      cell.configure(with: items[indexPath.item])
      return cell
  }
}
```

## UIColletionViewDelegateFlowLayout 구현
```
extension ViewController: UICollectionViewDelegateFlowLayout {
  func collectionView(_ collectionView: UICollectionView, layout collectionViewlayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
    let width = (collectionView.bounds.width - 40) / 3 // 전체크기의 3개의 열로 나눈 너비
    return CGSize(width: width, height: width) // 정사각형 셀
  }
}
```


## UICollectionViewCell 구현
```
class MyCollectionViewCell: UICollectionViewCell {
  let label: UILabel = {
    let label = UILabel()
    label.translatesAutoresizingMaskIntoConstraints = false
    return label
  }()

  override init(framw: CGRect) {
    super.init(frame: .zere)

    contentView.addSubview(label)
    NSLayoutConstraint.activate([
        label.centerXAnchor.constraint(equalTo: contentView.centerXAnchor),
        label.centerYAnchor.constraint(equalTo: contentView.centerYAnchor)
    ])
  }

  // 스토리보드 초기화
  required init?(coder: NSCoder) {
      fatalError("init(coder:) has not been implemented")
  }
  
  // 데이터 설정 메서드
  func configure(with number: Int) {
      label.text = "\(number)"
  }
}
```






