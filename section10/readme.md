# Context API & useReducer

## Props Drilling

- 컴포넌트 트리에서 데이터를 하위 컴포넌트로 전달하기 위해 중간 컴포넌트를 통해 property를 내려주는 것
- 중간 컴포넌트는 원하는 자식 컴포넌트에게 property를 전달하기 위해 필요하다.
- 그러나, 해당 값을 직접 사용하지 않는 경우에도 property를 받고 전달해야 한다.

### 장점

- 명시적인 값의 사용
- 값 추적이 편해진다.
- 코드 변경 파악이 편해진다.


### 단점

- property 데이터 형식 변경의 불편
- 중간 컴포넌트에 불필요한 property 전달
- 누락된 property 파악 어려움
- property 이름 변경 추적의 어려움

### 해결법

- Context API
- Redux 또는 다른 상태 관리 라이브러리
- Custom Hooks
- Render Props 패턴과 Children props



