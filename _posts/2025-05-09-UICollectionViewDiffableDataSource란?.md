---
title: UICollectionViewDiffableDataSource란
date: 2025-06-12 12:00:00 +0900
categories: [개념]
tags: [컬렉션뷰]
---

### ✅ 기존 방식의 문제

`UICollectionView`는 원래 이렇게 구성함:

- `data` 배열 직접 관리
- `cellForItemAt`, `numberOfItems` 직접 작성
- 데이터가 바뀌면 `reloadData()` 또는 `performBatchUpdates()`로 수동 처리
    
    → **문제**: 항목 이동/삽입/삭제가 복잡하고, 잘못하면 index 충돌로 앱 크래시
    

---

### ✅ DiffableDataSource가 해결한 점

> 상태를 "전체 스냅샷"으로 정의하고, 그 변화만 반영한다.
> 
- 이전 상태와 새 상태를 비교(diff)해서, 애니메이션 포함 UI 자동 갱신
- 개발자는 “최종 상태”만 말하면 된다
- 덕분에 코드 간결 + 충돌 없음 + 디버깅 쉬움

---

### ✅ 구조 요약

| 구성 요소 | 설명 |
| --- | --- |
| `UICollectionViewDiffableDataSource` | 컬렉션 뷰의 새로운 데이터 관리자 |
| `NSDiffableDataSourceSnapshot` | “현재 화면에 표시될 데이터 상태”를 담은 객체 |
| `apply(snapshot)` | 스냅샷을 기준으로 화면 갱신 (diff 계산 포함) |
| `Hashable`한 모델 | 아이템 고유 식별용 (UUID 등) |

---

### 모델 정의

```swift
enum Section {
    case main
}

struct Item: Hashable {
    let id = UUID()
    let title: String
}

```

---

### 셀 등록 + DataSource 설정

```swift
let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
collectionView.register(UICollectionViewCell.self, forCellWithReuseIdentifier: "Cell")

var dataSource: UICollectionViewDiffableDataSource<Section, Item>!

dataSource = UICollectionViewDiffableDataSource<Section, Item>(collectionView: collectionView) { collectionView, indexPath, item in
    let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "Cell", for: indexPath)
    cell.contentView.backgroundColor = .lightGray

    let label = UILabel()
    label.text = item.title
    label.frame = cell.contentView.bounds
    cell.contentView.subviews.forEach { $0.removeFromSuperview() } // label 중복 방지
    cell.contentView.addSubview(label)

    return cell
}

```

---

### 스냅샷 적용 함수

```swift
func applyItems(_ items: [Item]) {
    var snapshot = NSDiffableDataSourceSnapshot<Section, Item>()
    snapshot.appendSections([.main])
    snapshot.appendItems(items)
    dataSource.apply(snapshot, animatingDifferences: true)
}
```

---

### 사용 예 (뷰컨트롤러에서)

```swift
var items = [
    Item(title: "사과"), Item(title: "바나나"), Item(title: "체리")
]

override func viewDidLoad() {
    super.viewDidLoad()
    view.addSubview(collectionView)
    collectionView.frame = view.bounds

    applyItems(items)
}

@objc func reloadData() {
    items.shuffle()
    applyItems(items)
}

```

---

이 흐름이면 직접 인덱스를 계산하지 않아도 되고, 셀 삭제/삽입/이동도 자동으로 처리된다.

**원하는 경우**:

- `UICollectionViewCompositionalLayout`과 조합
- 셀에 버튼 넣고 데이터 바인딩
- 섹션 여러 개 구성
