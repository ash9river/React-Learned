# Components

## JSX
- JSX는 타겟 UI/HTML 코드를 자바스크립트 코드 바로 안(컴포넌트 바로 안)에 정의할 수 있도록 한다.
- JSX 코드는 트리 모양의 코드 구조를 띄며 리액트에게 각 컴포넌트들이 어떻게 연관되어 있고 UI는 어떻게 보여야 하는지 알려준다.
- 그 다음, 올바른 명령어를 실행하여 실제 DOM을 제어하며 타겟 구조/코드를 반영한다.
- React는 리액트는 JSX 코드에서 사용한 컴포넌트로 웹사이트 DOM을 업데이트할 명령어를 수행하는 컴포넌트 트리를 불러온다. 

## Props

### 단일 Prop 객체 전달

- 자바스크립트 객체로 이미 구성된 데이터가 있다면, 그 객체를 여러 Prop들로 나누는 대신 하나의 Prop 값으로 전달할 수 있다.

```javascript
<CoreConcept
  title={CORE_CONCEPTS[0].title}
  description={CORE_CONCEPTS[0].description}  
  image={CORE_CONCEPTS[0].image}
/>
```
또는
```javascript
<CoreConcept
  {...CORE_CONCEPTS[0]}
/>
```
등등...

### 받은 Prop들을 단일 객체로 그룹화

- 여러 Prop을 컴포넌트에 전달한 다음, 컴포넌트 함수 내에서 자바스크립트의 `Rest Property`  문법을 사용하여 단일 객체로 그룹화할 수도 있다.

```javascript
<CoreConcept
  title={CORE_CONCEPTS[0].title}
  description={CORE_CONCEPTS[0].description}  
  image={CORE_CONCEPTS[0].image}
/>
```
```javascript
export default function CoreConcept({ ...concept }) { 
  // ...concept groups multiple values into a single object
  // Use concept.title, concept.description etc.
  // Or destructure the concept object: const { title, description, image } = concept;
}
```

### default Props

- 선택적 `prop`을 받을 수 있는 컴포넌트를 만들게 될 때가 있다.
- 자바스크립트는 객체 비구조화를 사용할 때 기본 값을 할당하여 해결.
- `type`에 기본값을 주는 것이 아니라, `type`가 주어지지 않았을 때, 기본값을 전달하는 것이 좀 더 보편적이다.

```javascript
export default function Button({ caption, type }) { 
  // caption has no default value, type has a default value of "submit"
  const buttonType = type || "submit";
}
```

## children props

- 컴포넌트 태그 사이의 텍스트
- `props.children`에 `Text of Text`가 출력된다.

```javascript
  <MyComponent >Text of Text</MyComponent >
```

```javascript
export default function MyComponent(props){
  return(
    <>
      {props.children}
    </>
  )
}
```



## 이벤트 처리하기

### props에 이벤트리스너 추가

- 이벤트 이후에 코드를 실행시키려면, `onClick`과 같은 이벤트 속성에 실행되어야 하는 함수의 포인터가 전달되어야 한다.
- 이벤트로부터 독립적인 함수는 함수를 다룬 함수(화살표 함수 등)로 감싼다.
- 그러면 다른 함수가 이벤트 핸들링의 속성 값으로 전달되고, 메인 함수는 이벤트가 발생될 때만 실행하게 된다.
- 예를 들어, 어떤 인자를 전달할지 정의할 때 주로 사용한다.

```javascript
export default function TabButton({ lable, onSelect }) {
  return (
    <>
      <li>
        <button onClick={onSelect}>{lable}</button>
      </li>
    </>
  );
}

```

## Hooks
  
- 컴포넌트 최상위 레벨에서 호출되어야 한다.

### useState

- 값, 값의 설정, 초기값
 
```javascript
const [dynamicMessage, setDynamicMessage] = useState("Please Click a button");
```

## 조건부 렌더링

- 조건부 렌더링에서는 조건이 `true`이면 해당 값을 반환하고, `false`이면 무시되기 때문에 어떤 것도 렌더링되지 않는다.
- 그러나 `falsy`값은 렌더링되기 때문에, 불필요한 `0`을 렌더링하지 않기 위하여 **항상 && 앞의 표현식이 언제나 진리값(`true`, `false`)이 되도록 만든다.**

### falsy 값

- `0`
- `-0`
- `0n` :	`BigInt`. 불리언으로 사용될 경우, 숫자와 같은 규칙을 따름. `0n`은 거짓 같은 값.
- `""` : 빈 `string`.
- `null` : 아무런 값이 없음.
- `undefined` : 빈 배열의 `element`에 접근하면 `undefined`다.
- `Nan` : 숫자가 아님.

## 동적 스타일링

- 동적으로 `class` 부여
  
```javascript
<button
  className={isSelected ? "active" : undefined}
  onClick={onSelect}
>
  {title}
</button>
```

## List 데이터 동적 렌더링

- key 속성 추가 필요
- 강의랑 다르게 내 입맛대로 재활용

```javascript
import { useState } from "react";
import { CORE_CONCEPTS, EXAMPLES } from "./data";
import Header from "./components/Header";
import CoreConcept from "./components/CoreConcept";
import TabButton from "./components/TapButton";

const App = () => {
  const [dynamicMessage, setDynamicMessage] = useState("components");

  const handleOnSelect = (selectedButton) => {
    setDynamicMessage(selectedButton);
  };

  return (
    <>
      <Header />
      <main>
        <section id="core-concepts">
          <h2>Core Concepts</h2>
          <ul>
            {CORE_CONCEPTS.map((item) => {
              return (
                <CoreConcept
                  key={item.title}
                  image={item.image}
                  title={item.title}
                  description={item.description}
                />
              );
            })}
          </ul>
        </section>
        <section id="examples">
          <h2>Examples</h2>
          <menu>
            {Object.keys(EXAMPLES).map((category) => {
              return (
                <TabButton
                  key={category}
                  title={EXAMPLES[category].title}
                  onSelect={() => handleOnSelect(category)}
                  isSelected={dynamicMessage === category ? true : false}
                />
              );
            })}
          </menu>
          <div className="tab">
            <h3>{EXAMPLES[dynamicMessage].title}</h3>
            <p>{EXAMPLES[dynamicMessage].description}</p>
            <pre>
              <code>{EXAMPLES[dynamicMessage].code}</code>
            </pre>
          </div>
        </section>
        <h2>Time to get started!</h2>
      </main>
    </>
  );
};

export default App;

```






