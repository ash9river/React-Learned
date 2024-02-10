# Make a Food Order App

## 내가 먼저 해결한 소스들

- 백엔드에서 `axios`를 사용하여, `Custom Hook`인 `useInput`을 통해 데이터를 불러왔다.
- 그 데이터를 `Context API`를 사용하고 `useMemo`를 통해 전파하였다.
- 또한, `useReducer`를 통해 상태를 관리하였다.

<details>
  <summary>소스 코드</summary>

- `App` 컴포넌트

```javascript
import Header from './components/Header';
import Menu from './components/Menu';
import MealItemContextProvider from './store/MealItemContext';

function App() {
  return (
    <MealItemContextProvider>
      <Header />
      <Menu />
    </MealItemContextProvider>
  );
}

export default App;
```

- `axios`를 통한 데이터 받기

```javascript
import axios from 'axios';

export async function getItems() {
  return new Promise((resolve, reject) => {
    (async () => {
      try {
        const res = await axios.get('http://localhost:3000/meals');
        resolve(res.data);
      } catch (err) {
        reject(err);
      }
    })();
  });
}
```

- `Custom Hook`

```javascript
import { useEffect, useState } from 'react';

export function useFetch(fetchFn, initVal) {
  const [isFetching, setIsFetching] = useState();
  const [fetchedData, setFetchedData] = useState(initVal);
  const [error, setError] = useState();

  useEffect(() => {
    async function fetchData() {
      setIsFetching(true);
      try {
        const res = await fetchFn();
        setFetchedData(res);
      } catch (err) {
        setError({
          message: err || 'Fail to fetch Data.',
        });
      }
      setIsFetching(false);
    }

    fetchData();
  }, [fetchFn]);

  return {
    isFetching,
    fetchedData,
    setFetchedData,
    error,
  };
}
```

- `Context`와 상태관리하는 `useReducer`

```javascript
import { createContext, useMemo, useReducer } from 'react';

import { getItems } from '../http/util';
import { useFetch } from '../hooks/useFetch';

export const MealItemContext = createContext({
  items: [],
  cartItems: [],
  handleAddCartItems: () => {},
  updateCartItems: () => {},
});

function shoppingCartReducer(state, action) {
  if (action.type === 'ADD_ITEM') {
    const updateItems = [...state.cartItems];

    const existIdx = updateItems.findIndex(
      (prevExistingItems) =>
        prevExistingItems.item.id === action.payload.item.id,
    );

    if (existIdx === -1) {
      const newItem = {
        item: { ...action.payload.item },
        quantity: 1,
      };

      updateItems.push(newItem);
    } else {
      const plusItem = {
        ...updateItems[existIdx],
        quantity: updateItems[existIdx].item.quantity + 1,
      };

      updateItems[existIdx] = plusItem;
    }

    return {
      ...state,
      cartItems: updateItems,
    };
  }

  if (action.type === 'UPDATE_ITEM') {
    const updateItems = [...state.cartItems];

    const updateItemsIdx = updateItems.findIndex(
      (prevExistingItems) =>
        prevExistingItems.item.id === action.payload.item.id,
    );

    const wannaUpdateItem = {
      ...updateItems[updateItemsIdx],
      quantity: updateItems[updateItemsIdx].quantity + action.payload.amount,
    };

    if (wannaUpdateItem.quantity <= 0) {
      updateItems.splice(updateItemsIdx, 1);
    } else {
      updateItems[updateItemsIdx] = wannaUpdateItem;
    }
    return {
      ...state,
      cartItems: updateItems,
    };
  }

  return state;
}

export default function MealItemContextProvider({ children }) {
  const {
    isFetching,
    fetchedData: mealItems,
    setFetchedData,
    error,
  } = useFetch(getItems, []);

  const [shoppingCart, dispatch] = useReducer(shoppingCartReducer, {
    cartItems: [],
  });

  function addCart(item) {
    dispatch({
      type: 'ADD_ITEM',
      payload: { item },
    });
  }

  function updateCart(item, amount) {
    dispatch({
      type: 'UPDATE_ITEM',
      payload: {
        item,
        amount,
      },
    });
  }

  const ctxVal = useMemo(() => {
    return {
      items: mealItems,
      cartItems: shoppingCart.cartItems,
      handleAddCartItems: addCart,
      updateCartItems: updateCart,
    };
  }, [mealItems, shoppingCart]);

  if (isFetching || error) {
    return null;
  }

  return (
    <MealItemContext.Provider value={ctxVal}>
      {children}
    </MealItemContext.Provider>
  );
}
```

- `Context` 소비 

```javascript
import { useContext } from 'react';

import { getItems } from '../http/util';
import { useFetch } from '../hooks/useFetch';
import { MealItemContext } from '../store/MealItemContext';

export default function Menu() {
  const { items: mealItems, handleAddCartItems: handleClick } =
    useContext(MealItemContext);
  return (
    <div id="meals">
      {mealItems.map((items) => {
        return (
          <article key={items.id} className="meal-item">
            <img src={`http://localhost:3000/${items.image}`} alt="err" />
            <h3>{items.name}</h3>
            <div className="meal-item-price">${items.price}</div>
            <p className="meal-item-description">{items.description}</p>
            <button
              className="button"
              key={`${items.id}button`}
              onClick={() => handleClick(items)}
            >
              Add to Cart
            </button>
          </article>
        );
      })}
    </div>
  );
}
```
  
</details>

## reduce 함수 사용

- `reduce()` 함수를 사용해서 내부의 값들을 뽑을 수 있었다.

```javascript
  const totalCartItems = items.reduce((totalNumberOfItems, item) => {
    return totalNumberOfItems + items.quantity;
  }, 0);
```

## useEffect를 사용한 모달

- `forwardRef`를 사용해서 참조를 통해 모달을 제어할 수 있지만, `useEffect`로도 모달을 제어할 수 있다.
- **React** 공식 문서에도 예시가 있다.
- `cleanup` 시에 모달의 종료를 이끌어낸다.
  
```javascript
import { useEffect, useRef } from 'react';
import { createPortal } from 'react-dom';

export default function Modal({ children, open, className = '' }) {
  const dialog = useRef();

  const modalClassName = className === null ? 'modal' : `modal ${className}`;

  useEffect(() => {
    if (open) {
      dialog.current.showModal();
    } 
    
    return ()=>{
        dialog.current.close();
    }
  }, [open]);

  return createPortal(
    <dialog ref={dialog} className={modalClassName}>
      {children}
    </dialog>,
    document.getElementById('modal'),
  );
}
```

- `dialog`의 `current` 속성을 `useEffect`의 안에 저장하고, 그 값을 사용하는 것이 권장된다.

> ❗ `cleanup`이 더 나중에 실행되기 때문에, 이론적으로 그 사이에 `ref`의 값이 변할 수도 있기 때문이다. <br/>
> 필수적이지는 않지만 권장되는 패턴이다.

```javascript
import { useEffect, useRef } from 'react';
import { createPortal } from 'react-dom';

export default function Modal({ children, open, className = '' }) {
  const dialog = useRef();

  const modalClassName = className === null ? 'modal' : `modal ${className}`;

  useEffect(() => {
    const modal = dialog.current;
    if (open) {
      modal.showModal();
    }

    return () => {
      modal.close();
    };
  }, [open]);

  return createPortal(
    <dialog ref={dialog} className={modalClassName}>
      {children}
    </dialog>,
    document.getElementById('modal'),
  );
}
```

## Custom Hook을 통한 http 연결

- **HTTP** 연결을 하는 함수를 커스텀 훅을 통해 외부에 노출함으로써, 커스텀 훅을 사용하는 컴포넌트가 필요할 때만 **HTTP*** 연결을 요청할 수 있다.
- `useEffect`와 `useCallback`을 통해서 무한 루프 방지 하였다.

```javascript
import { useCallback, useEffect, useState } from 'react';

async function sendHttpRequest(url, config) {
  const response = await fetch(url, config);

  const resData = await response.json();

  if (!response.ok) {
    throw new Error(
      resData.message || 'Something went wrong, failed to send requset.',
    );
  }

  return resData;
}

export default function useHttp(url,config) {
  const [data, setData] = useState();
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState();

  const sendRequest = useCallback(async function sendRequest() {
    setIsLoading(true);
    try {
      const resData = sendHttpRequest(url,config);
      setData(resData);
    } catch (err) {
      setError(err.message || 'Something is wrong.');
    }
    setIsLoading(false);
  }, [url,config]);

  useEffect(() => {
    sendRequest();
  }, [sendRequest]);

  return {
    data,
    isLoading,
    error,
    sendRequest,
  };
}


```
