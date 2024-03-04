# Context API & useReducer

## Props Drilling

- 컴포넌트 트리에서 데이터를 하위 컴포넌트로 전달하기 위해 중간 컴포넌트를 통해 `property`를 내려주는 것
- 중간 컴포넌트는 원하는 자식 컴포넌트에게 `property`를 전달하기 위해 필요하다.
- 그러나, 해당 값을 직접 사용하지 않는 경우에도 `property`를 받고 전달해야 한다.

### 장점

- 명시적인 값의 사용
- 값 추적이 편해진다.
- 코드 변경 파악이 편해진다.


### 단점

- `property` 데이터 형식 변경의 불편
- 중간 컴포넌트에 불필요한 `property` 전달
- 누락된 `property` 파악 어려움
- `property` 이름 변경 추적의 어려움

### 해결법

- `Context API`
- `Redux` 또는 다른 상태 관리 라이브러리
- `Custom Hooks`
- `Render Props`과 `children props`

## Context API

- `children props`을 이용한 컴포넌트 합성으로 해결할 수 있지만, 상위 컴포넌트가 비대해질 수 있으므로, `Context API`의 사용도 고려해봐야 한다.
- `state`와 연결이 쉽다.
- 관습적으로 `Context API`를 저장하는 폴더를 `store`로 지정한다.

### Provider로 Context 이용가능하다.

- `Provide`로 `Context` 이용할 때, `Context`가 전송하는 `value` 데이터는 객체 형태이다.
- 객체에서 하나의 데이터만 변경되어도 `Provider`로 감싼 모든 자식 컴포넌트에서 리렌더링이 일어난다.

### 해결법

- `Context` 데이터를 가지고 있는 컴포넌트에 `useMemo hook`을 이용하여 데이터를 캐싱하여 불필요한 리렌더링 방지
- `Context` 쪼개서 여러 개를 사용한다.
- `React.memo` 고차함수 사용하여 자식 컴포넌트의 불필요한 리렌더링 방지한다.

- 나의 해결법은 `useMemo hook`을 이용했다.

```javascript
const [shoppingCart, setShoppingCart] = useState({ items: [] });

  const valueCtx = useMemo(
    () => ({
      items: shoppingCart.items,
    }),
    [shoppingCart],
  return (
    <CartContext.Provider value={valueCtx}>
      <Header />
      <Shop />
    </CartContext.Provider>
  );
```

## Context 소비

- `useContext`로 이용한다.
- 비구조화 할당도 가능하다.
```javascript
const cartCtx=useContext(CartContext);

const totalPrice = cartCtx.items.reduce(
    (acc, item) => acc + item.price * item.quantity,
    0
  );
```

## Context와 State 연결

```javascript
import { useState, useMemo } from 'react';

import Header from './components/Header';
import Shop from './components/Shop';
import Product from './components/Product';
import { DUMMY_PRODUCTS } from './dummy-products';
import { CartContext } from './store/ShoppingCardContext';

function App() {
  const [shoppingCart, setShoppingCart] = useState({ items: [] });

  function handleAddItemToCart(id) {
    // 생략
  }

  function handleUpdateCartItemQuantity(productId, amount) {
    // 생략
  }

  const valueCtx = useMemo(
    () => ({
      items: shoppingCart.items,
      addItemToCart: handleAddItemToCart,
      updateCartItemQuantity: handleUpdateCartItemQuantity,
    }),
    [shoppingCart],
  );

  return (
    <CartContext.Provider value={valueCtx}>
      <Header />
      <Shop />
    </CartContext.Provider>
  );
}

export default App;
```

### Context 추적을 위한 익명 함수

- 익명 함수로 만들어두어서 자동완성을 통해, Context 추적을 편하게 하였다.

```javascript
import { createContext } from 'react';

export const CartContext = createContext({
  items: [],
  addItemToCart: () => {},
  updateCartItemQuantity: () => {},
});
```

## Context Consumer

- `useContext Hook`을 사용하지 않는 접근법
- 컨텍스트 값에 대한 액세스를 가진 JSX 코드를 묶음
- 그러나 복잡해서 보통 `useContext`를 더 선호한다.

```javascript
import { useContext } from 'react';

import { CartContext } from '../store/ShoppingCardContext';

export default function Cart({ onUpdateItemQuantity }) {

  return (
    <CartContext.Consumer>
      {(cartCtx) => {
        const totalPrice = cartCtx.items.reduce((acc, item) => acc + item.price * item.quantity, 0);
        const formattedTotalPrice = `$${totalPrice.toFixed(2)}`;
        return (
          <div id="cart">
            {cartCtx.items.length === 0 && <p>No items in cart!</p>}
            {cartCtx.items.length > 0 && (
              <ul id="cart-items">
                {cartCtx.items.map((item) => {
                  const formattedPrice = `$${item.price.toFixed(2)}`;

                  return (
                    <li key={item.id}>
                      <div>
                        <span>{item.name}</span>
                        <span> ({formattedPrice})</span>
                      </div>
                      <div className="cart-item-actions">
                        <button onClick={() => onUpdateItemQuantity(item.id, -1)}>-</button>
                        <span>{item.quantity}</span>
                        <button onClick={() => onUpdateItemQuantity(item.id, 1)}>+</button>
                      </div>
                    </li>
                  );
                })}
              </ul>
            )}
            <p id="cart-total-price">
              Cart Total: <strong>{formattedTotalPrice}</strong>
            </p>
          </div>
        );
      }}
    </CartContext.Consumer>
  );
}
```

## Context 멀티소싱 & 분리된 컴포넌트에 State 연동

- 상위 컴포넌트에 `Context`를 비롯한 다른 로직이 너무나도 많아져서 상위 컴포넌트가 비대해지는 문제가 발생한다.
- 해결법으로 `Context`를 다른 컴포넌트로 분리

### 예시

-  App.jsx 파일

```javascript
import Header from './components/Header';
import Shop from './components/Shop';
import CartContextProvider from './store/ShoppingCardContext';

function App() {
  return (
    <CartContextProvider>
      <Header />
      <Shop />
    </CartContextProvider>
  );
}

export default App;
```

- ShoppingCardContext.jsx 파일

```javascript
import { createContext, useMemo, useState } from 'react';

import { DUMMY_PRODUCTS } from '../dummy-products';

export const CartContext = createContext({
  items: [],
  addItemToCart: () => {},
  updateCartItemQuantity: () => {},
});

export default function CartContextProvider({ children }) {
  const [shoppingCart, setShoppingCart] = useState({ items: [] });

  function handleAddItemToCart(id) {
    setShoppingCart((prevShoppingCart) => {
      const updatedItems = [...prevShoppingCart.items];

      const existingCartItemIndex = updatedItems.findIndex((cartItem) => cartItem.id === id);
      const existingCartItem = updatedItems[existingCartItemIndex];

      if (existingCartItem) {
        const updatedItem = {
          ...existingCartItem,
          quantity: existingCartItem.quantity + 1,
        };
        updatedItems[existingCartItemIndex] = updatedItem;
      } else {
        const product = DUMMY_PRODUCTS.find((products) => products.id === id);
        const newItem = {
          id,
          name: product.title,
          price: product.price,
          quantity: 1,
        };
        updatedItems.push(newItem);
      }

      return {
        items: updatedItems,
      };
    });
  }

  function handleUpdateCartItemQuantity(productId, amount) {
    setShoppingCart((prevShoppingCart) => {
      const updatedItems = [...prevShoppingCart.items];
      const updatedItemIndex = updatedItems.findIndex((item) => item.id === productId);

      const updatedItem = {
        ...updatedItems[updatedItemIndex],
      };

      updatedItem.quantity += amount;

      if (updatedItem.quantity <= 0) {
        updatedItems.splice(updatedItemIndex, 1);
      } else {
        updatedItems[updatedItemIndex] = updatedItem;
      }

      return {
        items: updatedItems,
      };
    });
  }

  const valueCtx = useMemo(
    () => ({
      items: shoppingCart.items,
      addItemToCart: handleAddItemToCart,
      updateCartItemQuantity: handleUpdateCartItemQuantity,
    }),
    [shoppingCart],
  );

  return <CartContext.Provider value={valueCtx}>{children}</CartContext.Provider>;
}
```

## 상태 관리를 위한 useReducer

- `Reducer` : 복잡한 값을 하나의 단순한 값으로 바꾸는 함수
- 불편한 반복을 피하기 위해서 `useState`가 아닌 `useReducer`를 이용하여 상태 관리.
- `useState`와는 다르게 `state`를 업데이트하는 로직을 다른 컴포넌트에 작성할 수 있고, 주로 복잡한 여러 상태를 다룰 때 사용한다.
- 첫 인자로는 다루려는 `state`가 필요하다.
- 다음 인자로는 `action`을 보낼 수 있는 `dispatch`함수가 필요하다.
- `useReducer` 안에는 상태 제어 함수가 필요하고, 처음 상태는 필요하면 넣는다.

```javascript
const [state, dispatch]=useReducer(theFunctionControlTheState, initialState);
```

### 컴포넌트 바깥에 함수를 정의하는 이유

1. 컴포넌트가 실행될 때마다 컴포넌트 바깥의 `Reducer` 함수가 재생성되지 않도록 한다.
2. 컴포넌트에서 정의 또는 업데이트되는 그 어떤 값에도 직접적인 액세스를 필요로 하지 않는다.
  - 속성에 접근할 필요가 없다.

### Reducer 함수의 인자로 state와 action

- `useReducer`의 `dispatch`를 통해 보내진 후에 리액트가 리듀서 함수를 호출할 것이기에 `state`와 `action`이 필요하다.
- `action`을 줄 때, type에 `string`으로 명시적으로 상태를 관리하고, 패러미터로 `payload`라 명시한다.

```javascript
shoppingCartDispatch({
      type: 'ADD_ITEM',
      payload: id,
    });
```

### 예시

- `switch`로 액션 타입을 나눌 수도 있지만, 나는 `if`를 더 선호해서 `if`로 하였다.

> 리액트를 다루는 기술에서 본 `action`과 `payload`의 의미를 이를 통해서 겨우 깨닫고, 진짜 감탄했다. 아는만큼 보인다는 사실이 진짜였구나... <br/>
> 앞으로 useReducer 자주 애용해야겠다.

```javascript
import { createContext, useMemo, useState, useReducer } from 'react';

import { DUMMY_PRODUCTS } from '../dummy-products';

export const CartContext = createContext({
  items: [],
  addItemToCart: () => {},
  updateCartItemQuantity: () => {},
});

function shoppingCartReducer(state, action) {
  if (action.type === 'ADD_ITEM') {
    const updatedItems = [...state.items];

    const existingCartItemIndex = updatedItems.findIndex((cartItem) => cartItem.id === action.payload);
    const existingCartItem = updatedItems[existingCartItemIndex];

    if (existingCartItem) {
      const updatedItem = {
        ...existingCartItem,
        quantity: existingCartItem.quantity + 1,
      };
      updatedItems[existingCartItemIndex] = updatedItem;
    } else {
      const product = DUMMY_PRODUCTS.find((products) => products.id === action.payload);
      const newItem = {
        id: product.id,
        name: product.title,
        price: product.price,
        quantity: 1,
      };
      updatedItems.push(newItem);
    }
    return {
      ...state,
      items: updatedItems,
    };
  }

  if (action.type === 'UPDATE_ITEM') {
    const updatedItems = [...state.items];
    const updatedItemIndex = updatedItems.findIndex((item) => item.id === action.payload.productId);

    const updatedItem = {
      ...updatedItems[updatedItemIndex],
    };

    updatedItem.quantity += action.payload.amount;

    if (updatedItem.quantity <= 0) {
      updatedItems.splice(updatedItemIndex, 1);
    } else {
      updatedItems[updatedItemIndex] = updatedItem;
    }

    return {
      ...state,
      items: updatedItems,
    };
  }

  return state;
}

export default function CartContextProvider({ children }) {
  const [shoppingCartState, shoppingCartDispatch] = useReducer(shoppingCartReducer, { items: [] });

  function handleAddItemToCart(id) {
    shoppingCartDispatch({
      type: 'ADD_ITEM',
      payload: id,
    });
  }

  function handleUpdateCartItemQuantity(productId, amount) {
    shoppingCartDispatch({
      type: 'UPDATE_ITEM',
      payload: {
        productId,
        amount,
      },
    });
  }

  const valueCtx = useMemo(
    () => ({
      items: shoppingCartState.items,
      addItemToCart: handleAddItemToCart,
      updateCartItemQuantity: handleUpdateCartItemQuantity,
    }),
    [shoppingCartState],
  );

  return <CartContext.Provider value={valueCtx}>{children}</CartContext.Provider>;
}
```
