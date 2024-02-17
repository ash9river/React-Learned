# Advanced Redux

## Redux and Side Effects with Async

- 리덕스의 중요한 규칙
  - 리듀서 함수는 `pure`, `side-effect free`, `synchoronous`여야 한다.(순수 함수, 사이드 이펙트 x, 동기식)
  - 리덕스 리듀서는 이전 상태 및 액션인 입력이 무조건 필요하며, 새로운 상태인 출력을 무조건 만들어낸다.
- 리덕스를 작업할 때 비동기 코드는 두 곳에 위치한다.
  1. `useEffect`를 사용하여 컴포넌트에 직접 비동기 코드를 작성한다.
  2. 리덕스 툴킷을 사용하지 않고, 직접 `action creator`를 작성한다.
      - 리덕스는 실제로 `action creator`의 일부로 `side effects`를 수행하고, 비동기 작업을 실행할 수 있는 솔루션이 있다.

## 리덕스 복습

- 만약 구성을 이렇게 했을 때, 상태와 액션 사용법

<details>
  <summary>구성 코드</summary>

```javascript
// index.js
import { configureStore } from '@reduxjs/toolkit';
import uiSlice from './ui-slice';

const store = configureStore({
  reducer: {
    ui: uiSlice.reducer,
  },
});

export default store;
```
```javascript
// ui-slice.js
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  cartIsVisible: false,
};

const uiSlice = createSlice({
  name: 'ui',
  initialState,
  reducers: {
    toggle(state) {
      state.cartIsVisible = !state.cartIsVisible;
    },
  },
});

export const uiActions = uiSlice.actions;

export default uiSlice;
``` 
</details>

- 액션은 `import`해서 사용.

```javascript
const handleClick = () => {
  dispatch(uiActions.toggle());
};
```

- 상태 접근은 `state.리듀서키값.상태명`

```javascript
const showCart = useSelector((state) => state.ui.cartIsVisible);
```

## 리덕스 및 비동기 코드

- 리덕스 안에서 동기든 비동기든 간에 `side effect`를 일으키는 것은 불가능하다.
- 비동기 방식을 사용하고자 한다면, 컴포넌트의 비동기 코드 안에서 실행하거나, 액션 생성자를 이용한다.
- 백엔드가 어느 정도의 논리까지 처리하느냐에 따라서 프론트엔드의 코드도 변하여서, 적절히 선택한다.
- `side effect`의 유무와 비동기 코드인가 아닌가를 확인하고, 서로 분리해야한다.
- 만약 동기식이면서 `side effect` 가 없으면, `reducers`를 선택하는 편이 낫다.
- 만약 비동기식이거나 `side effect`가 있으면, 액션 생성자를 만드는 것이나 컴포넌트 속에서 비동기를 선택하는 편이 낫다.(절대 리듀서를 쓰면 안된다.)

### 컴포넌트 비동기 방식

- 컴포넌트에서는 `state`가 변경하지 않도록, 불변성을 유지하는 것이 중요하다.
- `useEffect`를 활용하여, 리덕스의 저장소가 최신화될때마다, 백엔드에 **HTTP** 통신을 요청한다.
- 모든 `side effect`를 컴포넌트에 넣는 방식이고, 잘 작동되지만 컴포넌트의 비대화가 일어난다.

<details>
  <summary>코드 보기</summary>

```javascript
import { useDispatch, useSelector } from 'react-redux';
import { useEffect } from 'react';

import Cart from './components/Cart/Cart';
import Layout from './components/Layout/Layout';
import Products from './components/Shop/Products';
import { uiActions } from './components/store/ui-slice';
import Notification from './components/UI/Notification';

let isInitial = true;

function App() {
  const dispatch = useDispatch();

  const showCart = useSelector((state) => state.ui.cartIsVisible);
  const cart = useSelector((state) => state.cart.items);
  const notification = useSelector((state) => state.ui.notification);

  useEffect(() => {
    const sendCartData = async () => {
      dispatch(
        uiActions.showNotification({
          status: 'pending',
          title: 'Sending...',
          message: 'Sending cart data',
        }),
      );

      const response = await fetch(
        'https://파이어베이스링크',
        {
          method: 'PUT',
          body: JSON.stringify(cart),
        },
      );

      if (!response.ok) {
        dispatch(
          uiActions.showNotification({
            status: 'error',
            title: 'Error!',
            message: 'Sending cart data failed!',
          }),
        );
      }

      dispatch(
        uiActions.showNotification({
          status: 'success',
          title: 'Success!',
          message: 'Sent cart data successfully!',
        }),
      );
    };

    if (isInitial) {
      isInitial = false;
      return;
    }

    sendCartData();
  }, [cart]);

  return (
    <>
      {notification && (
        <Notification
          status={notification.status}
          title={notification.title}
          message={notification.message}
        />
      )}
      <Layout>
        {showCart && <Cart />}
        <Products />
      </Layout>
    </>
  );
}

export default App;
```
</details>

### 액션 생성자 Thunk

- `Thunk` : 다른 작업이 완성될 때까지 작업을 지연시키는 함수(리덕스 미들웨어).
- `Thunk`를 이용하여 액션이 아닌 함수를 디스패치하고, 비동기 작업을 할 수 있다.
- 이 `Thunk` 미들웨어를 통해 외부 API와의 통신하며, 비동기적인 작업을 처리한다.
  - 그 후, 해당 결과를 기반으로 액션을 디스패치하여 상태를 업데이트한다.

```javascript
import { cartActions } from './cart-slice';
import { uiActions } from './ui-slice';

export const fetchCartData = () => {
  return async (dispatch) => {
    const fetchData = async () => {
      const response = await fetch(
        'https://react-http-b31f8-default-rtdb.asia-southeast1.firebasedatabase.app/cart.json',
      );
      if (!response.ok) {
        throw new Error('Could not fetch cart data!');
      }

      const data = await response.json();

      return data;
    };

    try {
      const cartData = await fetchData();

      dispatch(
        cartActions.replaceCart({
          items: cartData.items || [],
          totalQuantity: cartData.totalQuantity,
        }),
      );
    } catch (err) {
      dispatch(
        uiActions.showNotification({
          status: 'error',
          title: 'Error!',
          message: 'Fetching cart data failed!',
        }),
      );
    }
  };
};

export const sendCartData = (cart) => {
  return async (dispatch) => {
    dispatch(
      uiActions.showNotification({
        status: 'pending',
        title: 'Sending...',
        message: 'Sending cart data',
      }),
    );
    const sendRequest = async () => {
      const response = await fetch(
        'https://react-http-b31f8-default-rtdb.asia-southeast1.firebasedatabase.app/cart.json',
        {
          method: 'PUT',
          body: JSON.stringify({
            items: cart.items,
            totalQuantity: cart.totalQuantity,
          }),
        },
      );

      if (!response.ok) {
        throw new Error('Sending cart data failed.');
      }
    };
    try {
      await sendRequest();

      dispatch(
        uiActions.showNotification({
          status: 'success',
          title: 'Success!',
          message: 'Sent cart data successfully!',
        }),
      );
    } catch (err) {
      dispatch(
        uiActions.showNotification({
          status: 'error',
          title: 'Error!',
          message: 'Sending cart data failed!',
        }),
      );
    }
  };
};
```

- 여러 가지 상황도 고려하였다.

```javascript
  useEffect(() => {
    dispatch(fetchCartData());
  }, [dispatch]);

  useEffect(() => {
    if (isInitial) {
      isInitial = false;
      return;
    }

    if (cart.changed) {
      dispatch(sendCartData(cart));
    }
  }, [cart]);
```

### 요약

- 리덕스에 `side effect`나 비동기 효과를 만들려면 컴포넌트나 액션 생성자로 만들어야 한다.
![image](https://github.com/ash9river/React-Learned/assets/121378532/a8ad514c-bad6-47c9-9524-bb2ab67eb167)
- 리덕스 스토어와 상태, 그리고 액션에 대해 이해할 수 있게 되었다.
- 그러나 `redux-saga`에 대해선 배우지 않아서 차후에 따로 배워야겠다.




