---
title: 해시 테이블(Hash Table) — 자료구조와 원리
date: 2025-02-14 12:00:00 +0900
categories: [CS, 자료구조]
tags: []
---


### 1. 해시 테이블 개념

해시 테이블은 \*\*키(Key)\*\*를 해시 함수로 변환해 배열의 인덱스처럼 사용하는 자료구조입니다. 이를 통해 데이터를 빠르게 저장하고 검색할 수 있습니다.

![Image](https://github.com/user-attachments/assets/08bb02a5-481d-4b61-8b53-a734407c2b41)


### 2. 핵심 원리

* **해시 함수(Hash Function)**: 키를 고정 크기 정수 값(해시 값)으로 변환합니다.
* **배열(Array)**: 해시 값을 인덱스로 사용해 데이터를 저장하는 공간입니다.
* **충돌(Collision)**: 서로 다른 키가 같은 해시 값을 가질 때 발생합니다.

### 3. 충돌 해결 방법

* **체이닝(Chaining)**: 같은 인덱스에 연결 리스트나 다른 자료구조를 두어 충돌 데이터를 묶어 저장합니다.
* **오픈 어드레싱(Open Addressing)**: 충돌 시 빈 배열 위치를 찾아 데이터를 저장합니다. (선형 조사, 이차 조사, 이중 해싱 등)

### 4. 연산 시간 복잡도

* 평균적으로 \*\*검색, 삽입, 삭제 모두 O(1)\*\*에 가깝습니다.
* 최악의 경우(모든 키가 같은 해시 값)에는 O(n)이 될 수 있습니다.

### 5. 활용 사례

* 데이터베이스 인덱스
* 캐시 구현
* 집합(Set), 맵(Map) 자료구조 내부 구현
* 중복 검사, 빈도 계산

### 6. 예시 (Swift)

```swift
class HashTable<Key: Hashable, Value> {
    private typealias Element = (key: Key, value: Value)
    private var buckets: [[Element]]
    private(set) var count = 0
    
    init(capacity: Int) {
        buckets = Array(repeating: [], count: capacity)
    }
    
    private func index(forKey key: Key) -> Int {
        return abs(key.hashValue) % buckets.count
    }
    
    func insert(_ value: Value, forKey key: Key) {
        let index = self.index(forKey: key)
        
        for (i, element) in buckets[index].enumerated() {
            if element.key == key {
                buckets[index][i].value = value
                return
            }
        }
        
        buckets[index].append((key, value))
        count += 1
    }
    
    func value(forKey key: Key) -> Value? {
        let index = self.index(forKey: key)
        for element in buckets[index] {
            if element.key == key {
                return element.value
            }
        }
        return nil
    }
    
    func remove(forKey key: Key) {
        let index = self.index(forKey: key)
        for (i, element) in buckets[index].enumerated() {
            if element.key == key {
                buckets[index].remove(at: i)
                count -= 1
                return
            }
        }
    }
}
```

### 7. 마무리

해시 테이블은 빠른 데이터 접근이 필요한 곳에서 매우 효과적인 자료구조입니다. 해시 함수와 충돌 해결 방식을 잘 설계하면 높은 성능을 유지할 수 있습니다.
