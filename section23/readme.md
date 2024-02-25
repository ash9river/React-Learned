# Deploying React Apps

## 배포 과정

- 배포할 때, 거치는 과정은 다음과 같다.
    1. 코드를 작성하고 테스트
    2. 최적화가 가능한지 파악(지연 로딩이 가능한가도 파악)
    3. 프로덕션용 앱 빌드(코드 패키지 최적화)
 
## 지연 로딩

- 리액트의 최적화 테크닉 중에서 `React.memo()`를 사용할 수도 있지만, 필요한 코드만 로딩함으로써 최적화를 이끌어낼 수도 있다.
- `지연 로딩(Lazy Loading)`은 특정 코드를 필요할 때만 로딩하는 방법이다.

<br/>

- 만약 프로젝트가 지연 로딩 없이 빌드되고 최종 사용자에게 전달될려면, `import` 문들에 포함된 코드들이 최종 사용자들에게 전달되기 전에 모든 코드가 불러와져야 한다.
- 기초적인 앱에서는 문제가 되지 않지만, 복잡한 앱인 경우, 수많은 라우트와 컴포넌트가 문제될 수 있다.
  - 또한 첫 페이지의 로딩도 느려질 수 있다.(**CSR**의 단점) 
- 이는 사용자 경험이 나빠지는 결과를 초래한다.

<br/>

- **지연 로딩의 핵심 아이디어는 특정 컴포넌트를 나중에 불러오는 것이다.**
    - 미리 불러오는 대신, 필요할 때에 불러오는 것이다.
- 지연 로딩을 적용하려면, `import` 문을 삭제하고, 필요할 때만 로딩되도록 해야 한다.

### loader 지연 로딩 적용

- 이미 존재하는 `loader` 또한 삭제하고, `loader`에 함수를 할당한다.
- 이 때, 할당하는 함수에 `import()`를 이용하여 동적으로 필요한 `import()`를 이용한다.
    - 이 `import()` 함수는 `promise`를 반환한다.
- `async`와 `await`을 이용할 수도 있지만, `then()`을 이용할 수도 있다. 

```javascript
loader: () => import('./pages/Blog').then((module) => module.loader()),
```

- `loader` 에서 로딩된 `module(모듈)`을 받아서 그 `module`에 `loader` 함수를 적용한다.
- 그 `loader` 함수는 `Promise<Response>`를 반환하므로, 이 전체 `loader` 함수는 마지막의 `module.loader()`에 의해 `promise`를 반환한다.
- 결국 이 `import` 문은 이 `loader`가 호출될 때만 실행된다.
    - 블로그 페이지를 방문해야한, 블로그 파일이 `import`되고, 이에 의해 파일의 `loader` 함수가 실행된다.  

### 페이지 컴포넌트에 지역 로딩 적용

- 컴포넌트를 함수로 작성해서 반환하지면서도 `import`를 이용하면 유효하지 않는 컴포넌트 함수가 만들어진다.

```javascript
const BlogPage = () => import('./pages/Blog');
```

- 이 함수는 `promise`를 반환하기 때문에 제대로 작동하지 않는다.
- **React**에서는 이러한 문제를 해결하기 위해서 특수한 감싸는 함수를 제공한다.
    - **React**에서 `import`하는 `lazy` 함수이다. 
- `lazy`는 실행될 때, 동적으로 `import`하는 함수를 인자로 받는다.
- 또한, 코드를 다운로드하는데 시간이 소요되기 때문에 **React**에서 제공하는 `Suspense` 컴포넌트로 감싸야 한다.
    - 이 `Suspense` 컴포넌트는 다른 컴포넌트에서 호출하여서, 실제로 콘텐츠를 렌더링하기 전에 콘텐츠의 로딩을 기다리는 데에 사용할 수 있다.
    - 또한, `fallback` 속성으로 추가해서 쉽게 구현할 수 있다.

```javascript
const BlogPage = lazy(() => import('./pages/Blog'));
```

```javascript
element: (
  <Suspense fallback={<p>Loading...</p>}>
    <BlogPage />
  </Suspense>
),
```

### 전후 비교

- 전

```javascript
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import BlogPage, { loader as postsLoader } from './pages/Blog';
import HomePage from './pages/Home';
import PostPage, { loader as postLoader } from './pages/Post';
import RootLayout from './pages/Root';

const router = createBrowserRouter([
  {
    path: '/',
    element: <RootLayout />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: 'posts',
        children: [
          { index: true, element: <BlogPage />, loader: postsLoader },
          { path: ':id', element: <PostPage />, loader: postLoader },
        ],
      },
    ],
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

- 후

```javascript
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import { lazy, Suspense } from 'react';
import RootLayout from './pages/Root';

const HomePage = lazy(() => import('./pages/Home'));
const BlogPage = lazy(() => import('./pages/Blog'));
const PostPage = lazy(() => import('./pages/Post'));

const router = createBrowserRouter([
  {
    path: '/',
    element: <RootLayout />,
    children: [
      {
        index: true,
        element: (
          <Suspense>
            <HomePage />
          </Suspense>
        ),
      },
      {
        path: 'posts',
        children: [
          {
            index: true,
            element: (
              <Suspense fallback={<p>Loading...</p>}>
                <BlogPage />
              </Suspense>
            ),
            loader: () =>
              import('./pages/Blog').then((module) => module.loader()),
          },
          {
            path: ':id',
            element: (
              <Suspense>
                <PostPage />
              </Suspense>
            ),
            loader: () =>
              import('./pages/Post').then((module) => module.loader()),
          },
        ],
      },
    ],
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

- 라우트 수준에서만 분할하였다.
- 과도한 분할은 **HTTP** 요청을 증가시키므로 오히려 성능 하락의 원인이 될 수 있다.

## 프로덕션용 코드 빌드

- 다음의 명령어를 입력하여 최적화된 프로덕션 빌드를 생성하고, `build` 폴더의 내용물을 서버에 업로드하면 된다.

```
npm run build
```

또는

```
yarn build
```

- `build` 폴더의 `static` 폴더 안에는 최적화된 자바스크립트 파일이 들어있다.
    - 동적으로 지연 로딩되는 코드도 있고, 초기에 다운로드되는 `main` 코드도 있다.

## 서버에 업로드 및 배포

- 웹사이트를 배포하는 데에 있어 중요한 점은 리액트 **SPA**는 정적 웹사이트라는 점이다.
    - `A React SPA is a "Static Website"`
- **HTML**, **CSS**, 자바스크립트 파일과 이미지 파일만으로 구성되고, 서버에서 실행되어야 하는 코드는 없다.
- 모든 코드는 브라우저에서 파싱되고, 웹사이트의 방문자 컴퓨터에서 실행된다.
    - 따라서, 정적 웹사이트 호스트를 사용한다.(서버에서 코드를 실행하는 호스팅 제공자를 사용하지 않아도 된다.)
      

















