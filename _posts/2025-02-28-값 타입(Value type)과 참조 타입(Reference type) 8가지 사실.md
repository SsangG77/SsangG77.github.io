---
title: 값 타입(Value type)과 참조 타입(Reference type) 8가지 사실
date: 2025-02-28 12:00:00 +0900
categories: [개념, Swift]
tags: [class, struct]
---


## 1. 값 타입은 ‘복사’ , 참조 타입은 ‘바로가기’

- 값 타입: 변수에 저장시 인스턴스를 복사하여 변수에 저장
- 참조 타입: 변수에 저장시 인스턴스를 힙 메모리([](https://www.notion.so/1a0ee035a51f800594eae3ef56054284?pvs=21) 참조)에 저장하고, 그 메모리 주소값을 변수에 저장

## 2. 값 타입은 불변성이 있다.

- 값 타입은 매번 복사되기 때문에 데이터가 바뀌지 않음.
- 참조 타입은 다른 변수라 해도 모두 같은 메모리를 가리키기 때문에, 값을 변경하면 모든 변수에 똑같이 적용됨.

```swift
class A {
	var name = "sangjin"
}

let a = A()
let b = a
b.name = "cha"
print(a.name)
```

## 3. ‘struct’는 값 타입, ‘class’는 참조 타입이다.

- `struct`, `enum`으로 생성하면 값 타입
- `class`로 생성하면 참조 타입

## 4. Swift에서 function/closure를 제외한 대부분은 값타입

- Int, Float, Double, Bool, String, Array, Dictionary, Set, Tuple... 은 `struct`로 구현돼있다.
- 특정 변수에 저장한 function이나 closure를 다른 변수로 복사한다면, 데이터를 복사하지 않고 참조만 공유한다.

## 5. Collection은 Copy-on-write 방식 사용

- Collection의 서브타입들(Array, Dictionary, String 등)은 기본적으로 값타입이다.
- 하지만 새로운 변수에 할당할때는 참조만 공유하다가 **값을 변경할때** 새로운 복사본을 만들어 값을 변경한다. (매번 복사하면 성능저하가 일어나기 때문)

## 6. 메모리의 Code, Data, Stack, Heap 4가지 영역

- Code
    - 실행될 프로그램이 저장되는 영역
    - 보통 읽기 전용으로 설정되어 프로그램 실행중 변경되지 않음
- Data
    - 고정된 전역변수, 정적 변수의 값을 저장
    - 종료시까지 유지되는 데이터
- Stack
    - 함수 호출 시 지역 변수, 매개변수, 리턴 주소 등을 저장
    - 함수 호출시 늘어나고 종료시 줄어듬.(LIFO)
- Heap
    - 동적으로 할당된 메모리를 저장
    - 명시적으로 메모리를 할장/해제해야함.
    - 크기가 스택보다 크며 자유롭게 메모리 사용 가능.

## 7. 값 타입은 Stack, 참조 타입은 Heap에 저장

- 값 타입은 Stack에 저장
    - 현재 실행중인 쓰레드와 관련된 컨텍스트를 Stack에 저장함.
    - Stack영역은 CPU가 관리하고 최적화하기 때문에 메모리에 빈 공간이 발생하지 않음.
- 참조 타입은 Heap에 저장
    - 원본 인스턴스를  Heap에 저장하고 이 Heap의 주소값을 이용함.
    - Heap 인스턴스의 사용이 끝나면 직접 해제해줘야 하기때문에 검색, 빈 메모리 삽입 등 과정이 필요하기때문에 Stack에 비해서 느린 단점이 있음.


## 8. 값 타입이 Heap, 참조 타입이 Stack에 저장되는 예외도 있음.

- Array, Dictionary, Set, String 등은 최적화를 위해 Heap공간을 활용.
- 참조 타입중에서도 크기가 고정되어있거나, 언제 지울지 컴파일러가 미리 예측이 가능하다면 Stack에 저장하여 성능을 향상시킴.
