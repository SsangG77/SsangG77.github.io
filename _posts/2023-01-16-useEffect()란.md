---
title: useEffect()란
date: 2023-01-16 12:00:00 +0900
categories: [개념, React]
tags: [useEffect]
---


## 개요

`useEffect()`는 React에서 **사이드 이펙트를 처리하는 함수**다.
대표적인 사이드 이펙트에는 다음이 포함된다:

* API 호출
* 이벤트 리스너 등록/해제
* 타이머 설정
* 수동 DOM 조작 등

클래스 컴포넌트의 `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`를 대체하는 **함수형 컴포넌트 전용 훅**이다.

---

## 기본 문법

```js
useEffect(() => {
  // 실행할 코드 (effect)
  return () => {
    // 정리(clean-up) 함수 (선택 사항)
  };
}, [의존성]);
```

---

## 동작 방식

### 1. 마운트 시 실행 (componentDidMount)

```js
useEffect(() => {
  console.log('컴포넌트 마운트 시 실행');
}, []);
```

의존성 배열이 빈 배열이면, 최초 렌더링 시에만 실행된다.

---

### 2. 업데이트 시 실행 (componentDidUpdate)

```js
useEffect(() => {
  console.log('count 변경 시 실행');
}, [count]);
```

`count`가 변경될 때마다 실행된다.

---

### 3. 언마운트 시 정리 (componentWillUnmount)

```js
useEffect(() => {
  const timer = setInterval(() => {
    console.log('타이머 실행중');
  }, 1000);

  return () => {
    clearInterval(timer);
    console.log('타이머 정리');
  };
}, []);
```

`return` 함수는 **정리(clean-up) 함수**로, 컴포넌트가 언마운트되거나 의존성이 바뀔 때 호출된다.

---

## 실전 예제 – API 호출

```js
import { useEffect, useState } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => setUser(data));
  }, [userId]); // userId가 바뀔 때마다 재호출

  if (!user) return <p>로딩 중...</p>;
  return <p>{user.name}</p>;
}
```

---

## 주의할 점

* 의존성 배열 생략 시: 매 렌더링마다 실행됨 → **성능 저하 위험**
* 무한 루프 주의: `useEffect` 안에서 상태 변경 시, 의존성 설정 잘못하면 **렌더링 무한 반복**
* 정리 함수는 꼭 필요한 경우 작성 (타이머, 이벤트 리스너 등)

---

## 요약

| 상황               | 의존성 배열            | 실행 시점                 |
| ---------------- | ----------------- | --------------------- |
| 마운트 한 번만 실행      | `[]`              | 최초 렌더링 후 1회           |
| 특정 상태 변경 시 실행    | `[state]`         | 해당 state 변경될 때마다      |
| 매 렌더링마다 실행       | 생략                | 렌더링 될 때마다 실행          |
| 언마운트 또는 재실행 시 정리 | `return () => {}` | 컴포넌트 제거 시 또는 의존성 변경 시 |

---

## 결론

`useEffect()`는 React 함수형 컴포넌트의 생명주기를 제어할 수 있는 핵심 훅이다.
**비동기 로직, 이벤트, 타이머, 외부 라이브러리 연동 등 모든 부수 효과를 처리하는 표준 방식**이므로 필수적으로 이해해야 할 개념이다.

> 의존성 배열을 정확히 설정하는 것이 핵심이다.
> `eslint-plugin-react-hooks`를 함께 사용해 오류를 사전에 방지하는 것이 좋다.

