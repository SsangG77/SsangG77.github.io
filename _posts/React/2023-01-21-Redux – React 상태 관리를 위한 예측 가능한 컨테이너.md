---
title: Redux – React 상태 관리를 위한 예측 가능한 컨테이너
date: 2023-01-21 12:00:00 +0900
categories: [개념, React]
tags: []
---



# Redux – React 상태 관리를 위한 예측 가능한 컨테이너

## 개요

Redux는 React 애플리케이션에서 **복잡한 상태를 예측 가능하게 관리하기 위한 상태 컨테이너**다.
단방향 데이터 흐름과 순수 함수 기반의 구조를 통해, 상태 변화가 어떻게 일어나는지 명확하게 추적할 수 있도록 한다.

---

## 왜 Redux를 사용하는가?

React는 컴포넌트 기반 구조이기 때문에 컴포넌트 간 상태 전달이 많아지면 **prop drilling**이나 **복잡한 상태 공유** 문제가 생긴다.

Redux는 다음을 해결해준다:

* 전역 상태 관리
* 상태 변경의 예측 가능성
* 개발 도구를 통한 상태 추적
* 코드 분리 (Action / Reducer)

---

## 핵심 개념

| 개념           | 설명                                     |
| ------------ | -------------------------------------- |
| **Store**    | 애플리케이션의 상태를 보관하는 객체. 상태를 읽거나 구독 가능     |
| **Action**   | 상태 변경을 요청하는 객체. `type` 필드는 필수          |
| **Reducer**  | 상태와 액션을 입력받아 새로운 상태를 반환하는 순수 함수        |
| **Dispatch** | 액션을 스토어에 전달하여 상태 변경을 유도                |
| **Selector** | 상태에서 필요한 값을 추출하는 함수 (주로 `useSelector`) |

---

## 동작 흐름

1. UI에서 사용자 이벤트 발생
2. `dispatch`를 통해 `action` 발생
3. `reducer`가 현재 상태와 action을 기반으로 새 상태를 계산
4. `store`에 새 상태 저장
5. 관련된 컴포넌트가 자동으로 리렌더링

---

## 간단 예제

### 1. Action 정의

```js
// actions/counter.js
export const increment = () => ({
  type: 'INCREMENT'
});
```

### 2. Reducer 작성

```js
// reducers/counter.js
const initialState = { count: 0 };

export default function counter(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    default:
      return state;
  }
}
```

### 3. Store 생성

```js
// store.js
import { createStore } from 'redux';
import counter from './reducers/counter';

const store = createStore(counter);

export default store;
```

### 4. React에서 사용

```jsx
// App.jsx
import React from 'react';
import { Provider, useSelector, useDispatch } from 'react-redux';
import store from './store';
import { increment } from './actions/counter';

function Counter() {
  const count = useSelector(state => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
}

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

---

## 장점

* 상태 추적이 쉬움
* 상태 변경 방식이 명확함 (순수 함수 기반)
* 미들웨어(Redux Thunk, Saga 등)로 비동기 로직 관리 가능
* Redux DevTools로 디버깅 수월

---

## 단점

* 보일러플레이트 코드가 많음
* 작은 앱에서는 오히려 복잡도 증가
* 최신 React(예: Context API, useReducer)만으로도 충분한 경우가 많음

---

## 결론

Redux는 규모가 크고 복잡한 상태를 관리해야 하는 앱에서 강력한 이점을 제공한다.
React 생태계에 잘 녹아들어 있고, 미들웨어나 DevTools 등 다양한 확장 기능을 갖추고 있어 **대형 프로젝트의 상태 관리**에 적합하다.

> 단순한 상태 관리에는 Context API + useReducer 조합이 더 나을 수 있다.


