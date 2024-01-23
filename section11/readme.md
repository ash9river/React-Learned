# Side Effects & useEffect

## Side Effects

- `Side Effects` : 제대로 동작하기 위해서 꼭 필요하지만, 현재의 컴포넌트 렌더링에 직접적이고, 즉각적인 영향을 미치지는 않는 `tasks(일)`
- React에서는 컴포넌트가 화면에 렌더링된 이후에 비동기로 처리되어야 하는 부수적인 효과이다.

## React에서의 순수 함수(Pure Function) vs Side Effects

### 순수 함수

- 대부분의 React 컴포넌트는 순수함수이다. 
- 함수의 리턴 값이 동일한 인수(argument)에 대해 동일하다.
- `Side Effect`가 없다. (외부의 상태를 변경하지 않는다.)

### Side Effect

- 함수 내의 구현 내용이 함수 외부에 영향을 끼치는 경우, 해당 함수는 `Side Effect`가 있다고 일컫는다.
- `Side Effect`는 함수 외부와 함께 수행되기 때문에 예측할 수 없다.
- 무언가를 하기 위해 React 컴포넌트 외부에 도달해야 하는 경우 `Side Effect`를 수행한다.

#### 예시

1. 백엔드 서버에 API로 데이터 요청하기
2. 브라우저 API와 상호 작용 (document, window 직접 사용하기)
3. setTimeout, setInterval 등 예측할 수 없는 타이밍 함수 사용

## useEffect

- `useEffect`는 우리가 외부 세계와 상호 작용하면서 해당 컴포넌트의 렌더링이나 성능에는 영향을 미치지 않도록 만들어주는 도구

> 컴포넌트 안에서 직접 Side Effect를 수행하는 경우, 컴포넌트의 렌더링에 방해가 되기 때문에 Side Effect는 렌더링 과정과 분리되어야 한다. <br/>
> Side Effect를 수행해야 하는 경우, 컴포넌트의 렌더링이 끝난 후에 수행되어야 한다. <br/>
> 그러한 기능을 제공해주는 것이 useEffect이다.

- `useEffect`는 두가지 인수가 있다.
  1. 컴포넌트가 렌더링된 이후에 호출되는 콜백함수
  2. `Side Effect`가 의존하는 모든 값을 포함하는 종속성 배열










