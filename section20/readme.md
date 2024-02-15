# Advanced Redux

## Redux and Side Effects with Async

- 리덕스의 중요한 규칙
  - 리듀서 함수는 `pure`, `side-effect free`, `synchoronous`여야 한다.(순수 함수, 사이드 이펙트 x, 동기식)
  - 리덕스 리듀서는 이전 상태 및 액션인 입력이 무조건 필요하며, 새로운 상태인 출력을 무조건 만들어낸다.
- 리덕스를 작업할 때 비동기 코드는 두 곳에 위치한다.
  1. `useEffect`를 사용하여 컴포넌트에 직접 비동기 코드를 작성한다.
  2. 리덕스 툴킷을 사용하지 않고, 직접 `action creator`를 작성한다.
    - 리덕스는 실제로 `action creator`의 일부로 `side effects`를 수행하고, 비동기 작업을 실행할 수 있는 솔루션이 있다.
