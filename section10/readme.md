# Context API & useReducer

## Props Drilling

- 컴포넌트 트리에서 데이터를 하위 컴포넌트로 전달하기 위해 중간 컴포넌트를 통해 property를 내려주는 것
- 중간 컴포넌트는 원하는 자식 컴포넌트에게 property를 전달하기 위해 필요하다.
- 그러나, 해당 값을 직접 사용하지 않는 경우에도 property를 받고 전달해야 한다.

### 장점

- 명시적인 값의 사용
- 값 추적이 편해진다.
- 코드 변경 파악이 편해진다.


### 단점

- property 데이터 형식 변경의 불편
- 중간 컴포넌트에 불필요한 property 전달
- 누락된 property 파악 어려움
- property 이름 변경 추적의 어려움

### 해결법

- Context API
- Redux 또는 다른 상태 관리 라이브러리
- Custom Hooks
- Render Props 패턴과 children props

## Context API

- children props을 이용한 컴포넌트 합성으로 해결할 수 있지만, 상위 컴포넌트가 비대해질 수 있으므로, Context API의 사용도 고려해봐야 한다.
- state와 연결이 쉽다.
- 관습적으로 Context API를 저장하는 폴더를 store라고 한다.

### Provider로 Context 이용가능하다.

- Provide로 Context 이용할 때, Context가 전송하는 value 데이터는 객체 형태이다.
- 객체에서 하나의 데이터만 변경되어도 Provider로 감싼 모든 자식 컴포넌트에서 리렌더링이 일어난다.

### 해결법

- Context 데이터를 가지고 있는 컴포넌트에 useMemo hook을 이용하여 데이터를 캐싱하여 불필요한 리렌더링 방지
- Context 쪼개서 여러 개를 사용한다.
- React.memo 고차함수 사용하여 자식 컴포넌트의 불필요한 리렌더링 방지한다.

- 나의 해결법은 useMemo hook을 이용했다.

```javascript
const [items, setItems] = useState(null);

  const valueCtx = useMemo(
    () => ({
      items: [],
    }),
    [items],
  );

return (
    <CartContext.Provider value={valueCtx}>
      <Header cart={shoppingCart} onUpdateCartItemQuantity={handleUpdateCartItemQuantity} />
      <Shop onAddItemToCart={handleAddItemToCart} />
    </CartContext.Provider>
  );
```

## Context 소비

- useContext로 이용한다.
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

```

## Context Consumer

- useContext Hook을 사용하지 않는 접근법
- 컨텍스트 값에 대한 액세스를 가진 JSX 코드를 묶음
- 그러나 복잡해서 보통 useContext를 더 선호한다.

```javascript
import { useContext } from 'react';

import { CartContext } from '../store/ShoppingCardContext';

export default function Cart({ onUpdateItemQuantity }) {
  // const { items } = useContext(CartContext);

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


ㅁ
