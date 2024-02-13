# React Redux

## State의 종류

### Local State

- 데이터가 변경되어서 하나의 컴포넌트에 속하는 UI에 영향을 미치는 상태
- 무조건 `useState`나 `useReducer` 등으로 관리를 해줘야 한다.
- 예를 들면, 버튼의 on/off 같은 것들을 UI에 표시하는 것

### Cross-Component State

- 하나의 컴포넌트가 아니라 다수의 컴포넌트에 영향을 미치는 상태
- `props chains`나 `props drilling`을 필요로 한다. 
- 예를 들면, 모달 컴포넌트 등

### App-Wide State

- 어플리케이션의 모든 컴포넌트에 영향을 미치는 상태
- `props chains`나 `props drilling`을 필요로 한다.
- 예를 들면, 사용자 인증 등

## React Redux를 사용하는 이유

- `React Context`는 **React**의 내장 기능이고, `Cross-Component State`나 `App-Wide State`를 쉽게 관리를 해준다.
- `React Redux` 또한, `Cross-Component State`나 `App-Wide State`를 위한 상태 관리 시스템이다.
- 그럼에도 왜 `Redux`를 쓰는 이유
  - `React Context`는 잠재적인 단점이 있다.
  - 설정이 복잡해지고, `React Context`를 이용한 상태 관리 또한 상당히 복잡해질 수 있다.
  - 대형 프로젝트는 중첩이 많이 된 컨텍스트가 생산성이나 유지보수성 측면에서 문제될 수 있다.
  - 성능 측면에서도 문제가 있는데, 데이터가 자주 변경되는 경우에는 좋지 않다.
  - `React Context`가 유동적인 상태 확산을 대처할 수 없다.

## React Redux의 작동 방식

- `Redux`는 애플리케이션에 있는 하나의 중앙 데이터 저장소다.
  - 여기서 데이터는 `state(상태)`를 의미한다.
  - `Redux`에 전체 애플리케이션의 모든 상태를 저장한다.
- 컴포넌트가 `Redux` 저장소를 구독하고, 데이터가 변경될 때마다 저장소가 컴포넌트에 알려준다.

### `Redux`의 중요한 규칙 

- 컴포넌트는 절대로 저장소에 저장된 데이터를 직접 조작하지 않는다.
- 대신 업데이트를 담당하는 `Reducer` 함수를 설정한다.(`useReducer`랑은 다른 개념이다.)
- `Reducer` 함수는 입력을 받아서 그 입력을 변환하고, 줄이는 함수이다.
  - 가존 상태인 `state`와 수행할 작업인 `action`을 받고 새로운 `state`를 반환한다. 
- 컴포넌트가 액션을 발송하여, 데이터 변경을 트리거한다.
- 리덕스는 그 액션을 리듀서로 전달하고, 그 작업을 리듀서가 수행한다.
- 리듀서는 새로운 `state(상태)`를 만들어나고, 중앙 데이터 저장소의 기존 `state`를 대체한다.


## createStore()

- 리덕스의 `createStore()` 함수는 `deprecated` 되었지만 여전히 사용할 수 있다.
- 하지만 리덕스 팀은 `createStore()`말고 **리덕스 툴킷**이라는 방식을 권장한다.

### 간단한 예시

```javascript
// import
const redux = require('redux');

// reducer 함수
// eslint-disable-next-line default-param-last
const counterReducer = (state = { counter: 0 }, action) => {
  if (action.type === 'increment') {
    return {
      counter: state.counter + 1,
    };
  }

  if (action.type === 'decrement') {
    return {
      conter: state.counter - 1,
    };
  }

  return state;
};

// 저장소
const store = redux.createStore(counterReducer);

// 저장소 구독 함수
const counterSubscriber = () => {
  const latestState = store.getState();
};

store.subscribe(counterSubscriber); // 직접 실행 x, 리덕스가 실행

// 리듀서 함수 실행
store.dispatch({
  type: 'increment',
});

store.dispatch({
  type: 'decrement',
});
```

## 루트 리듀서 만들기

- 한 프로젝트에 여러 개의 리듀서가 있을 때, 한 리듀서로 합쳐서 사용하고, 그 합쳐진 리듀서를 루트 리듀서라 한다.
- 리덕스에 내장되어있는 `combineReducers()`라는 함수를 사용한다.





ㅁ


