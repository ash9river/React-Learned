# Next.js Deep Dive

## 파일 기반 라우팅과 리액트 서버 컴포넌트

- `app` 폴더는 **NextJS** 프로젝트에서 가장 중요한 폴더이다.
  - 이 `app` 폴더에서 전반적인 웹 사이트에 넣고 싶은 페이지를 설정할 수 있다.
- `app`폴더의 `page.js` 파일을 통해 만드는데, 이 `page.js` 파일은 `layout.js` 파일과 마찬가지로 보호된 파일명이다.
- 이 `page.js` 파일은 **NextJS**에게 페이지를 렌더링해야 한다고 말해준다.
  -  이 컴포넌트의 특별한 점은 서버 컴포넌트라는 것이다.
 
### 서버 컴포넌트

- **React**만으로는 만들 수 없고, **NextJS**에서 지원하는 형식이다.
- 표면적으로는 일반적인 컴포넌트이지만, **NextJS**가 이 컴포넌트가 서버에 렌더링되고, 컴포넌트 함수가 서버에 실행되는 것을 보장한다.
- `Terminal`이 서버를 운영하고 있어서, 서버 컴포넌트에 `console.log()`를 찍어도, 클라이언트가 아니라 서버에 보인다.

> 일반적인 리액트 컴포넌트이지만, **NextJS**에서는 서버 컴포넌트로 취급받고 서버에서 실행된다. <br/>
> 그 후, 반환된 **JSX** 코드가 브라우저에 전달되어 **HTML**로 렌더링된다.

- [더 자세한 설명](https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)

### 파일 시스템을 통한 추가 경로 추가하기

- 만약에 `/about`이라는 페이지를 접속하고 싶다면, 리액트에서는 단순히 `react-router-dom`을 활용한다.
- 그러나 **NextJS**에서는 `app` 디렉토리에 라우트로 취급하는 새로운 경로를 만들 수 있는데, 단순히 폴더를 만들면 된다.
  - `/about` 라우트를 지원하고 싶으면, `app` 디렉토리에 `about`이라는 폴더를 만들면 된다. 
  - 그리고 그 폴더에 `page.js`라는 파일을 추가하면 `/about` 라우트가 정상적으로 렌더링 될 수 있다.


#### 라우트 폴더에 파일 이름 작명법

<img height="75%" width="75%" src="https://github.com/ash9river/React-Learned/assets/121378532/7c8318ae-72a7-4147-88a8-c4b65067798c" />

### 페이지 이동

- **NextJS**에서 페이지 이동을 할려면 여러가지 방법이 있다.

1. `a` 태그 활용
    - `a` 태그를 사용하면 클라이언트 사이드를 통해 이동하지 않고, 서버 사이드를 통해 페이지가 이동한다.
    - 백엔드에서 새로운 페이지를 다운받고, 현재 페이지에서 벗어나 새로운 페이지로 이동한다.(**SPA**가 아님)

```javascript
import Link from 'next/link';

export default function Home() {
  return (
    <main>
      <img src="/logo.png" alt="A server surrounded by magic sparkles." />
      <h1>Welcome to this NextJS Course!</h1>
      <p>🔥 Let&apos;s get started! 🔥</p>
      <p>
        <a href="/about">About Us</a>
      </p>
    </main>
  );
}
```

2. `Link` 사용
    - `Link`를 통해, **SPA**를 허용하고, 클라이언트 측 자바스크립트 코드로 **UI**를 업데이트한다.
    - 다음으로 이동할 페이지는 서버에 렌더링되기 전 단계이지만, 클라이언트 측 자바스크립트 코드로 클라이언트 사이드에 업데이트된다.

```javascript
import Link from 'next/link';

export default function Home() {
  return (
    <main>
      <img src="/logo.png" alt="A server surrounded by magic sparkles." />
      <h1>Welcome to this NextJS Course!</h1>
      <p>🔥 Let&apos;s get started! 🔥</p>
      <p>
        <Link href="/about">
          About Us
        </Link>
      </p>
    </main>
  );
}
```

- 이 `Link`를 통해 단일 페이지 애플리케이션에 머물수 있도록 보장해준다.

## 페이지 및 레이아웃

- `page.js`가 페이지의 내용을 정의한다면, `layout.js`는 하나 또는 그 이상의 페이지를 감싸는 껍데기를 정의한다.
- 모든 **NextJS** 프로젝트에는 최소한 하나의 루트 `layout.js`가 필요하다.
  - 즁첩된 `layout.js` 파일도 있을 수 있다.
- 레이아웃 파일은 리액트에서 모든 컴포넌트가 사용할 수 있는 표준 `children` 속성을 사용해서, `body` 태그 사이에 추가한다.
  - 즉, 이 컴포넌트는 실제로 **HTML**과 `body` 태그를 렌더링한다. 

```javascript
import './globals.css';

export const metadata = {
  title: 'NextJS Course App',
  description: 'Your first NextJS app!',
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

- 리액트 컴포넌트에서는 자주 사용하지 않는 요소이지만, **NextJS** 프로젝트의 루트 레이아웃은 웹사이트의 일반적인 **HTML**의 뼈대를 잡기 위해 필수적이다.
- `metadata`라는 특별한 변수를 불러옴으로써 `head`에 들어가는 모든 내용을 설정하거나, **NextJS**의 이면에서 자동으로 `head`를 설정한다.

## reserved file name

- `app` 디렉토리에는 `global.css` 파일과 `icon.png`가 있다.
- 먼저 `icon.png`는 특별한 `icon`이란 이름을 사용했으므로, `app` 디렉토리에 있는 `icon`은 `favicon`이 된다.
- `global.css`는 `layout.js` 파일에 `import`되는데, 이로 인해 로딩된 모든 페이지에서 사용가능하다.

## 컴포넌트 분할

- `app` 디렉토리에서 `components` 폴더를 만들고, 컴포넌트를 하나 만들고 그 컴포넌트를 `page.js`에서 `import`하는 방식으로 사용할 수 있다.
  - [`components` 폴더를 `app` 디렉토리 바깥에 저장하는 쪽을 더 선호할 수도 있다.](https://nextjs.org/docs/app/building-your-application/routing/colocation)
- 그러나 `components`라는 폴더를 만들었다고 해서 `/components`라는 경로에 접속할 수는 없다.
  - 이는 `components`라는 폴더에 `page.js` 파일이 없기 때문이다. 

<img height="75%" width="75%" src="https://github.com/ash9river/React-Learned/assets/121378532/1d7bc378-231f-4292-a0a0-d6b154466986" />

### import

- `import` 경로에 `@`을 이용하여, `app` 폴더 바깥에 있는 `root` 프로젝트 폴더를 지정할 수 있다.
- `jsconfig.json`에서 설정을 하면, 이 기능을 사용할 수 있다.

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

- `root` 프로젝트 폴더를 `@`를 통해 추적할 수 있다.

<img height="75%" width="75%" src="https://github.com/ash9river/React-Learned/assets/121378532/56796ea1-11e5-41c9-90e2-b0410eb97f85" />

```javascript
import Link from 'next/link';
import Header from '@/components/header';

export default function Home() {
  return (
    <main>
      <Header />
      <p>🔥 Let&apos;s get started! 🔥</p>
      <p>
        <Link href="/about">
          About Us
        </Link>
      </p>
    </main>
  );
}
````