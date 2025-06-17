---
title: Swift 제네릭(Generics)이란
date: 2025-02-03 12:00:00 +0900
categories: [개념, Swift]
tags: []
---

제네릭은 다양한 타입에서 동작할 수 있는 코드를 작성할 수 있도록 해준다.

# 제네릭 함수

- 같은 로직의 함수임에도 타입이 달라서 두번 선언해야함.

```swift
//Int 타입의 두 값을 바꾸는 함수
func swapInts(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

//Double 타입의 두 값을 바꾸는 함수
func swapDoubles(_ a: inout Double, _ b: inout Double) {
    let temp = a
    a = b
    b = temp
}
```

- 제네릭을 사용하여 해결

```swift
func swapValues<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

var x = 10, y = 20
swapValues(&x, &y) // ✅ Int 타입 사용 가능

var a = "Hello", b = "Swift"
swapValues(&a, &b) // ✅ String 타입도 사용 가능
```

---

# 제네릭 타입

- `struct`, `class`를 제네릭 타입으로 선언하면 객체 내부의 변수, 함수들도 선언된 제네릭 타입을 사용할 수 있다.
- T 대신 다른 이름을 사용해도 된다.

```swift
struct Stack<T> {
    private var elements: [T] = []

    mutating func push(_ item: T) {
        elements.append(item)
    }

    mutating func pop() -> T? {
        return elements.popLast()
    }
}
```

```swift
// ✅ Int 타입의 Stack 사용
var intStack = Stack<Int>()
intStack.push(10)
intStack.push(20)
print(intStack.pop()!) // 20
```

```swift
// ✅ String 타입의 Stack 사용
var stringStack = Stack<String>()
stringStack.push("Swift")
stringStack.push("Generics")
print(stringStack.pop()!) // "Generics"
```

---

# 제네릭 프로토콜

- 프로토콜에서 제네릭을 사용하기 위해서는 `associatedtype`으로 제네릭 역할을 하는 변수를 선언해주어야 한다.

```swift
protocol Container {
    associatedtype Item //변수 선언
    mutating func append(_ item: Item) //변수 사용
    var count: Int { get }
    subscript(i: Int) -> Item { get } // associatedType 변수 return
}
```

```swift
// ✅ Int 타입을 저장하는 컨테이너 구현
struct IntContainer: Container {
    var items: [Int] = []
    
    mutating func append(_ item: Int) {
        items.append(item)
    }

    var count: Int { items.count }

    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

```swift

// ✅ String 타입을 저장하는 컨테이너 구현
struct StringContainer: Container {
    var items: [String] = []

    mutating func append(_ item: String) {
        items.append(item)
    }

    var count: Int { items.count }

    subscript(i: Int) -> String {
        return items[i]
    }
}
```

---

# 제네릭 제약

- 특정 프토토콜을 따르는 타입만 허용하기
- 예제 `<T: Person>` 는 Person 프로토콜을 따르는 제네릭만 허용하도록 규정함.

```swift
func hello<T: Person>(of value: T) -> String? {
    return value.name
}

---------------------------

Protocol Person {
	var name: String
	func sayName()
}

class Sangjin: Person {
	var name = "sangjin"
	var age = 29
	
	func sayName() -> String {
		return name
	}
}

let people = Sangjin()
print(hello(of: people)!) // sangjin

```

d
