# A Multi-Page SPA with React Router

## 라우팅이 하는 일

- 도메인 이름 뒤의 경로에 따라 웹페이지의 콘텐츠를 변경시키는 것
- 단순히 다른 경로에 대해 다른 콘텐츠와 다른 **HTML**를 로딩하는 것으로 라우팅를 구현할 수는 있지만, 항상 새로운 콘텐츠를 가져와야 하는 단점이 있다.
  - 매번 새로운 **HTTP** 요청을 하고, 새로운 **HTTP** 응답을 받는 과정에서 지연이 발생하여, 결국은 사용자 경험이 저하된다.
- 그래서 **SPA**에서는 **HTML** 요청을 한번만 전송하고, **HTML**과 추가적인 **JavaScript**가 다운로드된다.
  - 그 후, 클라이언트에서 실행되는 추가 **JavaScript** 코드가 사용자가 화면에서 경험하는 것들을 실제로 조절하게 된다.
  - 클라이언트 측의 리액트 코드가 현재 사용중인 **URL**을 감시하고, **URL**이 변경될 때마다 화면에 다른 콘텐츠를 표시한다.
- **React**에서는 `react-router-dom` 패키지가 **URL**을 감시하고, 다양한 콘텐츠를 로딩한다.

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

```javascript
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
 
- 네비게이션 바는 `NavLink`를 통해 사용자 경험을 더 향상시킬 수 있다.
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

### 강제 라우팅

- `Link`를 활용하지 않고, 프로그램적으로 강제로 페이지를 이동하고 싶으면 `useNavigate()`를 사용한다.
  - 폼 제출, 타이머 완료 등의 경우에 사용한다.
- 단순히 예제 코드이지만, 프로그램적으로 페이지를 이동할 때, 이런 방법으로 사용하면 된다.
 
```javascript
import { Link, useNavigate } from 'react-router-dom';

export default function Home() {
  const navigate = useNavigate();

  function navigationHandler() {
    navigate('/products');
  }

  return (
    <>
      <h1>My Home Page</h1>
      <p>
        Go to <Link to="/products">the list of products</Link>
      </p>
      <p>
        <button onClick={navigationHandler}>Navigate</button>
      </p>
    </>
  );
}
```

### useNavigation을 통한 로딩 인디케이션 UI

- `useNavigation`에서 현재 전환이 진행되고 있는 중는지, 또는 데이터를 로딩하는 중인지, 아니면 전환이 진행되고 있지 않은지 알 수 있다.
- `useNavigation`의 `state`가 `idle`, `loading`, `submitting`인지를 통해 어느 상태인지 판별할 수 있다.
- 현재 데이터를 기다리는지 아닌지 알아내고, 그 로딩 인디케이터를 불러올 수 있는 한 가지 방법이다.
- 이 로딩 인디케이터는 이미 화면에 표시되어 있는 페이지, 컴포넌트에 추가된다.(보통 상단에 작은 로딩바가 추가된다.)

```javascript
import { Outlet, useNavigation } from 'react-router-dom';
import MainNavigation from '../components/MainNavigation';

export default function Layout() {
  const navigation = useNavigation();

  return (
    <>
      <MainNavigation />
      <main>
        {navigation.state === 'loading' && <p>Loading...</p>}
        <Outlet />
      </main>
    </>
  );
}
```


## 동적 라우트

- 제품의 경로 또는 게시글의 경로 등을 라우트에 포함시킬려면 기존의 고정 경로 추가로는 부족해져서, 새로운 방법이 필요해졌다.
- `Dynamic Path Segment(path parameter)`를 통하여 동적으로 라우트한다.
- `path`에 `:`를 넣고, 그 뒤에 원하는 식별자를 넣어서 동적 경로 세그먼트를 추가한다.
- `:`는 경로에서 이 부분이 동적이라는 것을 `react-router-dom`에 알려준다.

```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '/', element: <Home /> },
      { path: '/products', element: <Products /> },
      { path: '/products/:productId', element: <ProductDetail /> },
    ],
  },
]);
```

- 동적 라우팅으로 만들어진 페이지에서 동적으로 사용된 값을 알고 싶을 때에는 `useParams()`을 사용한다.
- `useParams` 훅은 라우트 정의에서 속성으로 정의한 모든 동적 경로 세그먼트가 담긴 **JS** 객체이다.
- 동적 경로 세그먼트에서 사용한 식별자를 `useParams`를 통한 객체에서 식별자를 속성값으로 이용하여, 우리가 원하고자 하는 값을 추출할 수 있다.

```javascript
import { useParams } from 'react-router-dom';

export default function ProductDetail() {
  const params = useParams();

  const { productId } = params;

  return (
    <>
      <h1>Product Details!</h1>
      <p>{productId}</p>
    </>
  );
}
```

### 동적 라우트에 링크 추가

- 단순히 자바스크립트 백택을 이용한다.

```javascript
export default function Products() {
  return (
    <>
      <h1>The Products Page</h1>;
      <ul>
        {PRODUCTS.map((item) => {
          return (
            <li key={item.id}>
              <Link to={`/products/${item.id}`}>{item.title}</Link>
            </li>
          );
        })}
      </ul>
    </>
  );
}
```

## 절대 경로와 상대 경로

- `path` 속성이 `/`로 시작하면 절대 경로이다.
- 만약 `path` 속성에서 `/`를 제거하게 된다면 상대 경로이다.
- 상대 경로는 `wrapper` 라우터의 경로 뒤에 중첩되어 첨부된다.
- 예시 코드에서 `Home` 컴포넌트의 경로는 `/root`가 될 것이고, `Products` 컴포넌트는 `/products`가 될 것이다.
- 또한, `ProductDetail` 컴포넌트의 경로는 `/root/products/:prductId`가 될 것이다.

```javascript
const router = createBrowserRouter([
  {
    path: '/root',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '', element: <Home /> },
      { path: 'products', element: <Products /> },
      { path: 'products/:productId', element: <ProductDetail /> },
    ],
  },
]);
```

- `Link`에서도 상대 경로를 이용하려면, `/`를 제거하면 된다.

<details>
  <summary>코드 보기</summary>

```javascript
export default function Products() {
  return (
    <>
      <h1>The Products Page</h1>;
      <ul>
        {PRODUCTS.map((item) => {
          return (
            <li key={item.id}>
              <Link to={`products/${item.id}`}>{item.title}</Link>
            </li>
          );
        })}
      </ul>
    </>
  );
}
```

또는

```javascript
export default function Home() {
  const navigate = useNavigate();

  function navigationHandler() {
    navigate('/products');
  }

  return (
    <>
      <h1>My Home Page</h1>
      <p>
        Go to <Link to="products">the list of products</Link>
      </p>
      <p>
        <button onClick={navigationHandler}>Navigate</button>
      </p>
    </>
  );
}
```
</details>

- `Link`에서 `to` 속성으로 `..`을 추가할 수 있는데, 이는 활성화된 경로의 상위 라우트로 돌아가라는 뜻이다.
- 그런데, `ProductDetail` 컴포넌트에서 `Link`의 `..` 속성을 이용하면, `Home` 컴포넌트로 돌아온다.
  - 상위 라우트, 부모의 라우트로 돌아갔기 때문이다. 
```javascript
const router = createBrowserRouter([
  {
    path: '/root',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '', element: <Home /> },
      { path: 'products', element: <Products /> },
      { path: 'products/:productId', element: <ProductDetail /> },
    ],
  },
]);
```
```javascript
export default function ProductDetail() {
  const params = useParams();

  const { productId } = params;

  return (
    <>
      <h1>Product Details!</h1>
      <p>{productId}</p>
      <p>
        <Link to="..">Back</Link>
      </p>
    </>
  );
}
```

- 물론 경로를 지금처럼 형제 관계가 아니라 부모 관계로 설정할 수 있지 않겠냐고 할 수 있겠지만, 그러면 `ProductDetail` 컴포넌트에서는 `Layout` 컴포넌트에 있는  `Outlet`(`react-router-dom`에서 지원한다.)의 이용이 불가능해진다.
  - 이 때, 새로운 레이아웃을 추가하는 방식을 채택할 수도 있으나 번거로움이 생길수도 있어서, 부모 라우트의 엘리먼트 속성을 비워두고, 자식 속성을 추가하는 방식으로도 가능하다.

```javascript
const router = createBrowserRouter([
  {
    path: '/root',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '', element: <Home /> },
      {
        path: 'products',
        element: <Products />,
        children: [{ path: ':productId', element: <ProductDetail /> }],
      },
    ],
  },
]);
```

- `Products` 컴포넌트에서 **URL**이 바뀌어도 콘텐츠는 `Products`만 나타나고 있다.

<img width="80%" height="80%" src="https://github.com/ash9river/React-Learned/assets/121378532/7ef22229-c15f-4be7-8cfe-94355e6d7dd8"/>

- `Link`를 `..`과 같은 상대경로를 이용할 때, 라우트 정의를 바꾸지 않고자 한다면 `relative` 속성을 이용한다.
  - `relative` 속성을 `path` 혹은 `route`로 설정할 수 있다. (기본 값은 `route`이다.)
- `relative`를 `path`로 설정한다면, 현재 활성화된 경로에서 한 세그먼트만 제거하게 된다.
  - 이 코드에서는 `/:productId`가 제거된다.

```javascript
import { Link, useParams } from 'react-router-dom';

export default function ProductDetail() {
  const params = useParams();

  const { productId } = params;

  return (
    <>
      <h1>Product Details!</h1>
      <p>{productId}</p>
      <p>
        <Link to=".." relative="path">
          Back
        </Link>
      </p>
    </>
  );
}
```

### 인덱스 라우트

- 기존 코드에서는 래퍼 경로와 자식의 경로가 일치하여 경로를 빈 문자열로 설정하는 경우가 발생한다.

```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '', element: <Home /> },
      { path: 'products', element: <Products /> },
      { path: 'products/:productId', element: <ProductDetail /> },
    ],
  },
]);
```

- 그러나 빈 문자열로 설정하는 대신에 `index` 속성을 추가하여 기본 라우트로 설정할 수 있다.
 - `index` 속성은 부모 라우트가 활성되었을 때, 로딩하는 기본 라우트를 정의할 수 있게 해준다.
```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { index: true, element: <Home /> },
      { path: 'products', element: <Products /> },
      { path: 'products/:productId', element: <ProductDetail /> },
    ],
  },
]);
```

### 라우터의 순서는 상관이 없다.

- `react-router-dom`은 좀 더 스마트하기 때문에, 자동으로 처리해준다.
- 지금은 가독성 좋게 배치를 했지만, 순서와 상관없이 `/events/new`라는 경로를 들어가면 `eventId`의 페이지가 아니라, `new`의 페이지가 렌더링된다.

```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: '/events', element: <EventsPage /> },
      { path: '/events/new', element: <NewEventPage /> },
      { path: '/events/:eventId', element: <EventDetailPage /> },
      { path: '/events/:eventId/edit', element: <EditEventPage /> },
    ],
  },
]);
```

### 중첩 레이아웃

- `Layout` 컴포넌트가 아니라 `EventsRootLayout` 컴포넌트에서 `Outlet`을 새로 만들어서 활용한다.

```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    children: [
      { index: true, element: <HomePage /> },
      {
        path: 'events',
        element: <EventsRootLayout />,
        children: [
          { index: true, element: <EventsPage /> },
          { path: 'new', element: <NewEventPage /> },
          { path: ':eventId', element: <EventDetailPage /> },
          { path: ':eventId/edit', element: <EditEventPage /> },
        ],
      },
    ],
  },
]);
```

# Optimization of React Router 

- 리액트 라우터 v6.4에서 새로운 데이터 **API**를 지원하는 새로운 라우터가 도입되었다.([공식 문서](https://reactrouter.com/en/main/routers/picking-a-router))
- 그에 따라, 서버에서 **API**를 통해 데이터를 받아올 때, 리액트 라우터가 **CSR**을 활성화시켜서, 데이터를 받아올 때 등의 경우에 사용자 경험을 좀 더 향상시킨다.
- `loader`, `action`, `fetcher`를 살펴보고, 어떤 식으로 작동하는지 확인해보자.

## loader

- 리액트 라우터의 `loader`는 페이지의 라우터 정의에 추가하는 속성으로써, 일반 함수나 오류 함수, 모두를 값으로 가질 수 있다.
- `loader`는 불러오고자 하는 데이터를 미리 가져오고, 그 후에 라우트에 해당되는 컴포넌트를 렌더링을 함으로써, 사용자 경험을 향상시키는 방법이다.
  - 즉, 컴포넌트가 렌더링 되기 이전에 먼저 데이터를 가져오고, 컴포넌트에 데이터를 전달한다.
- `loader`의 호출 시점은 컴포넌트가 렌더링 되기 전이며, 컴포넌트가 렌더링 되기 전에 **APT**와 통신한다.
  - 그 후에 응답을 받고나서 렌더링을 시작한다.

> `loader`를 통해 사용자 경험 향상시킬수 있다.
> 또한, 데이터 가져오기 및 랜더링 관련 개발자 경험을 개선하고, 컴포넌트 마운트 단계에서 상태에 **API**에서 받아온 값이 **UI**에 뿌려지지 않는 이슈를 해결할 수 있다.

- 리액트 라우터는 `loader` 함수를 정의할 때, 그 함수에서 반환하는 모든 값을 렌더링되는 페이지뿐만이 아니라, 그 데이터가 필요한 모든 컴포넌트에게도 제공한다.

### loader 데이터를 컴포넌트에서 액세스

- `react-router-dom`에서 제공하는 `useLoaderData()`라는 훅을 사용한다.
- `useLoaderDate`는 **가장 가까운 loader 데이터**를 액세스 하기 위해 실행하는 특수한 훅이다.
- 다음과 같은 경우, `loader`에서 `async/await`을 사용하고 있기 때문에 `promise`를 반환할 것이다.
  - 리액트 라우터는 이와 같은 경우, 실제로 `promise`를 반환하는 것을 확인하고, 그 `promise`를 `resolve`하여 반환한다.
  - `useLoaderData`는 컴포넌트에서 자동으로 `promise`에서 산출된 데이터를 받게 도와준다. 

```javascript
loader: async () => {
  const response = await fetch('http://localhost:8080/events');
  if (!response.ok) {
    // ...
  }
  const resData = await response.json();
  return resData.events;
},
```

```javascript
import { useLoaderData } from 'react-router-dom';

import EventsList from '../components/EventsList';

function EventsPage() {
  const events = useLoaderData();

  return <EventsList events={events} />;
}

export default EventsPage;
```

- `useLoaderData`를 `loader`가 정의된 페이지보다 상위 레벨의 페이지(예를 들면 루트 페이지 등)에서 사용할 수 없다.
  - 그러나 어느 페이지에 정의되어 있다면, 그 페이지에 연관된(중첩된) 모든 하위 레벨 컴포넌트에서는 사용할 수 있다. 

### loader 코드의 위치

- 보통 페이지 코드 안에 있는 것이 일반적이다.
- 라우팅하는 컴포넌트가 좀 더 간결해졌다.

```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    children: [
      { index: true, element: <HomePage /> },
      {
        path: 'events',
        element: <EventsRootLayout />,
        children: [
          {
            index: true,
            element: <EventsPage />,
            loader: eventLoader,
          },
          { path: 'new', element: <NewEventPage /> },
          { path: ':eventId', element: <EventDetailPage /> },
          { path: ':eventId/edit', element: <EditEventPage /> },
        ],
      },
    ],
  },
]);
```

```javascript
import { json, useLoaderData } from 'react-router-dom';

import EventsList from '../components/EventsList';

function EventsPage() {
  const events = useLoaderData();

  return <EventsList events={events} />;
}

export default EventsPage;

export async function eventLoader() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    throw json(
      {
        message: 'Could not fetch events',
      },
      {
        status: 500,
      },
    );
  }
  const resData = await response.json();
  return resData.events;
}
```

### loader에서 응답 반환하기

- 브라우저에서 지원하는 `Response()`로 새로운 응답 개체를 반환할 수 있다.
- `loader`는 서버가 아니라 클라이언트에서 실행되는 **CSR**이지만, 브라우저를 통해 응답 객체를 생성할 수 있다.
  - 브라우저가 생성자와 응답 객체를 지원하기 때문이다.
- `Response()`의 생성자로, 첫 번째 인자는 원하는 아무 데이터를 집어넣을 수 있다.
  - 두 번째 인자는, 응답 객체를 `status`같은 상태 코드를 추가하여, 좀 더 자세히 설정하는 것이다.  
- 그러나 리액트 라우터의 `loader`는 이런 응답 객체들을 지원하고, 자동으로 데이터를 추출하기 때문에 `Response`의 `resolve`는 크게 신경쓸 필요가 없다.
  
```javascript
export async function eventLoader() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    // 실패시 로직
  }
  return Response('any Data', { status: 201 });
}
```

- 신경안쓰고 그냥 `promise`를 반환하자.
- `useLoaderData`에서 데이터를 꺼내올 때만, 객체에서 꺼내오듯이 하면 된다.

```javascript
function EventsPage() {
  const { events } = useLoaderData();

  if (events.isError) {
    return <p>{events.message}</p>;
  }

  return <EventsList events={events} />;
}

export default EventsPage;
export async function eventLoader() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    // 실패시 로직
  }
  return response;
}
```

### loader는 브라우저에서 실행된다

- `loader` 함수에서 어떠한 브라우저 **API**이던간에 사용할 수 있다.
  - `loader`는 서버에서 실행되는 것이 아니라 브라우저에서 실행되기 때문이다.
  - 쿠키에 액세스하거나, 로컬 스토리지에 액세스하거나, **JS**의 실행을 하는 등 여러가지 동작이 가능하다.
- 하지만, 리액트 컴포넌트가 아니기 때문에, 리액트 훅은 사용할 수 없다.

## loader를 이용한 오류 처리

- 단순히 오류 메세지를 만들어서 활용할 수는 있지만, 권장되지는 않는다.

```javascript
import { useLoaderData } from 'react-router-dom';

import EventsList from '../components/EventsList';

function EventsPage() {
  const { events } = useLoaderData();

  if (events.isError) {
    return <p>{events.message}</p>;
  }

  return <EventsList events={events} />;
}

export default EventsPage;

export async function eventLoader() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    return {
      isError: true,
      message: 'Could not fetch events!',
    };
  }
  return response;
}
```

- 오류 데이터를 반환하지 않고, `throw`로 오류를 내보낸다.
- 그러면 가장 가까운 `errorElement`가 렌더링된다.
- 그런데, `errorElement` 의 컴포넌트에서 리액트 라우터가 제공하는 `useRouteError()` 훅을 이용하면, 재사용성 높은, 포괄적인 컴포넌트를 만들 수 있다.
- `useRouteError`의 `data` 속성은 `throw`된 `Response`의 `message`에 접근할 수 있게 해준다.
  - 물론, **JSON**형태로 생성자를 만들어 전달했으므로, **JSON**의 `parse()`를 이용하여 파싱해야 된다. 

```javascript
export async function eventLoader() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    throw new Response(
      JSON.stringify(
        {
          message: 'Could not fetch events.',
        },
        { status: 500 },
      ),
    );
  } else return response;
}
```
```javascript
import { useRouteError } from 'react-router-dom';
import PageContent from '../components/PageContent';

export default function ErrorPage() {
  const error = useRouteError();

  let title = 'An error occured!';

  let message = 'Something went wrong!';

  if (error.status === 500) {
    message = JSON.parse(error.data).message;
  }

  if (error.status === 404) {
    title = 'Not found!';
    message = 'Could not find resource or page.';
  }

  return (
    <>
      <MainNavigation />
      <PageContent title={title}>
        <p>{message}</p>
      </PageContent>
    </>
  );
}
```

### 리액트 라우터에서 제공하는 json()

- 리액트 라우터에서는 생성자 대신에 사용할 수 있는 `json()`함수를 지원한다.
- 리액트 라우터의 `json()`을 사용하면, 생성자를 만드는 것보다 코드가 짧아질 뿐만이 아니라, 사용하는 부분에서도 파싱할 필요가 없게 된다.

```javascript
import { json, useLoaderData } from 'react-router-dom';

import EventsList from '../components/EventsList';

function EventsPage() {
  const { events } = useLoaderData();

  if (events.isError) {
    return <p>{events.message}</p>;
  }

  return <EventsList events={events} />;
}

export default EventsPage;

export async function eventLoader() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    throw json(
      {
        message: 'Could not fetch events',
      },
      {
        status: 500,
      },
    );
  } else return response;
}

```
```javascript
import { useRouteError } from 'react-router-dom';
import PageContent from '../components/PageContent';
import MainNavigation from '../components/MainNavigation';

export default function ErrorPage() {
  const error = useRouteError();

  let title = 'An error occured!';

  let message = 'Something went wrong!';

  if (error.status === 500) {
    message = error.data.message;
  }

  if (error.status === 404) {
    title = 'Not found!';
    message = 'Could not find resource or page.';
  }

  return (
    <>
      <MainNavigation />
      <PageContent title={title}>
        <p>{message}</p>
      </PageContent>
    </>
  );
}
```

## 동적 라우트와 loader

- 리액트 라우터가 `loader`를 실행할 때, 객체를 `loader`에 전달한다.
  - 그 객체에서는 요청 객체를 담고 있는 `request` 속성과, 모든 라우트의 파라미터가 담긴 `params` 속성을 가지고 있다. 
  - `request` 객체의 `url` 속성을 통해 쿼리 파라미터를 추출할 수도 있다.
- `params` 객체에서 해당하는 식별자를 통하여 값을 추출한다.

```javascript
import { json, useLoaderData, useParams } from 'react-router-dom';

import EventItem from '../components/EventItem';

export default function EventDetailPage() {
  const data = useLoaderData();
  const { event } = data;

  return <EventItem event={event} />;
}

export async function eventDetailLoader({ request, params }) {
  const id = params.eventId;

  const response = await fetch(`http://localhost:8080/events/${id}`);

  if (!response.ok) {
    throw json(
      {
        message: 'Could not fetch details for selected event.',
      },
      {
        status: 500,
      },
    );
  } else return response;
}
```

## useRouteLoaderData

- 래퍼 라우트에 `loader`를 두어서, 공통인 `loader`를 사용하기 위해 레벨의 상단에 위치시킨다.
- `useLoaderData`가 라우트에 로드된 데이터를 뽑아내는 것이라면, `useRouteLoaderData`는 특정 라우트의 로더에서 로드된 데이터를 뽑아내는 것이다.
- 가장 가까운 로더의 데이터를 뽑아야 하는데 작동이 잘 안된다. 이유를 모르겠어서 계층 문제였나 싶었는데, `useRouteLoaderData`를 사용하니 작동이 잘된다.
- 이유는 좀더 찾아봐야겠다.

```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { index: true, element: <HomePage /> },
      {
        path: 'events',
        element: <EventsRootLayout />,
        children: [
          {
            index: true,
            element: <EventsPage />,
            loader: eventLoader,
          },
          {
            path: ':eventId',
            id: 'eventId',
            loader: eventDetailLoader,
            children: [
              {
                index: true,
                element: <EventDetailPage />,
              },
              { path: 'edit', element: <EditEventPage /> },
            ],
          },
          { path: 'new', element: <NewEventPage /> },
        ],
      },
    ],
  },
]);
```
```javascript
import { useLoaderData, useRouteLoaderData } from 'react-router-dom';
import EventForm from '../components/EventForm';

export default function EditEventPage() {
  const { event } = useRouteLoaderData('eventId');
  return <EventForm event={event} />;
}
```

- 리액트에서 제공하는 `defaultValue`를 이용하여 기본값을 만들었다.

```javascript
import { useNavigate } from 'react-router-dom';

import classes from './EventForm.module.css';

function EventForm({ method, event }) {
  const navigate = useNavigate();
  function cancelHandler() {
    navigate('..');
  }

  return (
    <form className={classes.form}>
      <p>
        <label htmlFor="title">Title</label>
        <input
          id="title"
          type="text"
          name="title"
          required
          defaultValue={event ? event.title : ''}
        />
      </p>
      <p>
        <label htmlFor="image">Image</label>
        <input
          id="image"
          type="url"
          name="image"
          required
          defaultValue={event ? event.image : ''}
        />
      </p>
      <p>
        <label htmlFor="date">Date</label>
        <input
          id="date"
          type="date"
          name="date"
          required
          defaultValue={event ? event.date : ''}
        />
      </p>
      <p>
        <label htmlFor="description">Description</label>
        <textarea
          id="description"
          name="description"
          rows="5"
          required
          defaultValue={event ? event.description : ''}
        />
      </p>
      <div className={classes.actions}>
        <button type="button" onClick={cancelHandler}>
          Cancel
        </button>
        <button>Save</button>
      </div>
    </form>
  );
}

export default EventForm;
```

## action

### Form with react-router-dom

- 모든 `input` 태그에 `name` 속성이 있어야 한다.
- `Form`은 백엔드로 요청을 전송하는 브라우저의 기본값을 생략하고, 대신 `action`에 그 요청을 보낸다.
- `Form`의 `method` 속성으로 `post`를 고르면, 백엔드가 아니라 액션으로 모든 폼데이터가 포함된 요청이 전송된다.
- 액션에서 이용할 때는, `request` 속성의 `formData()` 메서드를 이용한다.
- 그리고 `post`가 성공적으로 이루어졌다면, `redirect()`를 통해 리스트 페이지로 이동시켜서 사용자 경험을 향상시킨다.

```javascript
export async function action({ request, params }) {
  const data = await request.formData();

  const eventData = {
    title: data.get('title'),
    image: data.get('image'),
    date: data.get('date'),
    description: data.get('description'),
  };

  console.log(eventData);

  const response = await fetch('http://localhost:8080/events', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(eventData),
  });

  if (!response.ok) {
    throw json(
      {
        message: 'Could not save event.',
      },
      {
        status: 500,
      },
    );
  }
  return redirect('/events');
}
```

- `Form`은 단순히 이런 형태이다.

```html
<Form method="post" className={classes.form}>
  <p>
    <label htmlFor="title">Title</label>
    <input
      id="title"
      type="text"
      name="title"
      required
      defaultValue={event ? event.title : ''}
    />
  </p>
  <p>
    <label htmlFor="image">Image</label>
    <input
      id="image"
      type="url"
      name="image"
      required
      defaultValue={event ? event.image : ''}
    />
  </p>
  <p>
    <label htmlFor="date">Date</label>
    <input
      id="date"
      type="date"
      name="date"
      required
      defaultValue={event ? event.date : ''}
    />
  </p>
  <p>
    <label htmlFor="description">Description</label>
    <textarea
      id="description"
      name="description"
      rows="5"
      required
      defaultValue={event ? event.description : ''}
    />
  </p>
  <div className={classes.actions}>
    <button type="button" onClick={cancelHandler}>
      Cancel
    </button>
    <button>Save</button>
  </div>
</Form>
```

- `Form`은 현재 활성화된 라우트의 `action` 함수를 자동으로 실행시킨다.
- 그러나, `Form`에 `action` 속성이 있는데, 그 `action` 속성을 추가하고, 다른 경로로 설정해서 다른 라우트로 요청을 보낼 수 있다.
  - 그러면 다른 라우트 정의 객체에 `action` 함수가 있다면, 그 `action` 함수가 실행된다.

```html
<Form method="post" action="/any-other-path" className={classes.form}>
```

### useSubmit()

- `react-router-dom`에서는 `useSubmit` 훅을 제공한다.
- 첫 번째 인자로는 우리가 제출하려는 데이터를 넣는다.
  - 이 데이터는 자동으로 폼 객체로 감싸지고, `formData()` 메서드로 추출할 수 있다. 
- 두 번째 인자로는 여러 가지 설정값이 들어간다.
  - `method`, `action` 등 여러 가지 값을 설정할 수 있다. 

```javascript
const submit = useSubmit();

  function startDeleteHandler() {
    const proceed = window.confirm('Are you sure?');

    if (proceed) {
      submit(null, {
        method: 'DELETE',
      });
    }
  }
```

-  `useSubmit` 훅을 이용해 넘어간 `method`가 액션에서 쓰이고 있다.

```javascript
export async function action({ request, params }) {
  const { eventId } = params;

  const response = await fetch(`http://localhost:8080/events/${eventId}`, {
    method: request.method,
  });

  if (!response.ok) {
    throw json(
      {
        message: 'Could not delete event.',
      },
      {
        status: 500,
      },
    );
  } else {
    return redirect('/events');
  }
}
```

### 제출 상태를 파악하는 useNavigation()

- 여러 번 똑같은 데이터를 제출하는 것을 방지하기 위해, 제출 중이라는 것을 표시하는 **UI**가 필요하다.
- `useNavigation` 훅을 통해 네비게이션 객체에 대한 액세스를 제공하고, 그 객체에서 다양한 정보를 추출할 수 있다.
  - 제출된 모든 데이터를 추출할 수도 있고, 현재 활성된 `state`가 무엇인지 확인할 수 있다.
  - 또한, 현재의 데이터 제출 과정에 관한 정보와 그 제출이 이미 완료되었는지 확인할 수 있다.(트리거된 액션의 완료도 파악할 수 있다.)

```javascript
const navigation = useNavigation();

const isSubmitting = navigation.state === 'submitting';
```
```html
<button type="button" onClick={cancelHandler} disabled={isSubmitting}>
  Cancel
</button>
<button disabled={isSubmitting}>
  {isSubmitting ? 'Submitting' : 'Save'}
</button>
```

### useActionData()를 통한 클라이언트 측과 서버측 입력값 검증

- 클라이언트 측 입력값 검증은 관리자 도구로도 제거할 수 있기때문에, 프론트와 백에서 모두 검증하는 것이 필요하다.
- `action`으로 입력값을 백으로 보내서 데이터가 입력값이 잘못되었으면 `response`를 반환한다.
- `useActionData` 훅은 가장 가까운 `action`이 반환한 데이터에 액세스할 수 있다.
  - 이를 이용해 `action`에서 반환한 `response`에서 값을 추출한다.
- `useActionData`를 페이지 컴포넌트가 아닌 컴포넌트에서도 사용할 수 있다.
  - 그 이유는 그 컴포넌트가 `action`이 정의된 페이지 컴포넌트에 의해 렌더링되기 때문이다.

<details>
  <summary>액션 코드</summary>

```javascript
import { json, redirect } from 'react-router-dom';
import EventForm from '../components/EventForm';

export default function NewEventPage() {
  return <EventForm />;
}

export async function action({ request, params }) {
  const data = await request.formData();

  const eventData = {
    title: data.get('title'),
    image: data.get('image'),
    date: data.get('date'),
    description: data.get('description'),
  };

  const response = await fetch('http://localhost:8080/events', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(eventData),
  });

  if (response.status === 422) {
    return response;
  }

  if (!response.ok) {
    throw json(
      {
        message: 'Could not save event.',
      },
      {
        status: 500,
      },
    );
  }
  return redirect('/events');
}
```
</details>

```javascript
const data = useActionData();
  return (
    <>
      {data && data.errors && (
              <ul>
                {Object.values(data.errors).map((err) => (
                  <li key={err}>{err}</li>
                ))}
              </ul>
            )}
    </>
  )
```
      
### requset 객체로 action 재사용하기

- 컴포넌트를 생성하는 측에서 `method`를 전달하고, 그 `method`에 따라 동적으로 `action`을 이용한다.
- 리액트 라우터가 생성하고 `action`에 전달된 클라이언트 측 요청에 `method`를 같이 설정하기 위해서 이 `method`를 설정한 것이다.
- 클라이언트에서 액션을 통해 보낸 요청에서 `request`의 속성으로 `method`를 추출하고, 적절하게 백엔드로 보내는 `method`를 동적으로 설정할 수 있다.

<details>
  <summary>코드 보기</summary>

- 라우트 설정
```javascript
const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { index: true, element: <HomePage /> },
      {
        path: 'events',
        element: <EventsRootLayout />,
        children: [
          {
            index: true,
            element: <EventsPage />,
            loader: eventLoader,
          },
          {
            path: ':eventId',
            id: 'eventId',
            loader: eventDetailLoader,
            children: [
              {
                index: true,
                element: <EventDetailPage />,
                action: eventDetailAction,
              },
              {
                path: 'edit',
                element: <EditEventPage />,
                action: manipulateEventAction,
              },
            ],
          },
          {
            path: 'new',
            element: <NewEventPage />,
            action: manipulateEventAction,
          },
        ],
      },
    ],
  },
]);
```

```javascript
import {
  useNavigate,
  Form,
  useNavigation,
  useActionData,
  json,
  redirect,
} from 'react-router-dom';

import classes from './EventForm.module.css';

function EventForm({ method, event }) {
  const data = useActionData();

  const navigate = useNavigate();

  const navigation = useNavigation();

  const isSubmitting = navigation.state === 'submitting';

  function cancelHandler() {
    navigate('..');
  }

  return (
    <Form method={method} className={classes.form}>
      {data && data.errors && (
        <ul>
          {Object.values(data.errors).map((err) => (
            <li key={err}>{err}</li>
          ))}
        </ul>
      )}
      <p>
        <label htmlFor="title">Title</label>
        <input
          id="title"
          type="text"
          name="title"
          required
          defaultValue={event ? event.title : ''}
        />
      </p>
      <p>
        <label htmlFor="image">Image</label>
        <input
          id="image"
          type="url"
          name="image"
          required
          defaultValue={event ? event.image : ''}
        />
      </p>
      <p>
        <label htmlFor="date">Date</label>
        <input
          id="date"
          type="date"
          name="date"
          required
          defaultValue={event ? event.date : ''}
        />
      </p>
      <p>
        <label htmlFor="description">Description</label>
        <textarea
          id="description"
          name="description"
          rows="5"
          required
          defaultValue={event ? event.description : ''}
        />
      </p>
      <div className={classes.actions}>
        <button type="button" onClick={cancelHandler} disabled={isSubmitting}>
          Cancel
        </button>
        <button disabled={isSubmitting}>
          {isSubmitting ? 'Submitting' : 'Save'}
        </button>
      </div>
    </Form>
  );
}

export default EventForm;

export async function action({ request, params }) {
  const data = await request.formData();
  const { method } = request;

  const eventData = {
    title: data.get('title'),
    image: data.get('image'),
    date: data.get('date'),
    description: data.get('description'),
  };

  let url = 'http://localhost:8080/events';

  if (method === 'PATCH') {
    const { eventId } = params;
    url = `http://localhost:8080/events/${eventId}`;
  }

  const response = await fetch(url, {
    method,
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(eventData),
  });

  if (response.status === 422) {
    return response;
  }

  if (!response.ok) {
    throw json(
      {
        message: 'Could not save event.',
      },
      {
        status: 500,
      },
    );
  }
  return redirect('/events');
}
```

</details>

## useFetcher

- 중첩된 라우트 바깥에서 `loader`나 `action`을 가져오고 싶을 때, **URL**을 바꾸지 않고 가져올 수 있는 것이 `useFetcher()`이다.
- `useFetcher` 객체에는 유용한 속성과 메서드가 많이 포함되어 있다.
- 그 중에서도 `Form` 을 이용하면 실제로 `action`을 트리거한다.

> 즉 `useFetcher`는 `action`을 트리거하거나 `loader`의 도움으로 `load`를 트리거하지만, 실제로 `action` 혹은 `loader`가 속한 페이지로 이동하지 않을 때, 사용한다.

```javascript
import { useFetcher } from 'react-router-dom';
import classes from './NewsletterSignup.module.css';

function NewsletterSignup() {
  const fetcher = useFetcher();

  return (
    <fetcher.Form
      method="post"
      action="/newsletter"
      className={classes.newsletter}
    >
      <input
        type="email"
        placeholder="Sign up for newsletter..."
        aria-label="Sign up for newsletter"
      />
      <button>Sign up</button>
    </fetcher.Form>
  );
}

export default NewsletterSignup;
```

- 그러나 이대로는, **UI**에 반영이 되지 않는다.
- 라우트 변경을 트리거하지 않은 채로, 요청을 전송하고, `fetcher` 객체에서 `data` 속성을 이용하여 반환된 데이터에 액세스한다.
- 또한, `fetcher` 객체의 `state`는 `useNavigation`에서 얻을 수 있는 `idle`, `loading`, `submitting`의 값 중 하나를 가지고 있다.
  - 그러나 `fetcher` 객체의 `state`는 `useNavigation`과 달리 **실제 라우트 변경이 일어나지 않고**, 트리거된 `action` 혹은 `loader`의 상태를 알려준다. 
- 그 다음에 `useEffect`를 통해서 **UI**를 업데이트할 수 있다.

```javascript
import { useFetcher } from 'react-router-dom';
import { useEffect } from 'react';
import classes from './NewsletterSignup.module.css';

function NewsletterSignup() {
  const fetcher = useFetcher();

  const { data, state } = fetcher;

  useEffect(() => {
    if (state === 'idle' && data && data.message) {
      window.alert(data.message);
    }
  }, [data, state]);

  return (
    <fetcher.Form
      method="post"
      action="/newsletter"
      className={classes.newsletter}
    >
      <input
        type="email"
        placeholder="Sign up for newsletter..."
        aria-label="Sign up for newsletter"
      />
      <button>Sign up</button>
    </fetcher.Form>
  );
}

export default NewsletterSignup;
```

> ❕ 공통된 컴포넌트가 있거나, 같은 페이지에서 여러 번 사용되는 컴포넌트가 있을 경우에 사용자에게 보이지 않는 곳에서 데이터만 업데이트하거나, 받을 때 유용하다.

## defer()로 데이터 로딩 전에 렌더링

- 데이터가 전부 도착하기 전까지 시간이 오래 걸릴 경우, 미리 받은 데이터들로만 컴포넌트를 렌더링할 수도 있다.
- `loader`에서는 로딩을 연기하는 특수한 함수를 쓸 수 있는데 그게 `defer()`이다.
- `loader` 함수에서 `promise`를 기다리지 않고, `defer`를 통해, 데이터를 전부 받기 전에 로딩을 연기하고, 컴포넌트를 렌더링 하는 방식이다.
- `defer`가 인수로 받는 객체에는 페이지에서 오갈 수 있는 모든 **HTTP** 요청을 넣어야 한다.
  - 객체의 키명은 원하는 방식을 정하고, 그 값을 지정하지 않고 실행한다.
- 또한, `defer`를 사용할 때, 연기되는 `async` 함수는 `useLoaderData`를 사용할 때와 달리, 직접 `promise`에서 데이터를 추출하고, 그 추출한 데이터를 전달해야 한다.

```javascript
async function loadEvents() {
  const response = await fetch('http://localhost:8080/events');

  if (!response.ok) {
    throw json(
      {
        message: 'Could not fetch events',
      },
      {
        status: 500,
      },
    );
  } else {
    const resData = await response.json();
    return resData.events;
  }
}

export function loader() {
  return defer({
    events: loadEvents(), // 함수 표현으로 실행
  });
}
```

- `events`라는 키에 저장된 `loadEvent` 함수는 `async` 함수이기 때문에 `promise`가 있어야 한다.
- 만약, `promise`가 없다면 로딩을 연기할 것이 아무 것도 없다.
  - 즉, `defer`는 다른 값으로 `resolve`될 값이 있다는 것을 가정한다.
- 반환될 `promise`가 아직 반환되지 않았음에도, 컴포넌트를 로딩하고, 렌더링한다.
- `react-router-dom`에서 제공하는 `Await` 컴포넌트를 이용하여 연기된 값을 렌더링한다.
- `Await`은 `resolve`라는 속성을 갖는데, `resolve`는 연기된 값을 갖는다.
  - `defer`에서 설정한 키는 `promise`를 값으로 갖는데(반환하는데), `Await`의 `resolve`에 전달하려는 것이 이 `defer`에서 나온 `promise`다.
- `Await`의 태그 사이에서 데이터가 도착하면 리액트 라우터가 실행할 동적인 값을 렌더링한다.
- 그리고 마지막으로, `Await` 컴포넌트를 감싸는 특별한 컴포넌트를 추가해야한다.
  - 그 컴포넌트는 **React**가 제공하는 `Suspense` 컴포넌트이다.
- 이 `Suspense` 컴포넌트는 다른 데이터가 도착하는 것을 기다리는 동안 `fallback`을 보여주는 특정한 상황에서 사용가능하다. 


```javascript
function EventsPage() {
  const { events } = useLoaderData();

  return (
    <Suspense fallback={<p style={{ textAlign: 'center' }}>Loading...</p>}>
      <Await resolve={events}>
        {(loadedEvents) => <EventsList events={loadedEvents} />}
      </Await>
    </Suspense>
  );
}
```
