---
title: 덱(Deque, Double-Ended Queue) — 자료구조와 활용
date: 2025-02-13 12:00:00 +0900
categories: [CS, 자료구조]
tags: []
---



### 1. 덱 개념

덱은 **양쪽 끝에서 삽입과 삭제가 모두 가능한** 자료구조입니다. 큐(Queue)와 스택(Stack)의 특성을 모두 가지며, 데이터를 앞이나 뒤에서 넣고 뺄 수 있습니다.

### 2. 기본 연산

* **append(item)**: 덱의 뒤쪽에 데이터(item)를 추가합니다. (뒤쪽 삽입)
* **prepend(item)**: 덱의 앞쪽에 데이터(item)를 추가합니다. (앞쪽 삽입)
* **popFront()**: 덱의 앞쪽 데이터를 제거하고 반환합니다.
* **popBack()**: 덱의 뒤쪽 데이터를 제거하고 반환합니다.
* **peekFront()**: 앞쪽 데이터를 제거하지 않고 반환합니다.
* **peekBack()**: 뒤쪽 데이터를 제거하지 않고 반환합니다.
* **isEmpty()**: 덱이 비어 있는지 확인합니다.

### 3. 내부 구조

덱은 배열, 연결 리스트, 원형 버퍼 등으로 구현합니다.

* 배열 기반은 뒤쪽 삽입/삭제는 빠르지만, 앞쪽 작업은 비용이 발생할 수 있어 효율적 구현을 위해 원형 배열을 사용하기도 합니다.
* 연결 리스트 기반은 앞뒤 노드를 바로 접근할 수 있어 양쪽 연산 모두 효율적입니다.

### 4. 활용 사례

* **양방향 탐색**: BFS나 최단 경로 알고리즘에서 앞뒤로 노드를 추가/삭제해야 할 때.
* **슬라이딩 윈도우 문제**: 배열이나 리스트에서 일정 범위 내 최대/최소값을 빠르게 찾을 때.
* **텍스트 편집기**: 커서 이동 시 앞뒤 문자를 빠르게 삽입/삭제할 때.

### 5. 코드 예시 (Swift)

```swift
struct Deque<T> {
    private var elements = [T]()
    
    mutating func append(_ item: T) {
        elements.append(item)
    }
    
    mutating func prepend(_ item: T) {
        elements.insert(item, at: 0)
    }
    
    mutating func popFront() -> T? {
        guard !elements.isEmpty else { return nil }
        return elements.removeFirst()
    }
    
    mutating func popBack() -> T? {
        return elements.popLast()
    }
    
    func peekFront() -> T? {
        return elements.first
    }
    
    func peekBack() -> T? {
        return elements.last
    }
    
    var isEmpty: Bool {
        return elements.isEmpty
    }
}
```

### 6. 마무리

덱은 양쪽 끝에서 유연하게 데이터를 넣고 뺄 수 있어 다양한 알고리즘과 시스템에서 활용됩니다. 큐와 스택의 장점을 합친 자료구조로, 문제에 따라 적절히 선택해 사용하면 효율적인 구현이 가능합니다.
