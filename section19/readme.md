# React Redux

## State의 종류

### Local State

- 데이터가 변경되어서 하나의 컴포넌트에 속하는 UI에 영향을 미치는 상태
- 무조건 `useState`나 `useReducer` 등으로 관리를 해줘야 한다.
- 예를 들면, 버튼의 on/off 같은 것들을 UI에 표시하는 것

### Cross-Component State

- 하나의 컴포넌트가 아니라 다수의 컴포넌트에 영향을 미치는 상태
- `props chains`나 `props drilling`을 필요로 한다. 
- 예를 들면, 모달 컴포넌트 등

### App-Wide State

- 어플리케이션의 모든 컴포넌트에 영향을 미치는 상태
- `props chains`나 `props drilling`을 필요로 한다.
- 예를 들면, 사용자 인증 등

## React Redux를 사용하는 이유

- `React Context`는 **React**의 내장 기능이고, `Cross-Component State`나 `App-Wide State`를 쉽게 관리를 해준다.
- `React Redux` 또한, `Cross-Component State`나 `App-Wide State`를 위한 상태 관리 시스템이다.
- 그럼에도 왜 `Redux`를 쓰는 이유
  - `React Context`는 잠재적인 단점이 있다.
  - 설정이 복잡해지고, `React Context`를 이용한 상태 관리 또한 상당히 복잡해질 수 있다.
  - 대형 프로젝트는 중첩이 많이 된 컨텍스트가 생산성이나 유지보수성 측면에서 문제될 수 있다.
  - 성능 측면에서도 문제가 있는데, 데이터가 자주 변경되는 경우에는 좋지 않다.
  - `React Context`가 유동적인 상태 확산을 대처할 수 없다.













ㅁ


