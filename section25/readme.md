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

1. `<a>` 태그 활용
    - `<a>` 태그를 사용하면 클라이언트 사이드를 통해 이동하지 않고, 서버 사이드를 통해 페이지가 이동한다.
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
      <Link>
        <a href="/about">About Us</a>
      </Link>
    </main>
  );
}
```

- 이 `Link`를 통해 단일 페이지 애플리케이션에 머물수 있도록 보장해준다.






