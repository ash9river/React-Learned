# Optimizing React

## React dev Profiler

## Flamegraph chart

- 컴포넌트의 실행 순서와 관계를 알 수 있다.

![image](https://github.com/ash9river/React-Learned/assets/121378532/8b43058f-d59e-4232-b1a1-151da3d6f805)

### Ranked chart

- 리렌더링된 컴포넌트들만 볼 수 있고, 리랜더링 사이클에 필요한 컴포넌트들을 확인할 수 있다.

![image](https://github.com/ash9river/React-Learned/assets/121378532/57dfec7c-30c6-40ce-8b59-99d478bafa00)

## 자식 컴포넌트 리렌더링

- `App.jsx`의 `input` 태그의 값이 바뀔 때마다 모든 하위 컴포넌트들이 재실행되었다.

![image](https://github.com/ash9river/React-Learned/assets/121378532/0882462d-1e31-4cc8-a2f4-a7b1429f3700)


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
- 그 후 컴포넌트 함수가 정상적으로 다시 실행될 때, `props`의 값이 이전 값과 동일하다면 컴포넌트 함수의 실행을 방지한다. 
- 만약 컴포넌트 내부 상태가 바뀌면, `memo()`는 저지하지 않고 단순히 컴포넌트의 함수를 작동시킨다.

> `memo()`는 내부 상태(`state`) 변화로 인한 컴포넌트 재실행을 저지하지 않는다. <br/>
> 마찬가지로, 외부 상태(`props`) 변화로 인한 컴포넌트 재실행을 저지하지 않는다. <br/>
> 단순히, 외부 상태(`props`)가 변하지 않았는데, 상위 컴포넌트의 재실행이 발생한다면, `memo()`가 있는 컴포넌트의 재실행을 저지한다.

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










ㅁ
