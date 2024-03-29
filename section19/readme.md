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

 
> 업데이트시 절대 기존의 `state`를 변경해서는 안된다. <br/>
> 객체와 배열이 자바스크립트에서는 참조값이기 떄문에 기존 `state`를 변경하는 코드를 작성해서도 안된다.

## createStore()

- 리덕스의 `createStore()` 함수는 `deprecated` 되었지만 여전히 사용할 수 있다.
- 하지만 리덕스 팀은 `createStore()`말고 **리덕스 툴킷**이라는 방식을 권장한다.
- `createStore()`를 먼저 이용해보고, **리덕스 툴킷**을 사용해보자.

### 간단한 예시

```javascript
// import
const redux = require('redux');

// reducer 함수
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

## 리듀서 제공하기

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';

import './index.css';
import App from './App';
import store from './store/index';

const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
  <Provider store={store}>
    <App />
  </Provider>,
);
```

```javascript
import { legacy_createStore } from 'redux';

const counterReducer = (state = { counter: 0 }, action) => {
  if (action.type === 'increment') {
    return {
      counter: state.counter + 1,
    };
  }
  if (action.type === 'decrement') {
    return {
      counter: state.counter - 1,
    };
  }
  return state;
};

const store = legacy_createStore(counterReducer);

export default store;
```

## 리액트 컴포넌트에서 리덕스 데이터 사용하기

- `useSelector()` 훅을 이용해서 컴포넌트를 저장소로 연결한다.
- `useSelector()`를 사용할 때, `react-redux`는 컴포넌트를 위해 리덕스 저장소에 자동으로 구독 설정한다.
  - 이로 하여금 컴포넌트가 리덕스 저장소에서 데이터가 변경될 때마다, 자동으로 업데이트된다.
- 만약 컴포넌트가 언마운트되거나 **DOM**에서 제거되면 `react-redux`도 자동으로 구독을 해지한다. 

```javascript
const data = useSelector((state) => state.data);
```

또는

```javascript
const counter = useSelector((state) => state.counter);
```

### 내부 컴포넌트에서 action을 dispatch하기

- `useDispatch()` 훅을 사용하여 `react redux`에 `action`을 보낸다.

```javascript
const dispatch = useDispatch();

const incrementHandler = () => {
  dispatch({
    type: 'increment',
  });
};

const decrementHandler = () => {
  dispatch({
    type: 'decrement',
  });
};
```

```html
<button onClick={incrementHandler}>Increment</button>
<button onClick={decrementHandler}>Decrement</button>
```

### 클래스 컴포넌트에서의 리덕스

- `connet()`를 활용할 때 인수로 `props`와 `dispatch()`를 전달한다.
- **JSX**에는 `this`를 사용하여 클래스를 명시한다.

```javascript
class Counter extends Components {
  incrementHandler() {
    this.props.increment();
  }

  decrementHandler() {
    this.props.decrement();
  }

  toggleCounterHandler() {
    
  }

  render() {
    return (
      <main className={classes.counter}>
        <h1>Redux Counter</h1>
        <div className={classes.value}>{this.props.counter}</div>
        <div className="counter">
          <button onClick={this.incrementHandler.bind(this)}>Increment</button>
          <button onClick={this.decrementHandler.bind(this)}>Decrement</button>
        </div>
        <button onClick={this.toggleCounterHandler}>Toggle Counter</button>
      </main>
    );
  }
}

const mapStateToProps = (state) => {
  return {
    counter: state.counter,
  };
};

const mapDispatchToProps = (dispatch) => {
  return {
    increment: () => dispatch({ type: 'increment' }),
    decrement: () => dispatch({ type: 'decrement' }),
  };
};

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```

### payload 연결하기

- `useReducer`에서 한 것처럼 `action`에 데이터 연걸하여 추출한다.

- 저장소
```javascript
if (action.type === 'increase') {
  return {
    counter: state.counter + action.payload,
  };
}
```

- 컴포넌트에서 사용시

```javascript
const increaseHandler = (data) => {
  dispatch({
    type: 'increase',
    payload: data,
  });
};
```

### 여러 개의 state 관리하기

- 가독성을 위해 함수 바깥으로 초기 상태를 선언하였다.
- 불변성을 지키기 위해 스프레드 연산자를 사용하였으나, 리덕스 툴킷이 기본적으로 제공하는 `immer`라는 불변성을 지키는 라이브러리 사용을 권장한다.

```javascript
const initialState = {
  counter: 0,
  showCounter: true,
};

const counterReducer = (state = initialState, action) => {
  if (action.type === 'increment') {
    return {
      ...state,
      counter: state.counter + 1,
    };
  }

  if (action.type === 'increase') {
    return {
      ...state,
      counter: state.counter + action.payload,
    };
  }

  if (action.type === 'decrement') {
    return {
      ...state,
      counter: state.counter - 1,
    };
  }

  if (action.type === 'toggle') {
    return {
      ...state,
      showCounter: !state.showCounter,
    };
  }

  return state;
};
```

- 또한, 오타처럼 실수를 유발할 수 있기 때문에 상수로 `export`하면 생산성이 높아진다. (이건 진짜 꿀팁이다.)

```javascript
export const INCREMENT = 'increment';
export const INCREASE = 'increase';
export const DECREMENT = 'decrement';
export const TOGGLE = 'toggle';
```

```javascript
import { INCREASE, INCREMENT, DECREMENT, TOGGLE } from '../store/index';

const incrementHandler = () => {
  dispatch({
    type: INCREMENT,
  });
};

const increaseHandler = (data) => {
  dispatch({
    type: INCREASE,
    payload: data,
  });
};

const decrementHandler = () => {
  dispatch({
    type: DECREMENT,
  });
};

const toggleCounterHandler = () => {
  dispatch({
    type: TOGGLE,
  });
};
```

## React Tool Kit

- `Redux Tool Kit`, 즉 **RTK**는 리덕스의 몇 가지 특징을 단순화시켰다.

### createSlice 

- `action value`, `action type`, `initial state`, `reducer`를 한번에 작성할 수 있다.
- 만약 상태가 여러 조각으로 나누어져 있으면, 객체를 인자로 생성하여 전역 상태의 `slice`를 미리 만들어야 한다.

```javascript
const initialState = {
  counter: 0,
  showCounter: true,
};

const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    increment(state) {
      state.counter += 1;
    },
    decrement(state) {
      state.counter -= 1;
    },
    increase(state, action) {
      state.counter += action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});
```

## RTK state 연결하기

- 기본 리덕스에 사용되는 `combineReducers()`를 사용할 수도 있지만, **RTK**를 사용한다면 `configureStore()`를 사용한다.
- `configureStore()`에 설정 객체를 전달한다.
- 설정 객체에서 `configureStore()`가 요구하는 리듀서 프로퍼리틀 정한다.
  - ❗ `reducers`가 아니라 `reducer`다.
 
```javascript
const store = configureStore({
  reducer: counterSlice.reducer,
});
```

- 또는 `key` 값을 설정해서 리듀서 맵을 생성할 수 있다.

```javascript
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});
```

- 이 맵은 주요 리듀서의 값이 되고, `configureStore()`가 모든 리듀서를 하나의 큰 `reducer`로 병합한다.

### RTK 액션 전달하기

- `createSlice()`는 서로 다른 리듀서에 해당되는 고유 액션 식별자를 자동으로 생성한다.
- 이 액션 값은 `슬라이드.actions`를 통해 얻을 수 있다.
  - `createSlice()`의 `reducers` 영역에 있는 메서드 이름과 매칭된 `key`로 가득한 객체이다.
- 이 `actions`의 객체에서 어느 한 `key`에 접근한다면, `reducers`의 메서드에 접근할 필요가 없다.
  - **RTK**에 의해 자동으로 생성된 메서드가 생기고, 액션 생성자라고도 불리는 그 메서드가 호출되면 자동으로 액션 객체가 생성된다.
  - 이 객체는 **액션마다 다른 고유 식별자**와 `type` 속성을 가지고 있다. (**액션 식별자에 신경을 쓸 필요가 없어진다.**)
- 액션 생성자 메서드를 실행해서 리듀서 메서드와 이름이 같으면 액션을 전달한다.
- 보통 이런 식으로 저장소와 함께 `action`을 내보낸다.

```javascript
export const counterActions = counterSlice.actions;
```

- 만약 **RTK**의 액션 생성자 메서드를 통해 `payload`를 전달할려면 그냥 함수에 인자로 전달하면 된다.
  - **RTK**가 자동으로 액션 생성자를 생성해서, **RTK**가 생성한  `type: SOME_UNIQUE_IDENTIFIER`를 전달한다.
  - 그 후, 인자로서 실행하고자 하는 액션 메서드에 전달한 값을 추가 필드명이 `paylaod`인 곳에 저장한다.

> 필드명은 임의로 정할 수 없고, **RTK**가 기본값으로 사용하는 필드명을 이용한다.

```javascript
const increaseHandler = (payload) => {
  dispatch(counterActions.increase(payload));
};
```


### RTK 사용 전후 비교

- 전

```javascript
import { useSelector, useDispatch } from 'react-redux';
import classes from './Counter.module.css';
import { INCREASE, INCREMENT, DECREMENT, TOGGLE } from '../store/index';

const Counter = () => {
  const dispatch = useDispatch();

  const counter = useSelector((state) => state.counter);
  const showCounter = useSelector((state) => state.showCounter);

  const incrementHandler = () => {
    dispatch({
      type: INCREMENT,
    });
  };

  const increaseHandler = (data) => {
    dispatch({
      type: INCREASE,
      payload: data,
    });
  };

  const decrementHandler = () => {
    dispatch({
      type: DECREMENT,
    });
  };

  const toggleCounterHandler = () => {
    dispatch({
      type: TOGGLE,
    });
  };

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>{counter}</div>
      {showCounter && (
        <div className="counter">
          <button onClick={incrementHandler}>Increment</button>
          <button onClick={() => increaseHandler(5)}>Increment by 5</button>
          <button onClick={decrementHandler}>Decrement</button>
        </div>
      )}
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

- 후

```javascript
import { useSelector, useDispatch } from 'react-redux';
import classes from './Counter.module.css';
import { counterActions } from '../store/index';


const Counter = () => {
  const dispatch = useDispatch();

  const counter = useSelector((state) => state.counter);
  const showCounter = useSelector((state) => state.showCounter);

  const incrementHandler = () => {
    dispatch(counterActions.increment());
  };

  const increaseHandler = (payload) => {
    dispatch(counterActions.increase(payload));
  };

  const decrementHandler = () => {
    dispatch(counterActions.decrement());
  };

  const toggleCounterHandler = () => {
    dispatch(counterActions.toggleCounter());
  };

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>{counter}</div>
      {showCounter && (
        <div className="counter">
          <button onClick={incrementHandler}>Increment</button>
          <button onClick={() => increaseHandler(5)}>Increment by 5</button>
          <button onClick={decrementHandler}>Decrement</button>
        </div>
      )}
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

```

- **RTK**를 사용하면서, 짧고 간결하며, 유지보수가 좀 더 편해졌다.
- 그렇지만 더 복잡한 애플리케이션이면 리덕스를 사용하는게 더 쉽다.
- 다중으로 리덕스를 접근하려면 `key` 값에 접근하는 것처럼 하면 된다.

```javascript
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
    auth: authSlice.reducer,
  },
});

export const counterActions = counterSlice.actions;
export const authActions = authSlice.actions;
```
```javascript
const counter = useSelector((state) => state.counter.counter);
const showCounter = useSelector((state) => state.counter.showCounter);
```


## RTK 코드 분할

- 비대해진 루트 리덕스를 분할한다.
- 루트에는 `import`로 리덕스의 `reducer`만 받고, `configureStore()`로 `reducer`를 구성한다.
- 분할된 파일에는 각각의 리덕스와 `action`까지 포함한다.

<details>
  <summary>코드 보기</summary>

```javascript
// index.js
import { configureStore } from '@reduxjs/toolkit';

import counterReducer from './counter';
import authReducer from './auth';

const store = configureStore({
  reducer: {
    counter: counterReducer,
    auth: authReducer,
  },
});

export default store;
```

```javascript
// counter.js
import { createSlice } from '@reduxjs/toolkit';

const initialCounterState = {
  counter: 0,
  showCounter: true,
};

const counterSlice = createSlice({
  name: 'counter',
  initialState: initialCounterState,
  reducers: {
    increment(state) {
      state.counter += 1;
    },
    decrement(state) {
      state.counter -= 1;
    },
    increase(state, action) {
      state.counter += action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

export const counterActions = counterSlice.actions;

export default counterSlice;
```
```javascript
// auth.js
import { createSlice } from '@reduxjs/toolkit';

const initialAuthState = {
  isAuthenticated: false,
};

const authSlice = createSlice({
  name: 'authentication',
  initialState: initialAuthState,
  reducers: {
    login(state) {
      state.isAuthenticated = true;
    },
    logout(state) {
      state.isAuthenticated = false;
    },
  },
});

export const authActions = authSlice.actions;

export default authSlice.reducer;
```
</details>

### RTK 정리

- `store(저장소)`를 만들려면 `configureStore()`로 구성한다.
  - 변수로 루트 리듀서를 할당한 객체를 내보낸다.
    - 이때, 이 객체는 하나의 리듀서 함수를 가지고 있거나, 리듀서 맵을 설정해서 여러 개의 리듀서를 내보낼 수 있다.
- `useSelector()`를 통해 데이터를 사용할 수 있다.
  - 인자로는 `state`의 할당된 명명을 사용하여, `state.이름` 이런 방식으로 사용한다.
    - 만약 리듀서 맵을 설정하여 여러 개의 리듀서를 내보내고 있다면, `state.리듀서키값.이름` 이런 방식으로 접근한다.
- `useDispatch()`로 데이터의 상태를 변경할 수 있다.
- 리덕스 파일에서 액션을 `import`해서 `dispatch(액션명.액션)`을 통하여 데이터를 변경한다.
  - 이 때, 액션명은 `actions`로 `export`하는 것들이고, 액션은 `createSlice()`의 `reducers`에 있다. 

## 리덕스 구성 정리  with 참고안하고 직접 만들어 본 리덕스 코드

- 루트 리듀서 작성
  - `deprecated`된 `createStore()`를 사용하지 않고, `configureStore()`를 사용하였다.

```javascript
// index.js
import { configureStore } from '@reduxjs/toolkit';
import { combineReducers } from 'redux';
import showShoppingCartReducer from './shopping-cart';

const rootReducer = combineReducers({
  showShoppingCartReducer,
});

const store = configureStore({
  reducer: rootReducer,
});

export default store;
```

- 세부 리듀서 작성
  - 이 때, 액션 상수, 액션 함수, 리듀서 순으로 정리하면 좀 더 가독성이 좋다.
 
```javascript
//shopping-cart.js
export const SHOWSHOPPINGCART = 'shoppingCart/SHOWSHOPPINGCART';
export const NOTSHOWSHOPPINGCART = 'shoppingCart/NOTSHOWSHOPPINGCART';
export const ADDITEMTOSHOPPINGCART = 'shoppingCart/ADDITEMTOSHOPPINGCART';
export const REMOVEITEMFROMSHOPPINGCART =
  'shoppingCart/REMOVEITEMFROMSHOPPINGCART';

export const showMeMyShoppingCart = () => ({
  type: SHOWSHOPPINGCART,
});
export const notShowMeMyShoppingCart = () => ({
  type: NOTSHOWSHOPPINGCART,
});
export const addItem = (product) => ({
  type: ADDITEMTOSHOPPINGCART,
  payload: product,
});
export const removeItem = (product) => ({
  type: REMOVEITEMFROMSHOPPINGCART,
  payload: product,
});

const initialState = {
  showShoppingcart: false,
  items: [],
};

const showShoppingCartReducer = (state = initialState, action) => {
  switch (action.type) {
    case SHOWSHOPPINGCART:
      return {
        ...state,
        showShoppingcart: true,
      };

    case NOTSHOWSHOPPINGCART:
      return {
        ...state,
        showShoppingcart: false,
      };

    case ADDITEMTOSHOPPINGCART: {
      const existItemIdx = state.items.findIndex(
        (item) => item.title === action.payload.title,
      );

      const updatedItems =
        existItemIdx !== -1
          ? state.items.map((item, index) =>
              index === existItemIdx
                ? { ...item, quantity: item.quantity + 1 }
                : item,
            )
          : [...state.items, { ...action.payload, quantity: 1 }];

      return {
        ...state,
        items: updatedItems,
      };
    }

    case REMOVEITEMFROMSHOPPINGCART: {
      const existItemIdx = state.items.findIndex(
        (item) => item.title === action.payload.title,
      );
      if (existItemIdx === -1) return state;
      if (state.items[existItemIdx].quantity === 1) {
        const deleteTheItem = [...state.items];
        deleteTheItem.splice(existItemIdx, 1);
        return {
          ...state,
          items: deleteTheItem,
        };
      }
      const updatedItem = {
        ...state.items[existItemIdx],
        quantity: state.items[existItemIdx] - 1,
      };

      return {
        ...state,
        items: [...state.items, updatedItem],
      };
    }

    default:
      return state;
  }
};

export default showShoppingCartReducer;
```

- 사용법
  - `useSelector`를 사용하여 선택
  - `useDispatch`를 통해 액션
 
```javascript
import { useDispatch, useSelector } from 'react-redux';
import CartButton from '../Cart/CartButton';
import classes from './MainHeader.module.css';
import {
  notShowMeMyShoppingCart,
  showMeMyShoppingCart,
} from '../store/shopping-cart';

function MainHeader() {
  const dispatch = useDispatch();

  const isShow = useSelector(
    (state) => state.showShoppingCartReducer.showShoppingcart,
  );

  const handleClick = () => {
    if (isShow) {
      dispatch(notShowMeMyShoppingCart());
    } else {
      dispatch(showMeMyShoppingCart());
    }
  };

  return (
    <header className={classes.header}>
      <h1>ReduxCart</h1>
      <nav>
        <ul>
          <li>
            <CartButton onClick={handleClick} />
          </li>
        </ul>
      </nav>
    </header>
  );
}

export default MainHeader;
```
