---
title: 연결 리스트 (Linked List)
date: 2025-02-10 12:00:00 +0900
categories: [CS, 자료구조]
tags: []
---




# Swift로 알아보는 연결 리스트(Linked List)

iOS 개발에서 컬렉션 타입은 기본이다. 대부분은 `Array`로 시작하지만, 내부 동작이나 성능 최적화를 고려하다 보면 다른 자료구조도 반드시 이해해야 한다. 그중 대표적인 것이 \*\*연결 리스트(Linked List)\*\*다. Swift로 직접 구현해보며 개념과 특성을 정리한다.

---

## 배열과 뭐가 다른가?

Swift의 `Array`는 내부적으로 **연속된 메모리 공간**에 데이터를 저장한다. 이는 인덱스를 통한 빠른 접근이 가능하다는 장점이 있지만, 삽입/삭제가 빈번한 상황에서는 오히려 **성능 저하**가 발생한다.

이때 유리한 구조가 **연결 리스트**다. 연결 리스트는 데이터를 \*\*노드(Node)\*\*라는 단위로 저장하고, 각 노드는 다음 노드를 가리키는 방식으로 연결된다. 메모리가 연속적일 필요가 없고, 삽입/삭제 시 **링크만 변경**하면 된다.

---

## 단일 연결 리스트(Singly Linked List) 구조

```swift
class Node<T> {
    var value: T
    var next: Node?

    init(value: T) {
        self.value = value
    }
}

class LinkedList<T> {
    private var head: Node<T>?

    func append(_ value: T) {
        let newNode = Node(value: value)
        if head == nil {
            head = newNode
            return
        }

        var current = head
        while current?.next != nil {
            current = current?.next
        }
        current?.next = newNode
    }

    func printList() {
        var current = head
        while let node = current {
            print(node.value, terminator: " -> ")
            current = node.next
        }
        print("nil")
    }
}
```

---

## 예제 실행

```swift
let list = LinkedList<Int>()
list.append(10)
list.append(20)
list.append(30)
list.printList() 
// 출력: 10 -> 20 -> 30 -> nil
```

---

## 연결 리스트의 장단점

### 장점

* 메모리 연속성 필요 없음 → 동적 메모리 효율적
* 중간 삽입/삭제가 빠름 (포인터 변경만으로 처리)

### 단점

* 임의 접근 불가 (인덱스로 직접 접근 못함)
* 포인터 사용으로 메모리 추가 사용
* 순차 탐색만 가능

---

## 배열 vs 연결 리스트

| 특징       | 배열        | 연결 리스트       |
| -------- | --------- | ------------ |
| 인덱스 접근   | O(1) (빠름) | O(n) (느림)    |
| 중간 삽입/삭제 | O(n) (느림) | O(1) 또는 O(n) |
| 메모리 구조   | 연속적       | 비연속적         |
| 공간 효율    | 포인터 없음    | 포인터 필요 (더 큼) |

---

## 언제 써야 하나?

* **빈번한 삽입/삭제가 중간에서 발생**할 때
* **데이터 양이 많고** 메모리 재배치가 부담될 때
* **알고리즘 공부, 인터뷰 대비** 시 필수 구조

---

## 마무리

Swift에서 실무에서는 연결 리스트를 직접 구현하는 경우는 드물지만, 자료구조와 알고리즘의 기본기를 쌓는 데는 반드시 이해해야 하는 구조다. 특히 Swift의 값 타입 특성과 클래스의 참조 타입 특성을 이해할 수 있는 기회이기도 하다.

한 줄 정리:
**인덱스가 중요하면 배열, 삽입/삭제가 많으면 연결 리스트.**

