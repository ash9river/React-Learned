# React Core

## React.createElement

- React가 요소들 제어한다.

## JSX

- JSX는 무조건 하나의 요소만 반환해야 한다.
- 대책으로 빈 태그로 모두 감싼다.
- 
```javascript
  return (
    <>
    </>
  )
```

- Fragment로 대체 가능하다.
- 
```javascript
  return (
    <Fragment>
    </Fragment>
  )
```

## 컴포넌트 분리

- 컴포넌트를 잘게 쪼개서 리렌더링을 최적화시킴
- `Feature`와 `State`로 컴포넌트 분리

```javascript
import Header from "./components/Header";
import Examples from "./components/Examples";
import CoreConcepts from "./components/CoreConcepts";

const App = () => {
  return (
    <>
      <Header />
      <main>
        <CoreConcepts />
        <Examples />
        <h2>Time to get started!</h2>
      </main>
    </>
  );
};

export default App;
```
## 내부요소로 props 전달

- 커스텀 컴포넌트에 자동으로 연결해주지 않음

### forward props & proxy props

- javascript의 `Rest Property`를 사용하여 가져온다.

```javascript
export default function Section({ title, children, ...props }) {
  return (
    <>
      <section {...props}>
        <h2>{title}</h2>
        {children}
      </section>
    </>
  );
}
```











