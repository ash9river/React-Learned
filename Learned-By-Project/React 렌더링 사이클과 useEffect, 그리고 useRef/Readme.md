# ref와 useEffect

useEffect는 굉장히 섬세하다. 웬만해서는 사용해서는 안된다.

그런데, 이전에 구글 맵을 렌더링할 때, useEffect와 ref를 같이 사용함으로써, 예기치 못한 부작용들이 발생할 수 있었다.

이를 위해서 react의 trigger phase, render phase와 commit phase를 파고들어 보자.

---

# TLDR

리액트 공식문서에서는 렌더링 과정을 다음과 같이 얘기한다.

![](https://velog.velcdn.com/images/pikadev1771/post/93207b6f-8c22-4b45-ae4f-862f01347def/image.png)

1.  렌더링 트리거 (손님의 주문을 주방으로 전달)
2.  컴포넌트 렌더링 (주방에서 주문 준비하기)
3.  DOM에 커밋 (테이블에 주문한 요리 내놓기)

## 렌더링 트리거

렌더링 트리거란, 단순히 렌더링을 트리거하는 것이다.

렌더링 트리거가 왜 일어나는지에는 다음의 두 가지 이유가 있다.

### Initial render

-   어떠한 동작(페이지 방문 등)으로 인해 컴포넌트가 처음으로 렌더링되는 경우

사용자가 처음 웹을 방문하면, 리소스를 서버에 요청하고, 앱이 실행된다. 이때, 엔트리 파일에서 ReactDOM의 render()를 호출하고, root 컴포넌트를 화면에 렌더링한다.

```
ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <App />
);
```

### Re-render

-   컴포넌트의 state가 업데이트된 경우

컴포넌트의 상태 업데이트 함수를 호출하면, 자동으로 렌더링 대기열(state 업데이트 큐)에 추가된다.

한 번의 렌더링 사이클에서 하나의 상태 업데이트만을 처리하는 것은 상대적으로 많은, 연산과 리소스를 필요로 하기 때문에, 한 번의 DOM 업데이트를 통해서 처리하기 위해서, 렌더링 대기열을 이용한다.

![](https://velog.velcdn.com/images/pikadev1771/post/93207b6f-8c22-4b45-ae4f-862f01347def/image.png)

> 렌더링 트리거는, 초기 렌더링 또는 리렌더링을 알려주는 동작을 한다.

## 컴포넌트 렌더링(render phase)

render phase란, 간단히 말해서 컴포넌트를 실행하고, 그 함수의 결과값을 이전 virtual DOM node와 비교해서, 다른 점이 있으면 다시 렌더링하라고 체크하는 과정이다.

React는 render phase에서 다음과 같은 동작을 한다.

-   Initial render에서는 루프 컴포넌트를 호출한다.
-   Re-render에서는 state 업데이트가 일어나 렌더링을 트리거한 컴포넌트를 호출한다.
-   만약 컴포넌트가 다른 컴포넌트를 반환하면, React는 다음으로 해당 컴포넌트를 렌더링한다. 그리고 그 해당 컴포넌트도 다른 컴포넌트를 반환하면, 반환된 그 다른 컴포넌트를 다음에 렌더링하는 방식이다.  
    중첩된 컴포넌트가 더 이상 없으면서도, 동시에 React가 화면에 표시되어야 하는 내용을 정확히 알 때까지, 재귀적으로 동작된다.

재귀동작 공식문서 영어 원문

This process is recursive: if the updated component returns some other component, React will render that component next, and if that component also returns something, it will render that component next, and so on. The process will continue until there are no more nested components and React knows exactly what should be displayed on screen.

-   Initial render에서는 React는 DOM 노드를 생성한다.
-   Re-render에서 React는 이전 렌더링과 비교해서 변경된 속성을 계산한다. commit phase 전까지는 그 정보 아무런 것도 계산하지 않는다.

### 재조정(Reconciliation)

이전에 생성한 가상 DOM 트리와 새로 만든 가상 DOM 트리를 비교하고, 실제 DOM에 반영할 변경사항들을 파악한다.

Reconciliation의 알고리즘인 fiber를 통해,이전 가상 DOM 트리와 새로 만든 DOM 트리의 변경사항을 비교한 다음 변경된 부분만 실제 DOM에 반영한다.

#### fiber

fiber는 자체 가상 스택을 사용하는 작업단위로, fiber는 재귀 대신 연결리스트를 사용한다.  
작업이 끝나면 그 다음 작업을 링크타고가서 진행하는 방식이다.

React element를 fiber node에 일대일 매칭시킨다고 생각하는 편이 낫다.

[fiber에 더 알고 싶다면](https://blog.naver.com/dlaxodud2388/223195103660)

## DOM에 커밋(commit phase)

React는 컴포넌트를 호출한 이후, DOM을 수정한다.

### Initial render

-   Initial render의 경우, React는 DOM API의 appendChild()를 사용하여 생성한 모든 DOM 노드를 화면에 표시한다.

root에서 App, 그리고 App의 자식 컴포넌트... 이런 방식으로, appendChild를 통해 모든 DOM 노드를 화면에 표시한다.

```
ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <App />
);
```

### Re-render

-   Re-render의 경우, React는 렌더링하는 동안 계산된, 최소한으로 필요한 작업을 적용하여 DOM이 최신 렌더링 출력과 일치하게 만든다.

React가 렌더링 간의 차이가 있는 경우에만 DOM 노드를 변경한다.

재조정을 통해 확인된 변경사항들을 DOM에 반영한다.

## 브라우저 페인팅(Browser paint)

렌더링이 끝나고 React가 DOM을 업데이트한 이후에, 브라우저는 화면을 다시 그린다(repaint). 이를 **브라우저 렌더링**이라 한다.

하지만, 이 브라우저 렌더링이라는 단어는 혼동을 줄 수 있기 때문에, **브라우저 페인팅**이라 칭한다.

---

## 렌더링 과정에서의 useEffect

### render phase

render phase에서 React는 useEffect가 있는지 확인하고, useEffect의 호출을 기억한다. 여기서 useEffect의 첫 번째 인자인 콜백 함수와, 두 번째 인자인 의존성 배열이 저장된다.

useEffect는 render phase에서 동작하지 않고, commit phase에서 실행할 준비를 한다.(useEffect가 side effect를 실행하기 때문이다.)

### commit phase

실제 DOM이 업데이트 된 후(렌더링 이후), useEffect는 React에 의해 실행된다. commit phase(componentDidMount)에서 render phase에서 기억한 모든 useEffect를 모아서 실행한다.

commit phase는 이하의 순서로 진행된다.

#### cleanup phase

이전 렌더링에서 useEffect가 반환한 cleanup 함수가 있다면, 이 함수를 실행해서 이전 useEffect를 정리한다.

이는 컴포넌트의 상태 변경이나, 컴포넌트 언마운트에서 발생하는 **side effect**를 방지하기 위한 단계이다.

#### effect execute phase

이전 렌더링의 의존성 배열의 값과 현재 렌더링의 의존성 배열의 값를 비교하여, 변화가 있으면, useEffect에 전달된 콜백 함수를 실행한다.

이 콜백함수는 새로운 **side effect**를 수행하며, cleanup 함수도 반환할 수 있다.

## 빈 의존성 배열의 경우

useEffect가 빈 의존성 배열인 경우, 컴포넌트가 한 번 렌더링되고, useEffect의 콜백 함수를 한 번 더 실행한다.(cleanup 함수는 따로 저장했다가 cleanup phase에서 실행된다.)

웬만해선 쓰지 말라는 이유가 이런 이유 때문이다. 나도 모르는 사이에 컴포넌트가 두 번 렌더링된 것이다...

## useRef

> useRef는 컴포넌트 life-cycle동안 re-render을 트리거하지 않고, 값을 참조할 때 사용하는 hook이다.

useRef는 특히 DOM을 조작할 때, 사용된다.

이 때, ref를 변경하는 것은 Re-render을 유발시키지 않으므로, 바뀌는 값에 따라 렌더링을 하고자 하는 경우, 적합하지 않다.

### useRef가 보장하는 세 가지

> You can store information between re-renders (unlike regular variables, which reset on every render).  
> 렌더링 될 때 마다 바뀌는 값(state)이 아니어서 중간에 Re-render가 일어나도 값을 일관되게 저장할 수 있다.

> Changing it does not trigger a re-render (unlike state variables, which trigger a re-render)  
> Re-render을 트리거하는 state와 달리 re-rendering을 트리거하지 않는다.

> The information is local to each copy of your component (unlike the variables outside, which are shared).  
> 외부 컴포넌트와 공유하는 값이 아닌, 컴포넌트 자체에 존재하는 로컬 값이다.

## life-cycle에서의 ref

ref가 결정되는 시점은 life-cycle에서 commit phase이다.(빨간 박스)

![](https://velog.velcdn.com/images/rlwjd31/post/b1737243-f759-44db-b934-35e15ea2be76/image.png)

그래서 Initial render에서 ref를 console.log로 확인해보면 값이 null로 나온다.

이는 컴포넌트 코드가 실행되고 나서, componentDidMount 시점에 결정되기 때문이다.

쉽게 다시 서술하자면, 이는 컴포넌트 함수가 실행된 이후에(render phase), ref가 결정되고, 컴포넌트가 마운트되는(componentDidMount) 흐름이다.

## useRef의 type

useRef는 세 가지의 type를 Overloading한다.

useRef는 크게 mutable(변경가능)과 readOnly(변경불가) RefObject 타입을 가지는 2가지 type을 반환한다. 그러나 이러한 useRef들도 단순히 초기값을 **.current**에 저장한다고 생각하면 된다.

이 때, readOnly라고 해도 deep이 아닌 shallow의 특징을 띄기 때문에 ref의 속성은 변경가능하다.

예를 들어 `<input />`에 ref를 사용할 때, ref.current.value를 변경시킬 수 있다.

### 초기값이 주어진 MutableRefObject

인자의 타입과 제네릭의 타입이 T로 일치하는 경우, `MutableRefObject<T>`를 반환한다.

```
interface MutableRefObject<T> {
    current: T;
}

function useRef<T>(initialValue: T): MutableRefObject<T>;
```

MutableRef Object를 반환하므로, Re-render를 트리거하지 않으면서도 local value를 활용하고 싶을 때, 사용한다.

**.current** 속성을 직접 변경할 수 있다.

```
const numberRef = useRef<number>(123);
```

### 초기값 null을 허용하는 ReadOnly RefObject

초기값 null를 허용하지 않는 MutableRefObject와는 다르게, ReadOnly RefObject는 초기값으로 null를 허용하며, 제네릭으로 지정한, type으로 고정된다.

```
interface RefObject<T> {
    readonly current: T | null;
}

function useRef<T>(initialValue: T | null): RefObject<T>;
```

**.current** 속성을 직접 수정할 수 없다.  
그러나 current가 shallow이기 때문에, current의 value 속성에 접근해서 그 값을 수정할 수는 있다.

```
const inputRef = useRef<HTMLInputElement>(null);

const handleButtonClick = () => {
  if (inputRef.current) {
    inputRef.current.value = "";
  }
};

return (
  <div className="App">
  <button onClick={handleButtonClick}>+1</button>

<input ref={inputRef} />
  <button onClick={handleButtonClick}>Clear</button>
</div>
);
```

### 제네릭의 타입이 undefined인 MutableRefObject

제네릭의 타입이 제공되지 않은 경우, `MutableRefObject<T | undefined>`를 반환한다.

```
interface MutableRefObject<T | undefined> {
    current: T | undefined;
}

function useRef<T = undefined>(): MutableRefObject<T | undefined>;
```

```
const ref = useRef<HTMLDivElement>();
```

#### 참고

-   [리액트 공식문서](https://ko.react.dev/learn/render-and-commit)
-   [useRef - velog](https://velog.io/@rlwjd31/useRef#-%EC%B0%B8%EA%B3%A0)
-   [React의 렌더 단계와 커밋 단계](https://www.moonkorea.dev/React-%EB%A0%8C%EB%8D%94%EB%8B%A8%EA%B3%84-%EC%BB%A4%EB%B0%8B%EB%8B%A8%EA%B3%84#1.-%ED%8A%B8%EB%A6%AC%EA%B1%B0-%EB%8B%A8%EA%B3%84---%EC%B4%88%EA%B8%B0-%EB%A0%8C%EB%8D%94)
-   [Fiber 아키텍처의 개념과 Fiber Reconcilation 이해하기](https://blog.naver.com/dlaxodud2388/223195103660)
