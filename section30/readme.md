# Typescript

## 타입스크립트

- 타입스크립트는 자바스크립트의 `superset` 언어이다.
  - 자바스크립트를 기반으로 하나, 자바스크립트보다 더 확장된 프로그래밍 언어이다.
- 타입스크립트는 리액트와는 다르게 자바스크립트 라이브러리가 아니다.
  - 이로 하여금 자바스크립트의 기존 기능을 기반으로 새로운 기능을 만들거나, 기존 기능을 확장하지 않는다.
  - 대신에 자바스크립트의 주요 문법보다 확장된 문법을 가진다. 
- 타입스크립트는 `정적 타입(statically Typed)`의 특징을 갖는다.
  - 자바스크립트는 `동적 타입(dynamically Typed)` 언어임을 생각하면 대조적이다. 

### 원시 타입(Primitives Type)

- 자바스크립트의 기본형 데이터는 숫자형, 문자열, 불리언(boolean)형, `null`, `undefined`이 있다.

```javascript
let age: number;

age = 18;

let myAge: number = 26;

let userName: string = 'ash9river';

let isInstructor: boolean = true;
```

- `null`과 `undefinde`는 자기 자신의 값만 받을 수 있다.([설정 값에 따라 아닐 수도 있음](https://yamoo9.gitbook.io/typescript/types/null-undefined))

```javascript
let isNull: null = null;

let isUndefined: undefined = undefined;
```

### 배열 및 객체 타입

- 배열에는 단순히 대괄호`[]`를 추가.
- 객체는 지정을 안할 시에는 원시 타입으로 `any`가 지정되나, 자바스크립트랑 다를 바가 없어서 좋지 않은 방법이다.
- 또한, 객체에 타입 지정을 하지 않은 필드는 저장할 수 없다.

```javascript
// arrays

let hobbies: string[];

hobbies = ['Sports', 'Cooking', 'Games'];

// bad

let person;

person = {
  name: 'ash9river',
  age: 26,
};

// good

let man: {
  name: string;
  age: number;
};

man = {
  name: 'ash9river',
  age: 26,
};

// object array

let people: {
  name: string;
  age: number;
}[];
``` 

### 타입 추론(Type Inference)

- 타입스크립트는 가능한 많은 타입을 유추할려고 한다.
- 명시적인 타입 표기가 없어도 타입 추론을 통해 타입을 지정한다.
- 타입 추론을 이용해서 코드를 작성하는게 권장된다.(형식 지정에 따른 불필요한 코드를 줄이기 위함)

```javascript
let course = 'React - The Complete Guide';

course = 1234; // error
```

### 유니온 타입(Union Type)

- 하나의 변수에 두 개 이상의 타입을 지정할 수 있게 해주는 기능으로, 유니온 타입 기능이라 한다.

```javascript
let course: string | number = 'React - The Complete Guide';

course = 1234;
```

### 타입 별칭(Type Aliases)

- 동일한 코드 중복을 피하기 위해서 기본 타입을 만들어서 복잡한 타입을 정의하고, 그 타입 별칭을 사용함으로써, 반복해서 타입을 정의하는 것을 피할 수 있다.
- `type` 키워드를 이용하여 타입을 만들 수 있다.

```javascript
type Person = {
  name: string;
  age: number;
};

let personWithType: Person = {
  name: 'ash9river',
  age: 26,
};

let peopleWithType: Person[];
```

### 함수 및 함수 타입

- 함수를 사용할 때, 타입을 지정하는 위치가 따로 있다.

<img height="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/22408962-e38b-43e1-8669-84de0fa82170" />

- 이 이미지에서는 타입 추론을 통해 함수에 타입이 지정되었다.

```javascript
// Function with Type Inference
function add(a: number, b: number) {
  return a + b;
}

// Function without Type Inference
function add(a: number, b: number): number {
  return a + b;
}
```

- 타입스크립트가 타입을 추론하기 때문에 함수에 명시적으로 타입을 지정할 필요는 없다.
- 그렇지만 함수에서 타입을 사용할 때, 매개변수의 타입뿐만이 아니라 반환값의 타입도 생각은 하는 것이 필요하다.

- 만약 반환값이 없는 함수가 있다면, 그 함수는 `void`를 타입으로 갖는다. 
  - 이 함수의 반환 값을 받아서 작업하려면 `undefined` 타입으로 값을 받아야 한다.
- `void`는 함수에만 있는 특수한 타입으로 반환값이 없는 함수의 반환 타입으로 사용된다.

### 제네릭(Generic)

- 제네릭은 타입스크립트에서 함수, 클래스, 인터페이스 등을 정의할 때 타입을 파라미터화하는 기능이다.
- 이를 통해 함수나 클래스를 사용할 때 원하는 타입을 동적으로 지정할 수 있다. 
- 다음과 같은 코드가 있다고 생각해보자.

```javascript
function insertAtBeginning(array: any[], value: any) {
  const newArray = [value, ...array];

  return newArray;
}

const demoArray = [1, 2, 3];

const updatedArray = insertAtBeginning(demoArray, -1);
```

- 이 코드의 문제점은 `updatedArray`에 추론된 배열의 타입이 `any`라는 것이다.
  - 타입스크립트에서는 이 배열에 `number`만 들어있다는 것을 인식하지 못하였기 때문에, 타입스크립트는 이 배열을 제대로 지원할 수 없다.
    - 함수의 반환형이 `any`이어서 어떤 타입이든 받을 수 있지만, 실제로 함수가 반환할 때 어떤 타입인지에 대한 정보는 잃게 된다. 

<img heigth="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/0bc9b410-a9fd-458b-8155-e66b338b2ea2" />

- 제네릭 타입을 정의함으로써, 해결할 수 있다.
- 함수의 이름과 매개변수 사이에 `<>`를 추가하고, 식별자로 `Type`의 `T`를 따서 사용한다.(식별자는 다르게 지정해도 된다.)
   - 이를 `generic type placeholder`라고 한다.

```javascript
function insertAtBeginning<T>(array: T[], value: T) {
  const newArray = [value, ...array];

  return newArray;
}

const demoArray = [1, 2, 3];

const updatedArray = insertAtBeginning(demoArray, -1);
```

<img height="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/c6d668d7-9e2b-4a77-8bbc-76bdf152b22e" />

<img height="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/daf544f7-f834-41b9-b7d7-5a6ddbc57fdd" />

- 결과적으로 제네릭 타입을 통해 `any` 타입이 아니라 `number[]` 타입임을 제대로 추론할 수 있게 된다.
- 자유롭게 어떤 타입이든 사용할 수 있지만, 특정 타입을 이용해 해당 함수를 실행하면, 그 해당 특정 타입으로 고정되어서 동작한다.

<img height="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/f4e3838f-6ad5-49bd-a902-4491ce4a2e7b" />

> ❗ 함수 작성을 할 때, 제네릭 타입을 사용하면 유연성과 타입 안정성을 높여준다. 

#### 제네릭 자세한 설명

- 다음과 같은 코드가 있다고 생각해보자.

```javascript
let thisIsNumbers: number[] = [1, 2, 3];
```

- `thisIsNumbers`의 타입은 `number[]`이다.
- `number[]`은 타입스크립트의 표기법으로써 숫자의 배열이라 정의된다.
- 그러나, 이 `nubmer[]` 표기법은 `Syntax Sugar`이다.
  - `Syntax Sugar`란 여기서 코드를 읽는 사람 또는 작성하는 사람을 위해 편하게 디자인 된 문법이라는 뜻이다. 
- 이 `number[]`의 실제 타입은 `Array`이다.
  - 모든 배열은 `Array` 타입이다. 
- 그러므도 상기 코드도 하단의 방식으로 작성할 수 있다.

```javascript
let thisIsNumbers: Array<number> = [1, 2, 3];
```

- 이 코드를 통해, `generic type placeholder`에서 `<T>`가 작성자가 스스로 자신만의 타입을 만드는 것이 아니라 타입스크립트에게 실제 타입이 해당 유형을 대표한다는 것을 알 수 있다.

## 컴포넌트 with Typescript

- 리액트에서 `props`는 언제나 객체 형태이다.
- 그런데, 타입스크립트에서 `props`에 대해 정의를 할려면 객체 쌍만 정의를 해야 하는게 아니라, `children`까지 타입 정의를 해야 한다.
- 이는 번거롭고 비생산적인 결과를 초래한다.

```javascript
function Todos(props: { items: string[]; children: any }) {
  return (
    <ul>
      <li>Learn React</li>
      <li>Learn Typescript</li>
    </ul>
  );
}
```

- 리액트와 타입스크립트는 이에 대한 해결책으로 제네릭 타입을 지원한다.
  - 함수형 컴포넌트를 바로 제네릭 함수로 변환해서 이용하는 방식이다. 
- 함수형 컴포넌트에서 설정을 추가하여, 리액트 함수형 컴포넌트로 동작하도록 만들어서 `children`과 같은 기본 `props`를 사용할 수 있도록 만든다.
- 그 다음, 새로운 `props`를 추가로 정의한다.
  - 이 때, 정의되는 새로운 속성은 `props` 객체에 합쳐져야 한다.  
- `React.FC`를 타입으로 지정하는 방법이다.([권장되지 않는다.](https://github.com/facebook/create-react-app/pull/8177)
  - [`React v18`부터는 `children` 속성이 사라져서 선택의 폭이 넓어졌다.](https://velog.io/@doeunnkimm_/React%EC%97%90%EC%84%9C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC-%EC%9C%84%ED%95%B4-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%ED%83%80%EC%9E%85) 
- `props` 에 대한 타입을 선언 할 때에는 `interface` 또는 `type` 을 사용하면 되고, 프로젝트 내부에서 일관성만 지키면 된다.

```javascript
const Todos: React.FC = () => {
  return (
    <ul>
      <li>Learn React</li>
      <li>Learn Typescript</li>
    </ul>
  );
};

export default Todos;
```

### 다양한 props 전달 방식

1. `함수 시그니처 직접 지정`

```javascript
import { ReactElement } from 'react';

function Todos(props: { items: string[]; children: ReactElement }) {
  const { items, children } = props;
  return <ul>{children}</ul>;
}

export default Todos;
```

2. `Type Aliases`

```javascript
import { ReactElement } from 'react';

type TodosProps = {
  items: string[];
  children: ReactElement;
};

function Todos(props: TodosProps) {
  const { items, children } = props;
  return <ul>{children}</ul>;
}

export default Todos;
```

3. `Interface`

```javascript
import { ReactElement } from 'react';

interface TodosProps {
  items: string[];
  children: ReactElement;
}

function Todos(props: TodosProps) {
  const { items, children } = props;
  return <ul>{children}</ul>;
}

export default Todos;
```

- `interface`와 `type` 중 어떤 것을 사용할지는 개발자의 취향이나 프로젝트의 요구에 따라 다를 수 있다.
- 일반적으로 `type`이 `union` 타입, `intersection` 타입 등을 더 쉽게 정의할 수 있다.
- 반면에, `interface`는 확장이 가능하며, 클래스나 객체의 구조를 정의하는 데에 더 많이 사용된다.

## form with typescript

- `type`가 `submit`이면 `button`의 기존 타입을 생략해도 됐던 기존과는 다르게, 명시적으로 나타내야 한다.
- 또한 `event`를 다룰 때에는, `event`가 무엇인지 지정해야 하는데, 여기선 `React.FormEvent`라 지정하였다.
  - 이를 통해 `event.preventDefault()`가 자동완성이 뜬다.  

```javascript

function NewTodo() {

  function submitHandler(event: React.FormEvent) {
    event.preventDefault();
  }

  return (
    <form onSubmit={submitHandler}>
      <label htmlFor="text">
        Todo text
        <input type="text" id="text" />
      </label>
      <button type="submit">Add Todo</button>
    </form>
  );
}

export default NewTodo;
```

### useRef with typescript

- 타입스크립트에서는 `useRef`를 사용할 때, `ref`에 저장될 데이터가 어떤 타입인지 정확히 밝혀야 한다.
  - 이를 위해 제네릭 타입을 사용한다.
- 이는 `useRef` 자체가 제네릭 타입으로 정의되어 있는 이유이다.
- `useRef`는 `ref`에 저장되는 모든 종류의 데이터에 사용될 수 있고, `ref`와 연결할 수 있는 모든 **HTML** 요소에 사용될 수 있다.
- 그런데 폼 제출 시에는 `input` 요소를 저장할 것이기 때문에 `HTMLInputElement` 타입을 가진다.
  - 참고로, `button` 요소의 타입은 `HTMLButtonElement`, `p` 요소의 타입은 `HTMLParagraphEkenebt`, 이런 식이다. 
- [그리고, 참조에 기본값을 직접 설정해야 한다.](https://velog.io/@rkio/Typescript-useRef%EC%99%80-Typescript%EB%A5%BC-%EA%B0%99%EC%9D%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EA%B2%BD%EC%9A%B0)

```javascript
const enteredText = todoTextInputRef.current?.value;
```
- `current`의 `value`에 접근하는데 자동완성으로 `?`가 작성되고, 이를 제거하면 오류가 나온다.
- 이는 타입스크립트가 `ref`에 값이 설정되어있지 않을 수도 있어서 그런 것인데, 폼 제출시 값이 무조건 있기 때문에 `null`이 될 수 없다.
- 따라서, `?` 대신에 `!`을 사용해서, 이 값이 `null`이 될 수는 있지만, 이 시점에서는 절대 `null`이 될 수 없다고 알린다.
  - `!`는 절대 `null`이 아닐 경우에만 사용한다. 

```javascript
const enteredText = todoTextInputRef.current!.value;
```

## props로 함수 전달

- `props`로 함수를 전달하려면 화살표 함수를 사용하면 간단하다.
- 단순히 함수명에 마우스를 `hover`하면 안내해준다.

<img width="50%" height="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/6404b249-b6b5-4515-9d5d-d39a9404cdee" />

```javascript
// 상위 컴포넌트
  function onAddTodo(todoText: string) {}

  return (
    <>
      <NewTodo onAddTodo={(text) => onAddTodo(text)} />
      <Todos items={todo} />
    </>
  );
```
```javascript
// 하위 컴포넌트
import { useRef } from 'react';

type NewTodoProps = {
  onAddTodo: (text: string) => void;
};

function NewTodo({ onAddTodo }: NewTodoProps) {
  const todoTextInputRef = useRef<HTMLInputElement>(null);

  function submitHandler(event: React.FormEvent) {
    event.preventDefault();

    const enteredText = todoTextInputRef.current!.value;

    if (enteredText.trim().length === 0) {
      // throw an error
    }

    onAddTodo(enteredText);
  }

  return (
    <form onSubmit={submitHandler}>
      <label htmlFor="text">
        Todo text
        <input type="text" id="text" ref={todoTextInputRef} />
      </label>
      <button type="submit">Add Todo</button>
    </form>
  );
}

export default NewTodo;
```

## state with typescript

- `useState`를 사용할 때, 기본값에 빈 배열을 전달하면, `never[]`가 타입으로 지정된다.
  - 언제나 배열이 비어있어야 한다는 뜻이다.

<img height="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/d2a9a493-d056-407e-9495-064e008632e5" />

- 그러나 이것은 원하는 동작이 아니기 때문에, 타입을 제네릭을 통해 지정한다. 

```javascript
const [todo, setTodo] = useState<todos[]>(todoInit);
```

- `state`를 업데이트할 때는, `concat()`을 이용한다.

```javascript
import NewTodo from 'components/NewTodo';
import Todos from 'components/Todo';
import { todos } from 'models/todo';
import { useState } from 'react';

const todoInit: todos[] = [
  {
    id: 1,
    text: 'Learn React',
  },
  {
    id: 2,
    text: 'Learn Typescript',
  },
];

let counter: number = 3;

function App() {
  const [todo, setTodo] = useState<todos[]>(todoInit);

  function onAddTodo(todoText: string) {
    const newTodo: todos = {
      id: counter,
      text: todoText,
    };
    counter += 1;

    setTodo((prevTodos) => {
      return prevTodos.concat(newTodo);
    });
  }

  return (
    <>
      <NewTodo onAddTodo={(text) => onAddTodo(text)} />
      <Todos items={todo} />
    </>
  );
}

export default App;
```

### typescript 연습

- `props drilling`이 발생했지만, 어차피 간소한 연습 프로젝트라서 별로 신경을 안썼다.

<details>
  <summary>코드 보기</summary>

```javascript
import NewTodo from 'components/NewTodo';
import Todos from 'components/Todo';
import { todos } from 'models/todo';
import { useState } from 'react';

const todoInit: todos[] = [
  {
    id: 1,
    text: 'Learn React',
  },
  {
    id: 2,
    text: 'Learn Typescript',
  },
];

let counter: number = 3;

function App() {
  const [todo, setTodo] = useState<todos[]>(todoInit);

  function onAddTodo(todoText: string) {
    const newTodo: todos = {
      id: counter,
      text: todoText,
    };
    counter += 1;

    setTodo((prevTodos) => {
      return prevTodos.concat(newTodo);
    });
  }

  function onRemoveTodo(todoId: number) {
    console.log(todoId);

    setTodo((prevTodos) => {
      // 제거할 항목을 제외한 새로운 배열을 생성하여 반환합니다.
      return prevTodos.filter((item) => item.id !== todoId);
    });
  }

  return (
    <>
      <NewTodo onAddTodo={(text) => onAddTodo(text)} />
      <Todos items={todo} onRemove={(id) => onRemoveTodo(id)} />
    </>
  );
}

export default App;
```

```javascript
import { todos } from 'models/todo';
import TodoItem from './TodoItem';
import classes from './Todo.module.css';

type TodosProps = {
  items: todos[];
  onRemove: (id: number) => void;
};

function Todos({ items, onRemove }: TodosProps) {
  return (
    <ul className={classes.todos}>
      {items.map((item) => (
        <TodoItem
          text={item.text}
          id={item.id}
          onRemove={(id) => onRemove(id)}
          key={item.id}
        />
      ))}
    </ul>
  );
}

export default Todos;
```

```javascript
import classes from './TodoItem.module.css';

type TodoProps = {
  text: string;
  id: number;
  onRemove: (id: number) => void;
};

function TodoItem({ text, id, onRemove }: TodoProps) {
  return (
    <li
      className={classes.item}
      onClick={() => onRemove(id)}
      aria-hidden="true"
    >
      {text}
    </li>
  );
}

export default TodoItem;
```
</details>

## Context API with typescript

### createContext

- `createContext`는 제네릭 타입으로 정의되어 있기 때문에 `<>`를 통해 객체를 좀 더 자세히 정의한다.
- `<>` 사이에 타입의 정의를 내려야 한다.
  - 인터페이스나 타입도 가능하다.
 
```javascript
import React from 'react';

import { todos } from 'models/todo';

const TodosContext = React.createContext<{
  items: todos[];
  addTodo: () => void;
  removeTodo: (id: number) => void;
}>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: number) => {},
});
```
- 또는

```javascript
import React from 'react';

import { todos } from 'models/todo';

type ContextType = {
  items: todos[];
  addTodo: () => void;
  removeTodo: (id: number) => void;
};

const TodosContext = React.createContext<ContextType>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: number) => {},
});
```

```javascript
import React from 'react';

import { todos } from 'models/todo';

interface ContextType {
  items: todos[];
  addTodo: () => void;
  removeTodo: (id: number) => void;
}

const TodosContext = React.createContext<ContextType>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: number) => {},
});
```

- 결국 이런 방식으로 하면 된다.

```javascript
import React, { useCallback, useMemo, useState } from 'react';

import { todos } from 'models/todo';

type ContextType = {
  items: todos[];
  addTodo: (text: string) => void;
  removeTodo: (id: number) => void;
};

export const TodosContext = React.createContext<ContextType>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: number) => {},
});

let counter: number = 3;

function TodosContextProvider({ children }: { children: React.ReactNode }) {
  const [todo, setTodo] = useState<todos[]>([]);

  function onAddTodo(todoText: string) {
    const newTodo: todos = {
      id: counter,
      text: todoText,
    };
    counter += 1;

    setTodo((prevTodos) => {
      return prevTodos.concat(newTodo);
    });
  }

  function onRemoveTodo(todoId: number) {
    setTodo((prevTodos) => {
      return prevTodos.filter((item) => item.id !== todoId);
    });
  }

  const contextValue: ContextType = useMemo(
    () => ({
      items: todo,
      addTodo: onAddTodo,
      removeTodo: onRemoveTodo,
    }),
    [todo],
  );

  return (
    <TodosContext.Provider value={contextValue}>
      {children}
    </TodosContext.Provider>
  );
}

export default TodosContextProvider;

```

- `Context`를 소비할 때에는, 타입 추론이 자동으로 되어서 타입을 추가할 필요가 없다.

```javascript
import { useContext } from 'react';

import { TodosContext } from 'store/todos-context';
import TodoItem from './TodoItem';
import classes from './Todo.module.css';

function Todos() {
  const todosCtx = useContext(TodosContext);

  return (
    <ul className={classes.todos}>
      {todosCtx.items.map((item) => (
        <TodoItem
          text={item.text}
          id={item.id}
          onRemove={(id) => todosCtx.removeTodo(id)}
          key={item.id}
        />
      ))}
    </ul>
  );
}

export default Todos;
```
