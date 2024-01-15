# Refs & Portals

## Refs

> 어떻게 Refs를 사용하여 상태를 간소화할 수 있나?
- `React`에서 `Refs`는 `State`와 같이 값이다.
- `useRef` 훅을 불러옴으로써 사용가능하다.
- 다른 훅 함수들과 마찬가지로 컴포넌트 함수나 커스텀 훅 내에서만 호출할 수 있다.
- `useRef`는 바로 참조로 JSX 요소들과 연결될 수 있다.
  
```html
<input
  ref={playerName}
  type="text"
  onChange={handleChange}
  value={enteredPlayerName}
/>
```

- ref 속성은 모든 리액트 컴포넌트들에서 사용이 가능하다.
- useRef를 통해 생성된 참조 값들을 위해 우선은 current 속성에 접근해야 한다.
- useRef로부터 받는 이 참조 값들은 항상 자바스크립트 객체이며, 항상 current 속성을 가지고 있다.

> useRef 는 .current 프로퍼티로 전달된 인자(initialValue)로 초기화된 변경 가능한 ref 객체를 반환합니다. 반환된 객체는 컴포넌트의 전 생애주기를 통해 유지될 것입니다.
> > React 공식 홈페이지

- 💡 `useRef`는 저장공간 또는 DOM 요소에 접근하기 위해 사용하는 React Hook이다.
  - 변수를 관리하거나 특정 DOM을 선택할 때 사용한다. 

### Ref(참조)로 HTML 요소 연결 및 접근

- `input` 태그에 `ref`를 접목하여, 좀 더 효율적인 렌더링을 이끌어냈다.

```javascript
import { useState, useRef } from "react";

export default function Player() {
  const playerName = useRef();

  const [enteredPlayerName, setEnteredPlayerName] = useState("");

  function handleClick() {
    setEnteredPlayerName(playerName.current.value);
  }

  return (
    <section id="player">
      <h2>
        Welcome{" "}
        {enteredPlayerName.length > 0 ? enteredPlayerName : "unknown entity"}
      </h2>
      <p>
        <input ref={playerName} type="text" />
        <button onClick={handleClick}>Set Name</button>
      </p>
    </section>
  );
}
```

> ❗ 참조를 사용하는 목적이 페이지의 모든 종류의 값들을 읽고 조정하기 위해서면 안된다. <br/>
> DOM 상호작용은 리액트가 해야 하기 떄문이다.

## Ref vs State

- `useRef`의 주요 특징은 컴포넌트의 렌더링과 관련 없이 값이 유지되는 것이다.
  
### Ref.current를 직접적으로 출력해서는 안되는 이유 

- 처음 렌더링 될 때, `useRef`로 생성된 객체의 `current` 속성은 초기값이 `undefined`다.
- 다음 렌더링 사이클에서 `useRef`로 생성된 객체와 현재 값이 유지되지만, 이 값을 통해 **DOM** 요소와의 연결은 연결이 끊기게 된다.
- 따라서, **DOM**과의 연결이 끊겼을 때, `useRef`의 `cuurent` 속성만을 통해 값에 접근 가능해진다.





