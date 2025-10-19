---
title: JAVA - Override
date: 2022-04-29 12:00:00 +0900
categories: [개념, Java]
tags: [JAVA]
---

## 1. `@Override`란?

Java에서 `@Override`는 **상위 클래스나 인터페이스로부터 상속받은 메서드를 재정의**(Override)할 때 사용하는 \*\*애너테이션(annotation)\*\*이다.

```java
@Override
public void run() {
    // 부모 클래스의 run() 메서드를 재정의
}
```

---

## 2. 왜 사용하는가?

### ✅ 컴파일러에게 재정의임을 명시

* 상위 클래스 또는 인터페이스에 **동일한 시그니처의 메서드가 없으면 컴파일 에러** 발생.
* 오타, 시그니처 불일치 등 실수를 **컴파일 타임에 잡아준다**.

```java
class Animal {
    public void speak() {
        System.out.println("Animal speaks");
    }
}

class Dog extends Animal {
    @Override
    public void speak() {
        System.out.println("Dog barks");
    }

    // 아래는 오타가 있어 컴파일 에러 발생
    /*
    @Override
    public void speek() {
        System.out.println("Wrong method name");
    }
    */
}
```

---

## 3. 인터페이스 구현 시에도 사용

자바 6부터는 인터페이스 구현 시에도 `@Override` 사용 가능하다.

```java
interface Clickable {
    void onClick();
}

class Button implements Clickable {
    @Override
    public void onClick() {
        System.out.println("Button clicked");
    }
}
```

---

## 4. 안 써도 되는가?

**기능적으로는 안 써도 동작은 한다.**
하지만 실수를 방지하고 코드 가독성을 높이기 위해 **항상 명시적으로 사용하는 것이 권장된다.**

---

## 5. 사용 시 주의할 점

* 접근 제어자 범위를 줄일 수 없다. (`protected` → `private` 불가)
* 반환 타입이 다르면 재정의가 아니다.
* 부모 클래스에 해당 메서드가 없으면 컴파일 에러 발생.

---

## 6. 정리

| 항목    | 설명                            |
| ----- | ----------------------------- |
| 의미    | 부모 클래스나 인터페이스의 메서드를 재정의할 때 사용 |
| 이점    | 컴파일 타임 체크, 가독성 향상, 실수 방지      |
| 의무 여부 | 필수는 아니지만 권장                   |
| 대상    | 클래스 상속 메서드, 인터페이스 메서드 모두 가능   |

