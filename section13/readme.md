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

- ❗ 그러나 모든 컴포넌트를 `memo()`로 감싸면 안된다.
- 최대한 상위 컴포넌트를 `memo()`로 감싸는 것은 가능하나, 모든 컴포넌트를 `memo()`로 감싸게 되면, **React**는 해당 컴포넌트들의 `props`를 항상 확인하게 되고, 그것이 성능의 저하로 이어진다.


### 컴포넌트 구조 설계

- `App.jsx`의 `input` 태그의 값이 변동할 때마다 하위 컴포넌트가 전부 재실행이 되었다.
- `App.jsx`의 `input` 태그를 하위 컴포넌트로 분리하여, `input` 태그가 있는 컴포넌트만 재실행을 시킨다.

- 차이가 상당히 명확하다.

#### 분리 전

![image](https://github.com/ash9river/React-Learned/assets/121378532/ec2a50c2-3e13-421c-a2f9-6e0e64c67342)


#### 분리 후

![image](https://github.com/ash9river/React-Learned/assets/121378532/f9829875-1a9b-419f-b168-137ab55847af)

