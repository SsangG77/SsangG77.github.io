---
title: UICollectionView란?
date: 2025-05-06 12:00:00 +0900
categories: [개념, Swift]
tags: [Swift, 컬렉션뷰]
---

## ✅ 1. UICollectionView

**개념:**

스크롤 가능한 아이템 리스트를 보여주는 뷰. 레이아웃을 따로 지정해야 아이템이 보이고, 데이터소스와 델리게이트로 동작을 제어함.

**예제:**

```swift
let layout = UICollectionViewFlowLayout()
let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
collectionView.backgroundColor = .white

```

---

## ✅ 2. UICollectionViewCell

**개념:**

각 아이템을 나타내는 셀. `UICollectionViewCell`을 상속해서 내부에 UI 컴포넌트를 넣고 커스터마이징함.

**예제:**

```swift
class MyCell: UICollectionViewCell {
    let label = UILabel()

    override init(frame: CGRect) {
        super.init(frame: frame)
        label.frame = contentView.bounds
        label.textAlignment = .center
        contentView.addSubview(label)
        contentView.backgroundColor = .lightGray
    }

    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

```

## ✅ 3. UICollectionViewLayout (FlowLayout)

**개념:**

아이템들의 위치와 간격 등을 결정함. `UICollectionViewFlowLayout`은 기본 레이아웃으로 수직/수평 스크롤, 셀 크기/간격 설정 가능.

**예제:**

```swift
let layout = UICollectionViewFlowLayout()
layout.scrollDirection = .vertical
layout.itemSize = CGSize(width: 100, height: 100)
layout.minimumLineSpacing = 10
layout.minimumInteritemSpacing = 10
```

## ✅ 4. UICollectionViewDataSource

**개념:**

아이템 개수와 셀의 내용을 제공하는 역할. 최소 두 메서드를 구현해야 동작함.

- **numberOfItemsInSection**: 
섹션 내의 항목 수를 반환합니다. 예를 들어, 사진 갤러리에서 몇 장의 사진을 표시할지 결정합니다.
- **cellForItemAt**: 
특정 위치에 표시할 셀을 반환합니다. dequeueReusableCell을 사용하여 재사용 가능한 셀을 가져옵니다.

**예제:**

```swift
extension ViewController: UICollectionViewDataSource {
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 10
    }

    func collectionView(
    _ collectionView: UICollectionView,
    cellForItemAt indexPath: IndexPath
    ) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "MyCell", for: indexPath) as! MyCell
        
cell.label.text = "Item \(indexPath.item)"
       return cell
    }
}

```

## ✅ 5. UICollectionViewDelegate

**개념:**

셀 터치, 스크롤 이벤트 등을 처리함. 선택되었을 때의 동작을 정의할 수 있음.

**예제:**

```swift
extension ViewController: UICollectionViewDelegate {
    func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        print("Tapped item at \(indexPath.item)")
    }
}

```

## 전체 코드
```swift
import UIKit

class MyCell: UICollectionViewCell {
    let label = UILabel()

    override init(frame: CGRect) {
        super.init(frame: frame)
        label.frame = contentView.bounds
        label.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        label.textAlignment = .center
        contentView.addSubview(label)
        contentView.backgroundColor = .lightGray
        contentView.layer.cornerRadius = 8
        contentView.clipsToBounds = true
    }

    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

class ViewController: UIViewController, UICollectionViewDataSource, UICollectionViewDelegateFlowLayout {
    var collectionView: UICollectionView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. Layout 설정
        let layout = UICollectionViewFlowLayout()
        layout.scrollDirection = .vertical
        layout.itemSize = CGSize(width: 100, height: 100)
        layout.minimumLineSpacing = 12
        layout.minimumInteritemSpacing = 12
        layout.sectionInset = UIEdgeInsets(top: 16, left: 16, bottom: 16, right: 16)

        // 2. CollectionView 생성
        collectionView = UICollectionView(frame: view.bounds, collectionViewLayout: layout)
        collectionView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        collectionView.backgroundColor = .white

        // 3. 등록 및 설정
        collectionView.register(MyCell.self, forCellWithReuseIdentifier: "MyCell")
        collectionView.dataSource = self
        collectionView.delegate = self

        // 4. 뷰에 추가
        view.addSubview(collectionView)
    }

    // 5. DataSource 메서드
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 20
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "MyCell", for: indexPath) as! MyCell
        cell.label.text = "Item \(indexPath.item)"
        return cell
    }

    // 6. Delegate 메서드
    func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        print("Tapped item \(indexPath.item)")
    }
}

```


