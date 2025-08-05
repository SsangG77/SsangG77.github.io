---
title: Swift 프로그래밍 4판(야곰) 정리
date: 2025-07-25 12:00:00 +0900
categories: [Swift]
tags: [도서]
---


# 스위프트 기초
## 스위프트 언어의 특성
- 안전성: 개발자의 실수를 강제적이라고 느낄 수 있는 문법적 제재(옵셔널, guard 구문 등)를 통해 제어.
- 객체지향 프로그래밍: 여러 개의 독립된 단위인 객체의 모임으로 파악. 객체는 서로 메시지를 주고받으며 데이터 처리

## 객체지향에서의 클래스와 객체
### 클래스:
- 같은 종류의 집간에 속하는 속성과 행위를 정의한 것.
- 사용자 정의 데이터 타입

### 객체:
- 클래스의 인스턴스(실제로 메모리에 할당되어 동작하는 것).
- 객체는 자신 고유의 속성과 메소드가 있음.

### 메서드:
- 객체가 클래스에 정의된 행위를 실질적으로 동작하는 함수.

## 함수형 프로그래밍
### 특징
- 함수형 프로그래밍은 순수하게 함수에 전달된 인자 값만 결과에 영향을 주므로 상태 값을 갖지 않고 순수하게 함수만으로 동작. 따라서 병렬처리가 가능
- 함수형 프로그래밍은 함수를 일급 객체로 다름
- 일급 객체 조건:
  1. 함수 자체를 전달인자로 전달할 수 있음
  2. 동적 프로퍼티 할당이 가능 -> 함수를 변수에 할당할 수 있다는 뜻
  3. 변수나 데이터 구조 안에 담을 수 있음 -> 함수를 다른 값들처럼 배열, 딕셔너리, 구조체 안에 저장 가능
  4. 반환 값으로 사용 가능
  5. 할당할 때 사용된 이름과 관계없이 고유한 객체로 구별할 수 있음 -> 같은 기능을 해도 서로 다른 함수는 고유함
  ```
  let a = { print("hi") }
  let b = { print("hi") }
  print(a == b) // 에러: Swift에서는 클로저끼리 == 비교 불가, 즉 서로 다른 인스턴스임

  ```

  ## 프로토콜 지향
  스위프트는 구조체와 열거형에 기존의 클래스에서 구현할 수 있었던 캡슐화, 추상화, 접근 제어 등의 기능을 모두 구현 가능
  더불어 프로토콜 익스텐선을 활용할 수 있기 때문에 프로토콜 지향 프로그래밍이 가능

<br>

# 스위프트 시작하기
## 변수와 상수
변수와 상수를 이용해 필요한 데이터들을 메모리에 임시로 저장
변수: 생성 후 데이터 변경 가능 - `var`로 선언
상수: 생성 후 데이터 변경 불가 - `let`으로 선언

```
변수 생성시 타입을 생략하면 컴파일러가 타입을 추론함
```

<br>

# 데이터 타입
## Int와 UInt
Int: +, - 를 포함한 정수
UInt: 0을 포함한 양의 정수
- 각각 8, 16, 32, 64비트의 형태가 있음 ( 예) Int8, UInt8 )
- 시스템 아키텍처에 따라 32비트에서는 Int32, UInt32가 Int, UInt 타입으로 지정되고 64비트도 마찬가지
- 정수 범위: Int.min < UInt.min(== 0) < Int.max < UInt.max

### Bool
참(`true`), 거짓(`false`)만 가지는 타입
```
var boolean: Bool = false
print(boolean) // false
boolean.toggle()
print(boolean) // true
```



### Float, Double
부동소수점을 사용하는 실수, 부동소수 타입
Float: 32비트로 표현 -> 최소 6자리의 숫자까지 표현 가능
Double: 62비트로 표현 -> 최소 15자리의 숫자까지 표현 가능

- 임의의 수 만들기
  ```
  Int.random(in: -100...100)
  UInt.random(in: 1...30)
  Double.random(in: 1.5...4.3)
  Float.random(in: -0.5...1.5)
  ```



### Character
단 하나의 문자
유니코드, 한글 등으로 사용 가능
```
let cha: Character = "A"
```


### String
문자의 나열. 즉 문자열
```
let name: String = "Cha Sangjin"
let greeting = """
안녕하세요. 저는 차상진입니다.
"""
```

### Any, AnyObject와 nil
Any: 스위프트의 모든 데이터 타입을 사용할 수 있다는 뜻
```
var value: Any

value = 123
value = "Swift 최고!"
value = true

```

AnyObject: Any보다는 조금 한정된 의미로 클래스의 인스턴스만 할당 가능
구조체(struct)나 열거형(enum)은 담을 수 없고, 오직 클래스(class) 인스턴스만 가능

```
class Car {
    var brand: String
    init(brand: String) {
        self.brand = brand
    }
}

class Book {
    var title: String
    init(title: String) {
        self.title = title
    }
}

let items: [AnyObject] = [
    Car(brand: "Tesla"),
    Book(title: "Swift의 정석")
]

for item in items {
    if let car = item as? Car {
        print("자동차 브랜드: \(car.brand)")
    } else if let book = item as? Book {
        print("책 제목: \(book.title)")
    }
}

```

<br>

# 데이터 타입 심화
- 서로 다른 타입끼리 데이터 교환시 타입캐스팅(형변환)을 거쳐야 함

## 타입 별칭 추가
```
typealias MyInt = Int
typealias YourInt = Int

let age: MyInt = 100
let year: YoutInt = 2025

age = year // 같은 Int라서 할당 가능
```

## 튜플
- 지정된 데이터의 묶음

1. 인덱스로 접근 가능
```
var person: (String, Int, Double) = ("sangjin", 27, 182.4)
print("이름: \(person.0)")
```

2. 이릉으로 접근 가능
```
var person: (name: String, age: Int, height: Double) = ("sangjin", 27, 182.4)
print("이름: \(person.name)")
```

3. 별칭 지정 가능
```
typealias PersonTuple = (name: String, age: Int, height: Double)

let sangjin: PersonTuple = ("sangjin", 27, 183.2)
```

<br>

## 컬렉션형
### 배열 
- 같은 타입의 데이터를 일렬로 나열
```
// 동일한 표현
var nums: Array<Int> = [0, 1, 2]
var nums: [Int] = [0, 1, 2]
```

### 딕셔너리
- 순서없이 키와 값이 쌍으로 구성되는 컬렉션 타입
- 키는 유일해야 함
```
var numberForname: [String:Int] = ["sangjin" : 10, "Haewon" : 20]
print(numberForName["sangjin"]) // 10 출력
numberForName["sangjin"] = 15 // 15 할당
numberForName["winter"] = 25 // 새 딕셔너리 키 생성과 값 할당

```




### 세트
- 데이터들을 순서없이 중복없이 하나의 묶음으로 저장하는 형태의 컬렉션 타입
- 세트의 요소는 해시 가능한 값이어야 함
- Array와 마찬가지로 대괄호를 사용하기때문에 타입 추론시 Array로 추론함
```
var names: Set<String> = []
names = ["sangjin", "winter", "haewon"]
```

`insert`로 삽입
```
names.insert("karina")
```

`remove`로 제거
```
names.remove("sangjin")
```

- 또한 세트 내부의 값들은 유일함을 보장하기 때문에 교집합, 여집합 등 여러 집합연산이 필요할 때 유용하다.


### 열거형
- 연관된 항목들을 묶어서 표현
- 정의시 외에 추가/수정 불가
- 원시 값(raw value)형태로 실제 값을 가질 수도 있음

1. 열거형 정의
   아래의 두가지 방식으로 정의 가능
    ```
    enum School {
      case middle
      case high
      case college
    }

    enum School {
      case middle, high, college
    }
    ```
  
3. 열겨형 변수 생성
   아래의 두가지 방식으로 정의 가능
   ```
   var school: School = School.middle
   var school = .middle
   ```
  
5. 열거형 원시 값 할당
   ```
    enum School: String {
      case middle = "중학교"
      case high = "고등학교"
      case college = "대학교"
    }

   let school: School = .middle
   print(school.rawValue) // 중학교
   ```

6. 원시 값 일부만 할당
- Swift의 enum에서 rawValue를 명시하지 않으면, 숫자형 타입에서는 이전 값보다 1 증가된 숫자가 자동 할당되고, 문자열 타입에서는 case 이름이 자동으로 원시값으로 사용
```
enum School: String {
      case middle = "중학교"
      case high = "고등학교"
      case college
    }
let school: School = .college
print(school.rawValue) // college
```
```
enum Numbers: Int {
  case a       // 0
  case b       // 1
  case c = 10
  case d       // 11 ← 자동 증가
}

let num: Numbers = .d
print(num) // 11
```

7. 연관 값을 갖는 열겨형
- 열거형 내의 항목이 자신과 연관된 값을 가질 수 있음
- 항목별로 다른 값, 다른 타입을 가질 수 있음
```
// 정의
enum MainDish {
  case pasta(taste: String)
  case pizza(dough: String, topping: String)
  case chicken
}

var dinner: MainDish = .pasta(taste: "크림")
dinner = .pizza(dough: "치즈 크러스트", topping: "불고기")

// 사용
switch MainDish {
  case pasta(let taste):
    print(taste) // 크림
  case pizza(dough, topping): // let 생략 가능
    print("\(dough), \(topping)") // 치즈 크러스트, 불고기
  case chicken:
    print("치킨")
}

```


8. 항목 순회
- 열거형의 항목들을 순회가능하도록 만들려면 `CaseIterable`프로토콜을 채택해야 함
  ```
  enum School: CaseIterable {
    case primary
    case elementary
    case middle
    case high
    case cellege
  }

  let allCases: [School] = School.allCases
  print(allCases) // [primary, elementary, ...]
  ```

- 하지만 연관값이 있을 경우에는 모든 값 조합을 컴파일러가 알 수 없기 때문에 allCases 프로퍼티를 사용할 수 없음
- 이럴 경우 직접 프로퍼티를 정의하여 사용
  ```
  enum PastaTaste: CaseIterable {
    case Cream, tomato
  }
  
  enum PizzaDough: CaseIterable {
     case cheeseCrust, thin, original
  }
  
  enum PizzaTopping: CaseIterable {
     case pepperoni, cheese, bacon
  }
  
  enum MainDish: CaseIterable {
      case pasta(taste: PastaTaste)
      case pizza(dough: PizzaDough, topping: PizzaTopping)
      case chicken(withSauce: Bool)
      case rice
  }

  var allCases: [MainDish] {
     return PastaTaste.allCases.map(MainDish.pasta)
      + PizzaDough.allCases.reduce([]) { (result, dough) -> [MainDish] in 
        result + PizzaTopping.allCases.map { (topping) -> MainDish in 
          MainDish.pizza(dough: dough, topping: topping)
        }
    }
  }

  ```

9. 순환 열겨형
- 열겨형 항목의 관련 값이 자신일 때 사용
- 순환 열거형을 특정항목만 한정한다면 항목 앞에, 전체에 적용할 경우 enum 키워드 앞에 `indirect`키워드 사용
```
// 정의 에제
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}

// 사용 예제
let five = ArithmeticExpression.number(5) // 5
let four = ArithmeticExpression.number(4) // 4
let sum = ArithmeticExpression.addition(five, four) // 9
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2)) // 18
```


10. 비교 가능한 열거형
- `Comparable` 프로토콜을 준수하면 각 항목을 비교할 수 있음
- 작은 순서대로 작성
```
enum Condition: Comparable {
  case bad
  case good
  case great
}

if Condition.bad < Condition.great {
  print("Great")
} else {
  print("Bad")
}

// Great 출력
```

<br>

# 연산자

### 삼항 연산자
```
var boolValue = false
var result = boolValue ? "참" : "거짓" // 거짓 출력

boolValue = true
result = boolValue ? "참" : "거짓" // 참 출력

```

### 범위 연산자
- 폐쇄 범위 연산자 : A부터 B까지의 수를 묶어 표현.(A, B 포함) `A...B`
- 반폐쇄 범위 연산자 : A부터 B미만까지 수를 묶어 표현 (A만 포함) `A..<B`
- 단방향 범위 연산자 :
  - A 이상의 수를 묶어 표현 (A포함) `A...`
  - A 이하의 수를 묶어 표현 (A포함) `...A`
  - A 미만의 수를 묶어 표현 (A 미포함) `..<A`
 
### 기타 연산자
- nil 병합 연산자 : A가 nil이면 B 반환, 아니면 A 반환 `A ?? B`
- 옵셔널 강제 추출 연산자 : `A!`
- 옵셔널 연산자 : 안전하게 추출하거나 A가 옵셔널임을 표현 `A?`


### 사용자 정의 연산자
- 연산자 종류:
  - 전위 연산자(`prefix`) : 피연산자 앞에 위치 `!A`
  - 중위 연산자(`infix`) : 피연산자들 사이에 위치 `A + B`
  - 후위 연산자(`postfix`) : 연산자 뒤에 위치 `A!`

- 연산자 키워드 : `operator`
- 전위 연산자 정의 예제
```
prefix operator ** // 새로 만들때는 정의하기
prefix func ** (value: Int) -> Int {
  return value * value
}

let num = -5
let squareNum = **num
print(squareNum) // 25
```

- 중위 연산자는 피연산자 사이에 위치하는 것이 명확하기 때문에 `func`앞에 키워드를 안붙여도 됨


<br>

# 흐름 제어

### if문
### switch문
- fallthrough 사용
case문이 실행되고 그 다음 case문이 실행되게 하기 위해서는 `fallthrough`를 사용하면 됨
```
let stringValue = "joker"
switch stringValue {
case "joker":
    print("joker")
case "sangjin"
    fallthrough
}
```

- 튜플 사용
튜플로도 switch문 사용 가능
```
typealias NameAge = (name: String, age: Int)
let tupleValue: NameAge = ("sangjin", 21)

switch tupleValue {
case ("sangjin", 21):
  print(true)
default:
  print(false)
}
```

와일드 카드 식별자를 활용
```
typealias NameAge = (name: String, age: Int)
let tupleValue: NameAge = ("sangjin", 21)

switch tupleValue {
case ("sangjin", 21):
  print(true)
case (_, 21): // 와일드 카드 사용
  print("나이만 맞음")
default:
  print(false)
}
```


값 바인딩을 사용
```
typealias NameAge = (name: String, age: Int)
let tupleValue: NameAge = ("sangjin", 21)

switch tupleValue {
case ("sangjin", 21):
  print(true)
case (let name, 21): // 바인딩 사용
  print("이름은 \(name), 나이만 맞음.")
default:
  print(false)
}
```

unknown 속성 사용
- default로 처리를 해놨는데 새로운 케이스가 추가되었을때 default로 처리되지 않고 추가한 case에 대한 경고를 표시해주기 위한 속성
```
enum Num {
  case 0, 1
}

let value = 0

switch value {
case 0:
  print(0)
@unknown default: // 1에 대한 처리를 하지 않아서 경고 발생
  print("다른 숫자")
}
```


### 표현으로서의 조건문
- 표현 형식을 사용하면 if 나 switch의 조건 결과를 변수나 상수에 바로 할당 가능
```
  let someInt = 100
  let size: String = if someInt > 10 { "큰 수" } else { "작은 수" } // 바로 할당
```

```
  enum Menu {
    case chicken, pizza, hamburger
  }

  let lunch = .pizza

  let menu: String = switch lunch {
    case .pizza: "피자"
    default: "음식 없음"
  }
```


# 반복문






