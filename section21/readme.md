# A Multi-Page SPA with React Router

## 라우팅이 하는 일

- 도메인 이름 뒤의 경로에 따라 웹페이지의 콘텐츠를 변경시키는 것
- 단순히 다른 경로에 대해 다른 콘텐츠와 다른 **HTML**를 로딩하는 것으로 라우팅를 구현할 수는 있지만, 항상 새로운 콘텐츠를 가져와야 하는 단점이 있다.
  - 매번 새로운 **HTTP** 요청을 하고, 새로운 **HTTP** 응답을 받는 과정에서 지연이 발생하여, 결국은 사용자 경험이 저하된다.
- 그래서 **SPA**에서는 **HTML** 요청을 한번만 전송하고, **HTML**과 추가적인 **JavaScript**가 다운로드된다.
  - 그 후, 클라이언트에서 실행되는 추가 **JavaScript** 코드가 사용자가 화면에서 경험하는 것들을 실제로 조절하게 된다.
  - 클라이언트 측의 리액트 코드가 현재 사용중인 **URL**을 감시하고, **URL**이 변경될 때마다 화면에 다른 콘텐츠를 표시한다.
- **Recat**에서는 `react-router-dom` 패키지가 **URL*을 감시하고, 다양한 콘텐츠를 로딩한다.

### 리액트 라우터 추가하기

1. 지원하려는 라우터를 정의한다.
    - 지원하려는 **URL**과 다양한 경로에 대해 어떤 컴포넌트가 로딩되어야 하는지 정의한다.
2. 라우터를 활성화하고, 이전에 정의한 라우터를 로딩한다.
3. 로딩하려는 모든 컴포넌트가 있는지 확인하고, 페이지들 간에 이동할 수단이 있는지 확인한다.

## 라우터 정의하기

- `Route`는 단순히 **경로**와 **컴포넌트**를 매핑한다.
- `createBrowserRouter()`를 호출하고, 그 함수에 라우트 정의 객체로 된 배열을 넣음으로써 라우트를 정의한다.(`react-router-dom` v6.4에서 지원한다.)
  - 물론 `BrowserProvider`를 이용하여 `outlet`을 이용한 중첩 라우팅 또한 가능하다.(그러나 `react-router-dom`의 새로운 `data API`를 이용하지 못한다.)
- 배열의 원소인 객체에 `path` 속성을 통해서 라우트가 작동하려는 경로를 정의한다.
- 그리고 `element` 속성을 통하여, 그 경로에 해당되는 라우터가 활성화되면 필요한 컴포넌트를 정의한다.

```javascript
const router = createBrowserRouter([{ path: '/', element: <Home /> }]);
```

- 또한, 이 라우트를 사용하려면 `RouterProvider`의 `router` 속성을 통해서, `createBrowserRouter`를 전달해야 한다.
  - 기본 경로에 `Home` 컴포넌트가 렌더링 되는 것을 확인할 수 있다.

```javascript
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import Home from './pages/Home';

const router = createBrowserRouter([{ path: '/', element: <Home /> }]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

- 다른 방식을 사용하고 싶으면 `createRoutesFromElements()`를 사용함으로써, 이전 버전의 `BrowserProvider`의 재현이 가능하다.

```javascript
import {
  createBrowserRouter,
  RouterProvider,
  createRoutesFromElements,
  Route,
} from 'react-router-dom';
import Home from './pages/Home';
import Products from './pages/Products';

const routeDefinitions = createRoutesFromElements(
  <Route>
    <Route path="/" element={<Home />} />
    <Route path="/products" element={<Products />} />
  </Route>,
);

const router = createBrowserRouter(routeDefinitions);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

### 여러 개의 라우터 추가

- 여러 개의 라우터를 추가하는 방법에는 여러 가지가 있다.
- 먼저, 중첩되지 않게 만들 수도 있다.

```javascript
const router = createBrowserRouter([
  { path: '/', element: <Home /> },
  { path: '/products', element: <Products /> },
]);
```

### Link로 페이지 이동

- `a` 태그의 `href` 속성을 이용하여 페이지 이동을 구현할 수도 있으나, 새로고침을 유발하여 리액트 애플리케이션의 재실행을 야기한다.
  - 서버에 새로 **HTTP** 요청을 발생시켜, 모든 **JavaScript**를 재실행시키고, 컨텍스트나 전체 애플리케이션의 상태를 초기화시킨다.
```javascript
export default function Home() {
  return (
    <>
      <h1>My Home Page</h1>
      <p>
        Go to <a href="/products">the list of products</a>
      </p>
    </>
  );
}
```

- `react-router-dome`에서 지원하는 `Link`의 `to` 속성을 이용하여 새롭게 **HTTP** 요청하는 것을 막고, 전체 애플리케이션의 상태를 유지한 채로 페이지를 이동시킨다.

```javascript
import { Link } from 'react-router-dom';

export default function Home() {
  return (
    <>
      <h1>My Home Page</h1>
      <p>
        Go to <Link to="/products">the list of products</Link>
      </p>
    </>
  );
}
```

## 레이아웃과 중첩된 컴포넌트

> 블로그나 쇼핑몰 등 기본적인 웹사이트를 구성할 때 `Header`, `Section`, `Footer`, `SideBar`와 같은 기본요소를 가지게 된다.
> 메뉴를 클릭하여 페이지가 변경되면 `Header`, `Footer`, `SideBar`는 항상 같은 내용을 유지하며, `Section`의 내용만 변경되게 된다.
> 단순히 모든 컴포넌트에 `Header`, `Footer`, `SideBar`를 `import`할 수는 없다.
> 프로젝트의 크기가 작으면 모르겠지만, 점점 대형 프로젝트로 성장해나갈 시에 걸림돌이 되고 만다.
> 그러한 것들을 타파하기 위해 나온 것이 **레이아웃**과 **중첩된 컴포넌트**이다.

- 먼저 레이아웃을 정의하고, 루트에 그 레이아웃을 `element`로 정의한다.
- 그 후에 `children` 속성을 이용하여 컴포넌트들을 경로에 맞게 중첩시킨다.

```javascript
import {
  createBrowserRouter,
  RouterProvider,
} from 'react-router-dom';
import Home from './pages/Home';
import Products from './pages/Products';
import Layout from './pages/Layout';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    children: [
      { path: '/', element: <Home /> },
      { path: '/products', element: <Products /> },
    ],
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

- 그리고 레이아웃 컴포넌트에서 `react-router-dom`이 제공하는 `Outlet` 컴포넌트를 이용하여 중첩된 자식 요소인 컴포넌트를 렌더링시킨다.

```javascript
import { Outlet } from 'react-router-dom';
import MainNavigation from '../components/MainNavigation';

export default function Layout() {
  return (
    <>
      <MainNavigation />
      <Outlet />
    </>
  );
}
```

- 레이아웃 컴포넌트가 페이지 컴포넌트의 `wrapper` 역할을 한다는 것이 장점이다.

## 에러 페이지

- 모종의 이유로 존재하지 않는 **URL**을 사용자가 방문하려고 할 때, 에러를 페이지에 표시하여 사용자 경험을 증가시킨다.
- 단순히 `errorElment` 속성을 라우터 정의에 추가한다.

```javscript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '/', element: <Home /> },
      { path: '/products', element: <Products /> },
    ],
  },
]);
```

- 루트 컴포넌트인 레이아웃 컴포넌트의 `Outlet`을 이용하지 않고, 에러 엘리먼트 그 자체를 내보냄으로 네비게이션 바 등 추가적인 컴포넌트는 따로 추가해야 한다.

```javascript
import MainNavigation from '../components/MainNavigation';

export default function ErrorPage() {
  return (
    <>
      <MainNavigation />
      <main>
        <h1>An error occurred!</h1>
        <p>Could not find this page!</p>
      </main>
    </>
  );
}
```

### 네비게이션 링크 활용하기

- 네비게이션 링크에 마우스 커서를 가져다 두었을 때, **CSS**의 변화를 주어서 사용자 경험을 향상시킨다.
  - 이 때, `a` 태그로 변화를 주어야 한다.

 ```css
.list a {
  text-decoration: none;
  color: var(--color-primary-400);
}

.list a:hover,
.list a.active {
  color: var(--color-primary-800);
  text-decoration: underline;
}
```
 
- 네이게이션 바는 `NavLink`를 통해 사용자 경험을 더 향상시킬 수 있다.
- `Link`처럼 클릭 시 다른 페이지로 이동시킬 수도 있지만, `className` 속성이 특별한 기능을 발휘한다.
  - 일반적인 문자열을 받는 속성이 아니라, `a` 태그에 추가해야하는 **CSS** 클래스 이름을 반환하는 함수를 가지는 속성이다.
  - 그 반환하는 함수는 객체를 인수로 갖는데, 그 객체에는 `isActive`와 `isPending` 등의 속성을 갖는다.([공식문서 링크](https://reactrouter.com/en/main/components/nav-link))
  - `isActive`는 `boolean`으로, 현재 라우트가 활성화되어 있으면 참이다.
  - 또한, `end` 속성도 갖는데, `end` 속성이 참이면 현재 활성화된 라우트의 **URL** 경로의 끝이 이 경로로 끝나야 활성화되었다고 간주한다.
- `style` 속성을 통해 조건부로 인라인 스타일링도 할 수 있다.


```javascript
import { NavLink } from 'react-router-dom';

import classes from './MainNavigation.module.css';

export default function MainNavigation() {
  return (
    <header className={classes.header}>
      <nav>
        <ul className={classes.list}>
          <li>
            <NavLink
              to="/"
              className={({ isActive }) =>
                isActive ? classes.active : undefined
              }
              style={({ isActive }) => ({
                textAlign: isActive ? 'center' : 'left',
              })}
              end
            >
              Home
            </NavLink>
          </li>
          <li>
            <NavLink
              to="/products"
              className={({ isActive }) =>
                isActive ? classes.active : undefined
              }
            >
              Products
            </NavLink>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

