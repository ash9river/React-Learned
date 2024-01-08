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








