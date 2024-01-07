# Components

## JSX
- JSX는 타겟 UI/HTML 코드를 자바스크립트 코드 바로 안에 정의할 수 있도록 합니다 - 즉, 컴포넌트 바로 안이다.
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
  image={CORE_CONCEPTS[0].image} />
```
또는
```javascript
<CoreConcept
  {...CORE_CONCEPTS[0]} />
```
등등...

### 받은 Prop들을 단일 객체로 그룹화

- 여러 Prop을 컴포넌트에 전달한 다음, 컴포넌트 함수 내에서 자바스크립트의 `Rest Property`  문법을 사용하여 단일 객체로 그룹화할 수도 있다.

```javascript
<CoreConcept
  title={CORE_CONCEPTS[0].title}
  description={CORE_CONCEPTS[0].description}  
  image={CORE_CONCEPTS[0].image} />
```
```javascript
export default function CoreConcept({ ...concept }) { 
  // ...concept groups multiple values into a single object
  // Use concept.title, concept.description etc.
  // Or destructure the concept object: const { title, description, image } = concept;
}
```

### default Props

- 선택적 Prop을 받을 수 있는 컴포넌트를 만들게 될 때가 있다.
- 자바스크립트는 객체 비구조화를 사용할 때 기본 값을 할당하여 해결.

```javascript
export default function Button({ caption, type = "submit" }) { 
  // caption has no default value, type has a default value of "submit" 
}
```

## children props

- 컴포넌트 태그 사이의 텍스트

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

- props.children에 Text of Text가 출력된다.

## 이벤트 처리하기

- props에 이벤트리스너 추가

```javascript
export default function TabButton({ lable }) {
    const handleOnClick=()=>{
        console.log('Clicked');
    }
  return (
    <>
      <li>
        <button onClick={handleOnClick}>{lable}</button>
      </li>
    </>
  );
}

```

