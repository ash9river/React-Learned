# CSS with React

## Vanilla CSS

### 장점

- jsx에서 css 코드 분리
- 내가 작업하고 있는 jsx 코드를 다른 사람이 스타일링할 수 있다.(충돌방지 가능)

### 단점

- 다른 컴포넌트 간에 스타일 충돌이 발생할 수 있다.
- 클래스명을 동적으로 부여할 때, 조건이 여러 개일 경우에는 삼항 연산자를 여러 번 중첩해야 하므로 가독성이 떨어질 수 있다.(삼항연산자 권장되는 협업시)

## InLine Style

- style 객체를 키와 값으로 전달하는 방식

```javascript
<p style={{color:"red"}} >My Style</p>
```

### 장점

- 추가하기 쉽다.
- 추가하는 요소에만 영향을 미치고 다른 요소에는 영향을 미치지 않는다.

### 단점

- 모든 요소를 개별적으로 스타일해야한다.
- `style` 속성에 직접 객체를 생성하는 방식은 일부 스타일을 동적으로 관리하기 어렵다.
  - 특히, 여러 스타일 속성이 상호작용할 때 복잡성이 증가한다. 
- 협업시 충돌이 일어나기 쉽다.

## 동적 스타일
<!--바닐라 하겐다즈 먹고싶다-->
### 바닐라

- className 동적 부여

```javascript
return(
  <>
    <p className={isValid?valid:inValid} >My Style</p>
    <p className={vanilla?yesVanilla:undefined} >My Style</p>
    <p className={`${dynamicValue}`} > MyStyle</p>
  </>
);
```


### 인라인 방식


```javascript
const isValid=conditionWhatYouWant;

return <p style={{color:isValid?"red":"blue"}} >My Style</p>;
```

## CSS in module

- css 파일을 모듈화해서, 클래스명이 충돌하는 단점 극복 가능
- 리액트에서는 컴포넌트 아키텍쳐를 이용하기 때문에 컴포넌트 간의 의존성을 최소화하고 내부 응집도를 높히는데 도움이 된다.

## Styled Component

### 장점

- jsx 코드 파일에 styled된 컴포넌트를 작성할 수 있다.
- 클래스명을 사용하지 않고 스타일을 줄 수 있기 때문에, 클래스명이 중복되어 생기는 문제들을 해결할 수 있다.
- 컴포넌트의 props를 참조할 수 있으며, props의 값에 따라 스타일을 다르게 코딩할 수 있다.

### 단점

- jsx 코드 파일 자체가 너무 길어져서 가독성 저하를 일으킨다.
- 런타임에 스타일을 생성하기 때문에 성능 이슈가 발생할 수 있다.
- css를 수정할 때 마다 해당 컴포넌트 파일 위치를 찾아야 한다.
- **서버 사이드 렌더링에 사용하면, 클라이언트 런타임 때, styled-component가 생성되고 주입되므로 깜빡임이 발생할 수 있다.**












