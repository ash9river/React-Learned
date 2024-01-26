# Optimizing React

## React dev Profiler

## Flamegraph chart

- 컴포넌트의 실행 순서와 관계를 알 수 있다.

![image](https://github.com/ash9river/React-Learned/assets/121378532/8b43058f-d59e-4232-b1a1-151da3d6f805)

### Ranked chart

- 리렌더링된 컴포넌트들만 볼 수 있고, 리랜더링 사이클에 필요한 컴포넌트들을 확인할 수 있다.

![image](https://github.com/ash9river/React-Learned/assets/121378532/57dfec7c-30c6-40ce-8b59-99d478bafa00)

## 자식 컴포넌트 리렌더링

- `App.jsx`의 `input` 태그의 값이 바뀔 때마다 모든 하위 컴포넌트들이 리렌더링되었다.

<img src="https://github.com/ash9river/React-Learned/assets/121378532/0882462d-1e31-4cc8-a2f4-a7b1429f3700" width="80%" height="80%"/>

```javascript
import { useState } from 'react';

import Counter from './components/Counter/Counter';
import Header from './components/Header';
import { log } from './log';

function App() {
  log('<App /> rendered');

  const [enteredNumber, setEnteredNumber] = useState(0);
  const [chosenCount, setChosenCount] = useState(0);

  function handleChange(event) {
    setEnteredNumber(+event.target.value);
  }

  function handleSetClick() {
    setChosenCount(enteredNumber);
    setEnteredNumber(0);
  }

  return (
    <>
      <Header />
      <main>
        <section id="configure-counter">
          <h2>Set Counter</h2>
          <input type="number" onChange={handleChange} value={enteredNumber} />
          <button onClick={handleSetClick}>Set</button>
        </section>
        <Counter initialCount={chosenCount} />
      </main>
    </>
  );
}

export default App;
```

## 해결법

### memo()로 컴포넌트 함수 실행 방지


- `memo()` 이용
  - 컴포넌트 함수를 memo로 감싸고, 그 결과를 변수나 상수를 통해 저장.
  - 보통 const를 이용해 따로 저장해두고 이름은 컴포넌트 함수 이름과 동일하게 지어준다.
  - 그리고 이 변수 혹은 상수를 내보낸다.
- `memo()`가 하는 일은 컴포넌트 함수의 `props`를 살펴본다.
- 그 후 컴포넌트 함수가 정상적으로 다시 실행될 때, `props`의 값이 이전 값과 동일하다면 컴포넌트 함수의 리렌더링을 방지한다. 
- 만약 컴포넌트 내부 상태가 바뀌면, `memo()`는 저지하지 않고 단순히 컴포넌트의 함수를 작동시킨다.

> `memo()`는 내부 상태(`state`) 변화로 인한 컴포넌트 리렌더링을 저지하지 않는다. <br/>
> 마찬가지로, 외부 상태(`props`) 변화로 인한 컴포넌트 리렌더링을 저지하지 않는다. <br/>
> 단순히, 외부 상태(`props`)가 변하지 않았는데, 상위 컴포넌트의 리렌더링이 발생한다면, `memo()`가 있는 컴포넌트의 리렌더링을 저지한다.

```javascript
import { useState, memo } from 'react';

const Counter = memo(function Counter({ initialCount }) {
  log('<Counter /> rendered', 1);
  // 로직

  return (
    <section className="counter">
      {/* 내용 */}
    </section>
  );
});

export default Counter;
```

- ❗ 그러나 모든 컴포넌트를 `memo()`로 감싸면 안된다.
- 최대한 상위 컴포넌트를 `memo()`로 감싸는 것은 가능하나, 모든 컴포넌트를 `memo()`로 감싸게 되면, **React**는 렌더링하기 전에 해당 컴포넌트들의 `props`를 항상 확인하게 되고, 그것이 성능의 저하로 이어진다.


### 컴포넌트 구조 설계

- `App.jsx`의 `input` 태그의 값이 변동할 때마다 하위 컴포넌트가 전부 리렌더링되었다.
- `App.jsx`의 `input` 태그를 `App.jsx`의 하위 컴포넌트로 분리하여, `input` 태그가 있는 컴포넌트만 리렌더링을 시킨다.

> 자식 컴포넌트의 재실행은 부모 컴포넌트의 재실행을 야기시키지 않는다.

#### 분리 전

![image](https://github.com/ash9river/React-Learned/assets/121378532/ec2a50c2-3e13-421c-a2f9-6e0e64c67342)


#### 분리 후

![image](https://github.com/ash9river/React-Learned/assets/121378532/f9829875-1a9b-419f-b168-137ab55847af)

## useCallback()

- 카운터의 값을 증가시켰을 때, `Counter.jsx` 컴포넌트의 상태가 변하여 리렌더링이 일어난다.
- 그런데 리렌더링을 하면 전달되는 함수 객체의 자체 값이 변하여 하위 컴포넌트 전체가 리렌더링을 하게 되었다.
- `props`가 변하지 않는 증가, 감소 버튼이 리렌더링이 일어날 필요가 없는데, 최적화가 안되어 있는 것이다.
- 그러므로, 함수 객체의 불변성을 지키면서 `useCallback`과 `memo`를 활용하여 리렌더링을 방지한다.

### 변경 전

<img src="https://github.com/ash9river/React-Learned/assets/121378532/c84c9150-bc09-4813-a1eb-8c4978ee24b1" width="80%" height="80%">

- 증가, 감소 버튼 로직

```html
<IconButton icon={MinusIcon} onClick={handleDecrement}>
  Decrement
</IconButton>
<CounterOutput value={counter} />
<IconButton icon={PlusIcon} onClick={handleIncrement}>
  Increment
</IconButton>
```


```javascript
import { log } from '../../log';

export default function IconButton({ children, icon, ...props }) {
  log('<IconButton /> rendered', 2);

  const Icon = icon;
  return (
    <button {...props} className="button">
      <Icon className="button-icon" />
      <span className="button-text">{children}</span>
    </button>
  );
}
```

### 변경 후

<img src="https://github.com/ash9river/React-Learned/assets/121378532/95356a11-791b-4739-9bc3-6219c3a12977" width="80%" height="80%">

- 상위 컴포넌트의 내용

```javascript
import { useState, useCallback } from 'react';

import IconButton from '../UI/IconButton';
import MinusIcon from '../UI/Icons/MinusIcon';
import PlusIcon from '../UI/Icons/PlusIcon';
import CounterOutput from './CounterOutput';
import { log } from '../../log';

function isPrime(number) {
  log('Calculating if is prime number', 2, 'other');
  if (number <= 1) {
    return false;
  }

  const limit = Math.sqrt(number);

  for (let i = 2; i <= limit; i += 1) {
    if (number % i === 0) {
      return false;
    }
  }

  return true;
}

function Counter({ initialCount }) {
  log('<Counter /> rendered', 1);
  const initialCountIsPrime = isPrime(initialCount);

  const [counter, setCounter] = useState(initialCount);

  const handleDecrement = useCallback(function handleDecrement() {
    setCounter((prevCounter) => prevCounter - 1);
  }, []);

  const handleIncrement = useCallback(function handleIncrement() {
    setCounter((prevCounter) => prevCounter + 1);
  }, []);

  return (
    <section className="counter">
      <p className="counter-info">
        The initial counter value was <strong>{initialCount}</strong>. It{' '}
        <strong>is {initialCountIsPrime ? 'a' : 'not a'}</strong> prime number.
      </p>
      <p>
        <IconButton icon={MinusIcon} onClick={handleDecrement}>
          Decrement
        </IconButton>
        <CounterOutput value={counter} />
        <IconButton icon={PlusIcon} onClick={handleIncrement}>
          Increment
        </IconButton>
      </p>
    </section>
  );
}

export default Counter;
```

- `memo`를 이용한 버튼 리렌더링 방지

```javascript
import { memo } from 'react';
import { log } from '../../log';

const IconButton = memo(function IconButton({ children, icon, ...props }) {
  log('<IconButton /> rendered', 2);

  const Icon = icon;
  return (
    <button {...props} className="button">
      <Icon className="button-icon" />
      <span className="button-text">{children}</span>
    </button>
  );
});

export default IconButton;
```

## useMemo()

- `useMemo()`는 컴포넌트에 있는 함수들을 감싸고 처음에 계산된 결과값을 메모리에 저장한다.
- 컴포넌트가 반복적으로 렌더링 되어도 이전에 이미 계산된 결과값을 메모리에서 꺼내와서 재사용 할 수 있게 하는 **메모이제이션** 기법이다.
- `useMemo()`는 두 개의 인자가 필요하다. 첫 번째는 콜백 함수, 두 번째는 의존성 배열이다.
  - 첫 번째 인자인 콜백 함수는 메모이제이션을 해줄 값을 계산해서 리턴해 주는 함수이다.
  - 두 번째 인자인 의존성 배열 안에 요소가 업데이트 될때만, `useMemo()`가 배열 안의 요소의 값이 업데이트될 때만 콜백 함수를 다시 호출하고, 메모이제이션 된 값을 업데이트한다.

> `useMemo()`도 `memo()`와 마찬가지로 추가적인 의존성 값 비교를 수행해야 하기 때문에 남용은 금물이다. <br/>
> 매 컴포넌트 함수가 실행될 때마다 재실행되어야 하는 함수가 있다면, `useMemo()`의 추가적인 확인 과정은 성능 낭비로만 이어진다. <br/>
> 그러나 지나친 실행을 피할 수 있고 어떤 코드를 실행할지에 따라 실행 시간이 길어지는 경우, `useMemo()`를 사용하는 것이 더 합리적이다.






ㅁ

