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
3. `setTimeout`, `setInterval` 등 예측할 수 없는 타이밍 함수 사용

## useEffect

- `useEffect`는 우리가 외부 세계와 상호 작용하면서 해당 컴포넌트의 렌더링이나 성능에는 영향을 미치지 않도록 만들어주는 도구


> 컴포넌트 안에서 직접 Side Effect를 수행하는 경우, 컴포넌트의 렌더링에 방해가 되기 때문에 Side Effect는 렌더링 과정과 분리되어야 한다. <br/>
> Side Effect를 수행해야 하는 경우, 컴포넌트의 렌더링이 끝난 후에 수행되어야 한다. <br/>
> 그러한 기능을 제공해주는 것이 useEffect이다.

- `useEffect`는 두가지 인수가 있다.
  1. 컴포넌트가 렌더링된 이후에 호출되는  `Side Effect` 함수
  2. `Side Effect`가 의존하는 모든 값을 포함하는 종속성 배열

```javascript
  useEffect(()=>{
    // 컴포넌트가 렌더링될 때 실행되는 함수
  },[])
```

- 만약 브라우저 API와 상호 작용할 때, `useEffect` 안에서의 작업이 비동기이면 `cleanup` 함수를 반환하여 정리(**clean-up**를 해줘야 한다.

```javascript
useEffect(() => {
  // 브라우저 API와 상호 작용
  const fetchData = async () => {
    try {
      const result = await somethingAboutBrowserAPI();
      // 데이터 처리 등
    } catch (error) {
      // 에러 처리
    }
  };

  fetchData();

  // cleanup 함수 (정리 작업)
  return () => {
    // 어떤 정리 작업을 진행할지 작성
  };
}, [의존성배열]);
```



- `useEffect`는 컴포넌트가 실행이 모두 완료된 이후, 첫 인수인 콜백함수를 실행한다.
  - JSX 코드가 반환(**return**)된 후의 시점에서야 `useEffect`에 전달해둔 `Side Effect` 함수를 실행시킨다.
- 의존성 배열을 정의해야 의존성 배열의 값이 변화했을 때, `useEffect` 함수를 재실행 시킬 수 있다.
- **만약 의존성 배열이 비어있으면, 컴포넌트가 처음 실행된 이후 단 한 번만 실행한다.**
  - 만약 의존성 배열이 없으면 무한 루프가 생성된다.

### 모든 Side Effects가 useEffect를 사용하지 않는 이유

- `useEffect`는 컴포넌트 함수가 실행되고 추가적인 실행이 진행되는 방식이다. 그러므로 불필요한 사용은 자제해야한다.


1. 렌더링을 위해 데이터를 변환하는 경우
2. 사용자 이벤트를 처리하는 경우




```javascript
navigator.geolocation.getCurrentPosition // navigator는 브라우저가 제공하는 객체
```
