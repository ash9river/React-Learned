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
