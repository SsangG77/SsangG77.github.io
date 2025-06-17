---
title: struct와 class의 차이점
date: 2025-01-31 12:00:00 +0900
categories: [개념, Swift]
tags: []
---


# Swift에서 `struct`와 `class`의 차이 – 언제 어떤 타입을 써야 할까?

Swift에서는 데이터 모델을 만들 때 `struct`와 `class` 두 가지 타입 중 선택할 수 있다.
둘은 비슷해 보이지만, 실제로는 **메모리 관리, 동작 방식, 사용 용도**에서 중요한 차이가 있다.

이 글에서는 `struct`와 `class`의 차이를 정리하고, 언제 어떤 상황에서 선택해야 하는지 기준을 제시한다.

---

## 1. 타입의 본질: 값 타입 vs 참조 타입

| 구분     | struct            | class                  |
| ------ | ----------------- | ---------------------- |
| 메모리 특성 | 값 타입 (Value Type) | 참조 타입 (Reference Type) |

### ✅ struct는 **복사**된다

```swift
struct A {
    var value: Int
}

var a = A(value: 10)
var b = a
b.value = 20

print(a.value) // 10
print(b.value) // 20
```

→ `a`와 `b`는 완전히 다른 복사본

### ✅ class는 **참조**된다

```swift
class B {
    var value: Int
    init(value: Int) { self.value = value }
}

var a = B(value: 10)
var b = a
b.value = 20

print(a.value) // 20
print(b.value) // 20
```

→ `a`와 `b`는 같은 인스턴스를 가리킨다

---

## 2. 상속 가능 여부

| 구분 | struct | class |
| -- | ------ | ----- |
| 상속 | 불가능    | 가능    |

클래스는 객체지향 패러다임의 핵심 기능인 **상속**을 지원한다.
구조체는 상속을 지원하지 않지만, 프로토콜을 통해 유사한 기능은 가능하다.

---

## 3. 소멸자(deinit)의 유무

| 구분  | struct | class         |
| --- | ------ | ------------- |
| 소멸자 | 없음     | 있음 (`deinit`) |

클래스는 객체가 메모리에서 해제될 때 실행되는 **소멸자(deinitializer)** 를 지원한다.

```swift
class Sangjin {
    deinit {
        print("인스턴스 해제됨")
    }
}
```

→ 구조체는 메모리에서 해제되더라도 `deinit`을 사용할 수 없다.

---

## 4. mutating 키워드

구조체는 내부 속성을 변경하려면 `mutating` 키워드를 사용해야 한다.

```swift
struct Sangjin {
    var name: String
    
    mutating func changeName(to newName: String) {
        self.name = newName
    }
}

var s = Sangjin(name: "sangjin")
s.changeName(to: "cha sangjin")
```

클래스는 참조 타입이므로 `mutating` 없이 속성 변경이 가능하다.

---

## 5. === 연산자: 인스턴스 비교

| 구분         | struct | class |
| ---------- | ------ | ----- |
| === 연산자 사용 | 불가능    | 가능    |

클래스는 인스턴스의 **메모리 주소** 비교가 가능하다.

```swift
class A {}
let a = A()
let b = a
let c = A()

print(a === b) // true
print(a === c) // false
```

→ 구조체는 값 복사이므로 `===` 같은 비교는 불가능하다.

---

## 언제 struct를 쓰고 언제 class를 써야 할까?

### struct를 사용해야 할 경우

* 값이 변경되지 않고, 복사되는 것이 의도된 경우
* 데이터 중심 모델 (예: 좌표, 크기, 색상 등)
* 스레드 안정성(thread safety)이 중요한 경우

### class를 사용해야 할 경우

* 상속이 필요한 경우
* 여러 곳에서 같은 인스턴스를 공유해야 하는 경우
* 객체의 생명주기 관리를 직접 제어해야 하는 경우 (`deinit` 필요 등)

---

## 마무리

Swift에서 `struct`와 `class`는 단순한 문법 선택이 아니다.
**값 타입 vs 참조 타입**, **불변 vs 변경 가능**, **가벼운 구조체 vs 복잡한 객체 모델** 등
명확한 목적에 따라 선택해야 한다.

> 값 중심의 불변 데이터를 다룰 때는 struct,
> 동작 중심의 복잡한 객체를 다룰 때는 class를 사용하자.



