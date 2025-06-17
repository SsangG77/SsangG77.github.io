---
title: Spring - thymeleaf 
date: 2022-09-01 12:00:00 +0900
categories: [Spring]
tags: []
---

알겠다. 아래는 **Spring Boot + Thymeleaf**를 주제로 한 기술 블로그 글이다. 핵심 흐름, 코드 예제, 동작 원리까지 포함해 실무 관점에서 작성했다.

---

# Spring Boot에서 Thymeleaf 사용법 정리: 템플릿 엔진의 기본기부터 실전 예제까지

## 들어가며

Spring Boot에서 서버사이드 렌더링을 해야 한다면 `Thymeleaf`는 JSP보다 우선 고려된다. HTML 친화적인 문법, Spring MVC와의 자연스러운 연동, 강력한 표현식 기능 덕분이다. 이 글에서는 Thymeleaf의 기본 설정부터 동작 원리, 실제 예제를 통해 **핵심 기능을 바로 써먹을 수 있는 형태로** 정리한다.

---

## 1. Thymeleaf란?

* 템플릿 엔진이다. 서버에서 HTML을 생성한다.
* HTML 문법을 그대로 유지하므로 브라우저에서 열어도 깨지지 않는다.
* `${}`로 데이터 바인딩, `th:if`, `th:each`, `th:href` 등으로 제어 가능.
* Spring MVC에서 `Model` 객체와 자연스럽게 연결된다.

---

## 2. 프로젝트 셋업

### Gradle 설정

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

### application.yml

```yaml
spring:
  thymeleaf:
    cache: false # 개발 중에는 false
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML
```

---

## 3. Controller에서 데이터 전달

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("name", "차상진");
        return "home"; // home.html을 렌더링
    }
}
```

---

## 4. Thymeleaf 템플릿 작성 (`resources/templates/home.html`)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>홈 페이지</title>
</head>
<body>
    <h1>안녕하세요, <span th:text="${name}">이름</span>님</h1>
</body>
</html>
```

* `th:text="${name}"` → `${name}`에 있는 값을 HTML에 출력
* `Model`에 담은 값과 자동 바인딩

---

## 5. 반복문, 조건문 등 고급 기능

### 리스트 출력

```java
@GetMapping("/users")
public String users(Model model) {
    List<String> users = List.of("Alice", "Bob", "Charlie");
    model.addAttribute("users", users);
    return "users";
}
```

```html
<ul>
    <li th:each="user : ${users}" th:text="${user}">이름</li>
</ul>
```

### 조건 분기

```html
<p th:if="${name} == '차상진'">관리자입니다.</p>
<p th:unless="${name} == '차상진'">일반 사용자입니다.</p>
```

---

## 6. 템플릿 레이아웃 구성 (프래그먼트)

반복되는 HTML 구조는 분리해서 재사용 가능하다.

### `fragments/header.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <div th:fragment="header">
    <header><h1>공통 헤더</h1></header>
  </div>
</html>
```

### 메인 템플릿에서 include

```html
<html xmlns:th="http://www.thymeleaf.org">
  <body>
    <div th:insert="fragments/header :: header"></div>
    <p>본문 내용</p>
  </body>
</html>
```

---

## 7. 실무에서 유용한 팁

* `th:href`, `th:src`를 써야 URL이 Spring context path를 포함해서 동작한다.
* HTML을 검증 가능한 형태로 유지하면서 서버 렌더링도 가능
* REST API와 SPA가 아닌, 빠르게 관리자 페이지나 사내 도구 만들 때 유리

---

## 마무리

Thymeleaf는 학습 난이도는 낮지만 실제 HTML 구조를 유지하면서 서버 렌더링을 가능하게 한다. 특히 **Spring MVC와의 결합성이 매우 좋아서 빠르게 동작하는 화면을 만들기 적합하다.**
정적 페이지를 서버에서 동적으로 처리하고 싶을 때, 혹은 React나 Vue를 쓰기엔 과하다고 느껴질 때 선택지로 충분히 가치 있다.

---


