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
