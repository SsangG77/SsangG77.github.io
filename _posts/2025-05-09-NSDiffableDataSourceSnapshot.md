---
title: 
date: 2025-06-12 12:00:00 +0900
categories: [개념]
tags: [Swift, 컬렉션뷰]
---

### ✅ 스냅샷이란?

> 지금 컬렉션 뷰에 표시하고 싶은 섹션과 아이템의 구성을 담은 객체.
> 
> 
> → 이걸 `apply()` 하면 UI는 그 상태로 맞춰짐.
> 

---

### ✅ 왜 필요한가?

기존 방식은 insert/delete/move를 직접 계산해야 했다.

→ 실수 많고 복잡.

스냅샷은 **"최종 상태"만 정의**하면 된다.

→ 나머지는 시스템이 diff 계산해서 갱신함.

---

### ✅ 기본 사용 구조

```swift
var snapshot = NSDiffableDataSourceSnapshot<Section, Item>()
snapshot.appendSections([.main])
snapshot.appendItems([item1, item2, item3])
dataSource.apply(snapshot, animatingDifferences: true)
```

- `appendSections([.main])` → 어떤 섹션이 있는지
- `appendItems([...])` → 어떤 아이템이 어떤 순서로 들어가는지
- `apply()` → 컬렉션 뷰에 이 상태 반영 (필요한 애니메이션 포함)

---

### ✅ 바뀐 데이터 적용도 똑같음

```swift
snapshot.deleteItems([item1])
snapshot.appendItems([item4])
dataSource.apply(snapshot)
```

→ 이전 상태와 자동으로 비교(diff)해서 삭제/삽입/이동을 **애니메이션 포함해서 자동 적용**함.

---

즉, 스냅샷은 "데이터 상태 정의서"고, `apply()`는 "이 상태로 바꿔줘"라고 명령하는 거다.
