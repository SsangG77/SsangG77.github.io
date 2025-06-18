---
title: JAVA - Servlet
date: 2022-06-16 12:00:00 +0900
categories: [개념, JAVA]
tags: []
---


# Java Servlet: 웹의 백엔드를 구성하는 기초 블록

## 개요

**Java Servlet**은 Java를 기반으로 동작하는 **서버 측 웹 프로그래밍 기술**이다. 클라이언트(주로 웹 브라우저)의 요청을 받아 처리한 후, 결과를 다시 클라이언트에 전달하는 구조로 작동한다. JSP, Spring 등의 상위 프레임워크들도 결국 Servlet 기반으로 동작한다.

---

## Servlet은 어떤 역할을 하는가?

* 클라이언트의 **HTTP 요청**을 받아서
* 내부 로직 (비즈니스 처리, DB 접근 등)을 수행하고
* 최종적으로 **HTTP 응답**을 생성해 클라이언트에 전달

Servlet은 **Java 클래스**로 정의되며, 기본적으로 `javax.servlet.http.HttpServlet`을 상속받아 사용한다.

---

## Servlet 생명주기 (Life Cycle)

Servlet은 컨테이너(tomcat 등)에 의해 다음과 같은 순서로 관리된다:

1. **로드 및 인스턴스 생성**
2. **초기화**: `init()` 메서드 호출
3. **요청 처리**: `service()` → `doGet()`, `doPost()` 등으로 분기
4. **종료**: 컨테이너 종료 시 `destroy()` 호출

```plaintext
생성 → init() → (요청마다) service() → destroy()
```

---

## 기본 Servlet 코드 예제

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
                         throws ServletException, IOException {
        response.setContentType("text/plain");
        response.getWriter().write("Hello, Servlet!");
    }
}
```

* `@WebServlet("/hello")`: 해당 URL로 요청 시 이 Servlet이 호출됨
* `doGet()`: GET 요청 처리
* `HttpServletRequest`: 요청 정보 담김 (쿼리 파라미터, 헤더 등)
* `HttpServletResponse`: 응답 생성 (본문, 헤더, 상태코드 등)

---

## Servlet이 Tomcat에서 작동하는 구조

1. 사용자가 브라우저에서 `http://localhost:8080/app/hello` 요청
2. Tomcat이 해당 URL을 분석해 `HelloServlet` 매핑 확인
3. Servlet 인스턴스의 `service()` 메서드 호출
4. HTTP 메서드에 따라 `doGet()` 또는 `doPost()` 등 호출
5. 결과를 HTTP Response로 클라이언트에 반환

---

## Servlet의 실무 활용

Servlet만으로 웹 애플리케이션 전체를 만들기도 했지만, 요즘은 주로 **Spring MVC** 같은 고수준 프레임워크가 Servlet 위에서 작동한다. 예를 들어, Spring의 `@Controller`는 내부적으로 DispatcherServlet을 통해 요청을 라우팅하고 처리한다.

---

## Servlet vs JSP vs Spring

| 구분      | 역할     | 설명                                   |
| ------- | ------ | ------------------------------------ |
| Servlet | 백엔드 로직 | 요청 처리, 비즈니스 로직 담당                    |
| JSP     | 프론트 출력 | Servlet이 만든 데이터를 HTML로 출력            |
| Spring  | 프레임워크  | Servlet 기반의 편의 기능 제공 (의존성 주입, 라우팅 등) |

---

## 장점

* **Java 기반의 안정성**
* **HTTP 요청/응답 명세와 1:1 대응**
* Spring 등 프레임워크 학습 전 **웹 프로그래밍의 근본 원리 학습에 적합**

---

## 단점

* 모든 요청/응답을 직접 다뤄야 하므로 **생산성이 낮음**
* 유지보수가 어려움 → 그래서 Spring 같은 프레임워크가 등장함

---

## 프로젝트 적용 예 (학습 단계)

Java 기반 웹 프로젝트를 직접 만들 때 Servlet을 사용해 로그인 기능을 구현한 적이 있다.

* **로그인 요청 (POST)**: 사용자 ID/PW를 검증해 세션 생성
* **로그아웃 처리 (GET)**: 세션 무효화
* **회원가입 폼 (GET), 회원가입 처리 (POST)**: 입력값 검증 후 DB 저장

Servlet과 JSP만으로 작동하는 웹 애플리케이션이었고, 이후 Spring MVC로 마이그레이션해 비교 학습도 진행했다.

---

## 마무리

Servlet은 직접 쓰기엔 불편하지만, **웹 백엔드의 작동 원리를 이해하는 데 필수적인 기술**이다. Spring, Spring Boot를 쓰는 개발자라도 내부 DispatcherServlet의 동작 원리를 정확히 알면 **디버깅, 확장성 설계, 성능 개선** 등에 큰 도움이 된다.
