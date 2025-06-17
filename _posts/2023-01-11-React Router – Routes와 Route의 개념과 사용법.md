---
title: React Router – Routes와 Route의 개념과 사용법
date: 2023-01-11 12:00:00 +0900
categories: [개념, React]
tags: [JavaScript]
---



# React Router – Routes와 Route의 개념과 사용법

## 들어가기 전에

React는 기본적으로 SPA(Single Page Application)이므로 페이지 전환을 새로고침 없이 수행한다.
이때 경로(path)에 따라 다른 컴포넌트를 렌더링하도록 도와주는 라이브러리가 **React Router**이며, 그 중심에 `Routes`와 `Route`가 있다.

---

## 1. 핵심 개념

### ✅ `Routes`

* 라우팅 규칙들을 묶는 **컨테이너**
* `Route` 컴포넌트들을 자식으로 포함
* 가장 일치하는 경로의 컴포넌트를 렌더링

```jsx
import { Routes, Route } from 'react-router-dom';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  );
}
```

### ✅ `Route`

* 개별 라우팅 규칙을 정의
* `path`: 경로
* `element`: 렌더링할 컴포넌트(JSX 요소)

---

## 2. 동적 라우팅 (Dynamic Routing)

URL의 일부를 변수처럼 사용할 수 있다.

```jsx
<Route path="/user/:id" element={<User />} />
```

* `/user/1`, `/user/42` 모두 해당
* `useParams()` 훅으로 `id`에 접근

```jsx
import { useParams } from 'react-router-dom';

function User() {
  const { id } = useParams();
  return <p>유저 ID: {id}</p>;
}
```

---

## 3. 중첩 라우팅 (Nested Routing)

중첩된 경로를 구성할 수 있다.

```jsx
<Route path="/dashboard" element={<Dashboard />}>
  <Route path="profile" element={<Profile />} />
  <Route path="settings" element={<Settings />} />
</Route>
```

`<Outlet />` 컴포넌트를 사용해 중첩된 라우트를 렌더링한다.

```jsx
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet />
    </div>
  );
}
```

---

## 4. Not Found (404 페이지)

일치하는 경로가 없을 경우를 처리할 수 있다.

```jsx
<Route path="*" element={<NotFound />} />
```

---

## 5. 요약

| 구성 요소     | 역할               |
| --------- | ---------------- |
| `Routes`  | 여러 `Route` 묶음    |
| `Route`   | 경로별 컴포넌트 렌더링 설정  |
| `path`    | URL 경로 지정        |
| `element` | 렌더링할 JSX 요소      |
| `Outlet`  | 중첩 라우트 출력 위치     |
| `*`       | 모든 경로 (404 처리 등) |

---

## 결론

`Routes`와 `Route`는 React Router의 핵심 구조다.
클래식 HTML처럼 페이지를 나누는 대신, React에서는 이 구조를 통해 SPA 안에서 유동적으로 화면을 바꾼다.
동적 파라미터, 중첩 라우팅, 404 처리 등 실전에서 자주 활용되므로 개념을 정확히 이해하고 있어야 한다.

> `react-router-dom`은 최신 버전(v6 이상) 기준으로 사용하는 것이 권장된다.
> v6에서는 `Switch` 대신 `Routes`가 도입되었고, `element`에는 JSX를 직접 전달해야 한다.
