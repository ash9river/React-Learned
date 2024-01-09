# React Core

## React.createElement

- React가 요소들 제어한다.

## JSX

- JSX는 무조건 하나의 요소만 반환해야 한다.
- 대책으로 빈 태그로 모두 감싼다.

```javascript
  return (
    <>
    </>
  )
```

- Fragment로 대체 가능하다.
  
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

## 이미지 저장소

- 빌드 프로세스에 의해 처리되지 않는 이미지는 `public/` 폴더를 사용해야 한다.
  - 예를 들면 `index.html` 파일이나 파비콘과 같은 이미지.
- 컴포넌트 내에서 사용되는 이미지는 일반적으로 `src/` 폴더에 저장되어야 한다.

### public/

- 파일이 프로젝트 개발 서버 및 빌드 프로세스에 의해 공개적으로 제공.
- 브라우저 내에서 직접 방문할 수 있다.
- 다른 파일에 의해 요청될 수도 있다.


### src/assets/

- 파일이 공개적으로 제공되지 않는다. (웹사이트 방문자가 접근 불가)
- 코드 파일에 가져온 이미지는 빌드 프로세스에 의해 인식되어 최적화되며 웹사이트에 제공하기 직전에 `public/` 폴더에 `삽입`된다.
- 가져온 이미지는 참조한 위치에서 자동으로 링크가 생성되어 사용된다.


 









