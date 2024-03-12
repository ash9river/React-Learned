# NextJS Deep Dive

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
    - `Link`를 통해, **SPA**를 허용하고, 클라이언트 사이드 자바스크립트 코드로 **UI**를 업데이트한다.
    - 다음으로 이동할 페이지는 서버에 렌더링되기 전 단계이지만, 클라이언트 사이드 자바스크립트 코드로 클라이언트 사이드에 업데이트된다.

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
- [더 많은 파일명들](https://nextjs.org/docs/app/api-reference/file-conventions)

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

## 동적 라우팅

- 동적으로 라우트를 정의할려면 대괄호를 사용하여 중첩 폴더를 추가해야 한다.
- 만약 `blog` 폴더에 게시글 라우트를 만들려면 그 `blog` 폴더에 `[식별자]` 폴더를 만들고, `page.js`를 추가해야 한다.
  - 이 대괄호는 **NextJS**에게 경로 분할을 원하지만, `blog`의 다음 경로의 정확한 값은 모른다는 것이다.
  - 이 임의 식별자 `slug`가 경로에 값이 실릴 때, 정확한 값에 접근할 수 있도록 도와준다.
  
<img height="50%" width="50%" src="https://github.com/ash9river/React-Learned/assets/121378532/7308e780-c9c8-4d78-bcbd-b596c06338cb" />

### 경로 매개변수 추출

- **NextJS**는 `props` 객체를 모든 페이지 컴포넌트에 넘긴다.
- 모든 페이지 컴포넌트에는 특별한 `property`가 있는데, 이를 구조 분해 할당으로 값을 추출할 수 있다.
  - `params`가 그 주인공인데, 동적 라우트에 임의로 넣은 모든 이름이 있는 객체이다.
  - 객체의 키가 동적 라우팅을 만들 때에 사용한 식별자이고, 그 키의 값이 **URL**에 인코딩된 정확한 값이다.

> 동적 라우팅을 통해, 동적으로 데이터베이스에 접근하고, 그 데이터를 가져와서 페이지에 내용을 불러올 수 있다.

```javascript
export default function MealDetailPage({ params }) {
  const { mealSlug } = params;

  return (
    <main>
      <h1 style={{ color: 'white', textAlign: 'center' }}>
        MealDetailPage
      </h1>
      <img src={`/images/${mealSlug}.jpg`} alt={mealSlug} />
    </main>
  );
}
```

## 레이아웃

- **NextJS**에서 레이아웃은 페이지의 포장지와 같다.
- 이론적으로 중첩된 레이아웃도 있을 수도 있고, 하위 페이지에 특화된 레이아웃을 적용할 수도 있다.
  - 하나의 루트 레이아웃에 제한되지 않아도 된다. 
- 만약에 `meals` 폴더에 레이아웃이 있다면, `meals` 관련 페이지에만 적용된다.
  - 또한 이 레이아웃이 루트 레이아웃에 중첩되기 때문에, 루트 레이아웃이 항상 활성화된다.
- 결국, 레이아웃은 페이지와 마찬가지로 리액트 컴포넌트이다.
  - 레이아웃은 `children` 속성을 사용할 수 있다.
- 그러나 레이아웃은 페이지와 마찬가지로, 컴포넌트들을 직접 렌더링하는 것이 아니다.
  - **JSX**에 직접 사용하는 것이 아니라, **NextJS**에서 대신 사용한다.
- 기본적으로 **NextJS**는 레이아웃을, 이 레이아웃을 포함한 모든 페이지와 중첩된 레이아웃으로 감싼다.
  - 이로 하여금 `children`을 통해 중첩 레이아웃 또는 페이지에 접근할 수 있게 된다.  

```javascript
export default function MealsLayout({ children }) {
  return (
    <>
      <p>Meals layout</p>
      {children}
    </>
  );
}
```

### 레이아웃에 커스텀 컴포넌트 추가하기

- 레이아웃에 커스텀 컴포넌트를 불러올 수 있다.
- 그런데 이 커스텀 컴포넌트에서 이미지를 불러올려면, 리액트 프로젝트와 다르게 `src` 속성을 액세흐 해야 한다.
  - 이미지의 경로가 `src` 속성 여하에 저장된 객체가 될 것이기 때문이다. 

```javascript
import Link from 'next/link';

import logo from '@/assets/logo.png';

export default function MainHeader() {
  return (
    <header>
      <Link href="/">
        <img src={logo.src} alt="A plate with food on it" />
        NextLevel Food
      </Link>
    </header>
  );
}
```

- 그리고 단순하게 레이아웃에 추가한다.

<details>
  <summary>코드 보기</summary>

```javascript
import MainHeader from '@/components/main-header';
import './globals.css';

export const metadata = {
  title: 'NextLevel Food',
  description: 'Delicious meals, shared by a food-loving community.',
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <div className="header-background">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320">
            <defs>
              <linearGradient id="gradient" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop
                  offset="0%"
                  style={{ stopColor: '#59453c', stopOpacity: '1' }}
                />
                <stop
                  offset="100%"
                  style={{ stopColor: '#8f3a09', stopOpacity: '1' }}
                />
              </linearGradient>
            </defs>
            <path
              fill="url(#gradient)"
              d="M0,256L48,240C96,224,192,192,288,181.3C384,171,480,181,576,186.7C672,192,768,192,864,181.3C960,171,1056,149,1152,133.3C1248,117,1344,107,1392,101.3L1440,96L1440,0L1392,0C1344,0,1248,0,1152,0C1056,0,960,0,864,0C768,0,672,0,576,0C480,0,384,0,288,0C192,0,96,0,48,0L0,0Z"
            />
          </svg>
        </div>
        <MainHeader />
        {children}
      </body>
    </html>
  );
}
```
</details>

## NextJS 디자인

- **NextJS** 프로젝트를 스타일링할 때는 리액트 프로젝트의 `index.css`처럼 `globals.css`가 있다.
- 이 `globals.css`를 루트 `layout.js` 파일에서 `import` 하면 모든 페이지에서 유효하게 된다.
- 또한, **CSS Module**도 사용 가능하다.
  - 이는 일반적인 스탠다드 **CSS**이지만, `.module.css`로 끝나는 **CSS** 파일을 추가하면, 그 파일로부터 객체를 불러올 수 있다.
  - 그리고 이는 기본 빌드 프로세스 및 개발 서버에 의해 자동으로 생성되며 해당 파일에 정의된 모든 **CSS** 클래스에 대한 접근을 속성으로 제공한다.  

```javascript
import Link from 'next/link';

import logo from '@/assets/logo.png';
import styles from './main-header.module.css';

export default function MainHeader() {
  return (
    <header className={styles.header}>
      <Link className={styles.logo} href="/">
        <img src={logo.src} alt="A plate with food on it" />
        NextLevel Food
      </Link>
      <nav className={styles.nav}>
        <ul>
          <li>
            <Link href="/meals">Browse Meals</Link>
          </li>
          <li>
            <Link href="/community">Foodies Community</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

### Image 컴포넌트를 통한 이미지 최적화

- 리액트 프로젝트에서는 이미지를 `img` 태그를 통해 가져왔다.
- 그러나 **NextJS**에서는 이미지를 출력할 때, 기본 이미지 태그보다 더 좋은 내장 이미지 컴포넌트가 있다.
- `Image` 컴포넌트는 페이지에서 실제로 보이는 경우에만 이미지가 표시되도록 이미지를 지연로딩하여서 구한다.
- 또한, 추가적인 구성 없이 반응형 이미지를 설정하는 프로세스 등을 단순화한다. 
- [더 많은 정보](https://nextjs.org/docs/app/api-reference/components/image)

```javascript
import Link from 'next/link';

import Image from 'next/image';
import logo from '@/assets/logo.png';
import styles from './main-header.module.css';

export default function MainHeader() {
  return (
    <header className={styles.header}>
      <Link className={styles.logo} href="/">
        <Image src={logo} alt="A plate with food on it" />
        NextLevel Food
      </Link>
      <nav className={styles.nav}>
        <ul>
          <li>
            <Link href="/meals">Browse Meals</Link>
          </li>
          <li>
            <Link href="/community">Foodies Community</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

- 이 때, `Image` 컴포넌트의 `src`는 `src`의 속성값만 불러오는 것이 아니라 전체 객체를 불러온다.
  - 이미지를 불러올 때, 최적화된 방법으로 `image` 컴포넌트를 띄울 수 있는 정보를 포함하기 때문이다.
- 이로 하여금, `srcset` 속성을 생성하여 뷰포트와 웹사이트를 방문하는 기기에 따라 크기가 조정된 이미지가 로딩되도록 보장한다.
- 또한, 자동적으로 사용자에 의해 사용되는 브라우저에 가장 알맞는 파일 포맷으로 이미지를 전달한다.
- 이미지가 페이지를 로드할 때, 필요하지 않은 컨텐츠의 변경 또는 깜빡임이 없고, 가능한 빨리 로딩시킬려면 `priority` 속성을 추가한다.

```javascript
<Image src={logo} alt="A plate with food on it" priority />
```

## 리액트 서버 컴포넌트와 클라이언트 컴포넌트

- 모든 바닐라 리액트 앱에서는 기본적으로 클라이언트 컴포넌트를 사용하고 있다.
- **ReactJS**는 순수한 클라이언트 사이드 라이브러리로, 브라우저에서 클라이언트 사이드 코드를 실행한다.
- 그러나, **NextJS**는 풀스택 프레임워크이기 때문에, 코드가 백엔드에서 실행된다.
- **NextJS**의 모든 리액트 컴포넌트들은 페이지, 레이아웃, 또는 기본 컴포넌트인가와는 상관없이 오직 서버에서만 렌더링된다.
- 이로 인해, 이러한 컴포넌트들은 리액트 서버 컴포넌트라 불린다.
  - **NextJS**의 모든 컴포넌트는 서버에서만 렌더링 된다.
- **SPA**일 때, 그 안에서 페이지 이동이 일어나도, 모든 컴포넌트는 백엔드에서의 서버에 렌더링된다.
- 그리고 백엔드가 클라이언트에게 완성된 **HTML** 코드를 보낸다.

> **NextJS**는 서버 컴포넌트를 가질 수 있고, 이는 **NextJS**의 주요한 장점이다. <br/>
> 서버 컴포넌트를 사용함으로써, 클라이언트 사이드에서 다운로드하는 자바스크립트 코드가 줄어들어서 웹사이트의 성능을 향상시킨다.

- 그러나, **NextJS** 프로젝트에서 클라이언트 컴포넌트도 만들 수 있다.
- 그 컴포넌트들은 서버에서 사전 렌더링이 되는 것들이지만, 잠재적으로 클라이언트에서 렌더링될 수 있다.
- 이러한 컴포넌트들은 클라이언트에서 무조건 렌더링되야 하는데, 클라이언트에서만 사용가능한 코드나 기능을 포함하기 때문이다.(`useState`, `useEffect`, `eventHandler` 등)
- 기본적으로 **NextJS**에서는 모든 컴포넌트가 서버 컴포넌트이기 때문에, 클라이언트 컴포넌트를 만들고 싶다면 `use client`라고 지시해야 한다.
- 클라이언트 컴포넌트 파일의 최상단에 `'use client';`를 작성하면, 클라이언트 컴포넌트가 만들어진다.

```javascript
'use client';

import { useEffect, useState } from 'react';
import Image from 'next/image';

import burgerImg from '@/assets/burger.jpg';
import curryImg from '@/assets/curry.jpg';
import dumplingsImg from '@/assets/dumplings.jpg';
import macncheeseImg from '@/assets/macncheese.jpg';
import pizzaImg from '@/assets/pizza.jpg';
import schnitzelImg from '@/assets/schnitzel.jpg';
import tomatoSaladImg from '@/assets/tomato-salad.jpg';
import classes from './image-slideshow.module.css';

const images = [
  { image: burgerImg, alt: 'A delicious, juicy burger' },
  { image: curryImg, alt: 'A delicious, spicy curry' },
  { image: dumplingsImg, alt: 'Steamed dumplings' },
  { image: macncheeseImg, alt: 'Mac and cheese' },
  { image: pizzaImg, alt: 'A delicious pizza' },
  { image: schnitzelImg, alt: 'A delicious schnitzel' },
  { image: tomatoSaladImg, alt: 'A delicious tomato salad' },
];

export default function ImageSlideshow() {
  const [currentImageIndex, setCurrentImageIndex] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCurrentImageIndex((prevIndex) => (prevIndex < images.length - 1 ? prevIndex + 1 : 0));
    }, 5000);

    return () => clearInterval(interval);
  }, []);

  return (
    <div className={classes.slideshow}>
      {images.map((image, index) => (
        <Image
          key={image.alt}
          src={image.image}
          className={index === currentImageIndex ? classes.active : ''}
          alt={image.alt}
        />
      ))}
    </div>
  );
}
```

## 클라이언트 컴포넌트의 효율적인 사용

- 경로에 따라 네비바 활성화 표시를 위해서 경로 이름을 가져와야 한다.
- `usePathName`을 통해서 경로 이름을 가져오고, `startWith()`를 사용해서 활성화를 알아낸다.
- 그러나 이런 기능들은 서버 컴포넌트에서 작동되지 않아서, **NextJS**에서 이를 자동으로 알려주고, `use client`를 추가하라 지시한다.

```javascript
'use client';

import Link from 'next/link';
import Image from 'next/image';
import { usePathname } from 'next/navigation';

import MainHeaderBackground from './main-header-background';
import logo from '@/assets/logo.png';
import styles from './main-header.module.css';

export default function MainHeader() {
  const path = usePathname();

  return (
    <>
      <MainHeaderBackground />
      <header className={styles.header}>
        <Link className={styles.logo} href="/">
          <Image src={logo} alt="A plate with food on it" priority />
          NextLevel Food
        </Link>
        <nav className={styles.nav}>
          <ul>
            <li>
              <Link
                href="/meals"
                className={path.startsWith('/meals') ? styles.active : undefined}
              >
                Browse Meals
              </Link>
            </li>
            <li>
              <Link
                href="/community"
                className={path === '/community' ? styles.active : undefined}
              >
                Foodies Community
              </Link>
            </li>
          </ul>
        </nav>
      </header>
    </>
  );
}
```

### 클라이언트 컴포넌트 분리

> ❗ 클라이언트 컴포넌트를 사용하려면 가능한 컴포넌트 트리의 최하단에서 사용하는 것이 보편적이다. <br/>
> 이를 통해 필요한 컴포넌트만 클라이언트 컴포넌트로 변환하여, 대부분의 컴포넌트가 서버 컴포넌트로 유지되면서 서버 컴포넌트의 이점을 가질 수 있다.

<details>
  <summary>코드 보기</summary>

```javascript
'use client';

import Link from 'next/link';
import { usePathname } from 'next/navigation';

import styles from './nav-link.module.css';

export default function NavLink({ href, children }) {
  const path = usePathname();

  return (
    <Link href={href} className={path.startsWith(href) ? styles.active : undefined}>
      {children}
    </Link>
  );
}
```
```javascript
import Link from 'next/link';
import Image from 'next/image';

import MainHeaderBackground from './main-header-background';
import logo from '@/assets/logo.png';
import styles from './main-header.module.css';
import NavLink from './nav-link';

export default function MainHeader() {
  return (
    <>
      <MainHeaderBackground />
      <header className={styles.header}>
        <Link className={styles.logo} href="/">
          <Image src={logo} alt="A plate with food on it" priority />
          NextLevel Food
        </Link>
        <nav className={styles.nav}>
          <ul>
            <li>
              <NavLink href="/meals">
                Browse Meals
              </NavLink>
            </li>
            <li>
              <NavLink href="/community">
                Foodies Community
              </NavLink>
            </li>
          </ul>
        </nav>
      </header>
    </>
  );
}
```
</details>

## NextJS 및 풀스택 기능을 활용한 데이터 불러오기

- **NextJS**에서는 기본적으로 모든 컴포넌트들이 서버에서만 실행되는 서버 컴포넌트이기 때문에, 데이터 요청을 보내지 않아도 된다.
- 대신, 이 컴포넌트가 기본값으로 서버에서만 실행되기 때문에 바로 데이터베이스와 연결할 수 있다.
- 리액트와는 다르게, 서버 컴포넌트 함수들이 `async` 함수로 바뀔 수 있기 때문에, `promise`와 `await`을 사용할 수 있다.

```javascript
import Link from 'next/link';

import { getMeals } from '@/lib/meals';
import MealsGrid from '@/components/meals/meals-grid';
import styles from './page.module.css';

export default async function MealsPage() {
  const meals = await getMeals();

  return (
    <>
      <header className={styles.header}>
        <h1>
          Delicious meals, create
          <span className={styles.highlight}>by you</span>
        </h1>
        <p>Choose your favorite recipe and cook it yourself. It is easy and fun!</p>
        <p className={styles.cta}>
          <Link href="/meals/share">
            Share Your Favorite Recipe
          </Link>
        </p>
      </header>
      <main className={styles.main}>
        <MealsGrid meals={meals} />
      </main>
    </>
  );
}
```

### 로딩 컴포넌트 추가

- 데이터를 데이터베이스에서 불러올려면 로딩이 필요하다.
- 그러나, 로딩은 한 번만 하면 되는데, **NextJS**에서는 안보이는 곳에서 굉장히 공격적인 캐싱을 하기 때문이다.
  - **NextJS**는 한 번 들어간 페이지들을 해당 페이지의 데이터를 포함해서 모두 캐시한다. 
- 페이지를 새로고침할 때만, 페이지가 재구성된다.
- `loading.js` 파일을 원하는 페이지 폴더에 추가하여, 데이터를 불러올 때, 로딩 컴포넌트가 보이도록 만든다.
  - 이 `loading.js` 파일은 `page.js`나 `layout.js` 파일처럼 `reserved file name`이다. 

<img height="75%" width="75%" src="https://github.com/ash9river/React-Learned/assets/121378532/35114a23-dde3-4221-8cde-436c368d0976" />

## Suspense & Streamed Response를 활용환 세분화 로딩

- 리액트에서 한 것처럼, `Susepnse`를 리액트에서 `import`한다.
- 그리고 데이터를 가져오는 코드를 다른 컴포넌트로 분할시키고, `Suspense`로 감싼다.
- `loading.js` 파일을 생성하지 않고, 대신 `fallback` 속성을 이용한다.

```javascript
import Link from 'next/link';

import { Suspense } from 'react';
import { getMeals } from '@/lib/meals';
import MealsGrid from '@/components/meals/meals-grid';
import styles from './page.module.css';
import MealsLoadingPage from './loading-out';

async function Meals() {
  const meals = await getMeals();

  return <MealsGrid meals={meals} />;
}

export default function MealsPage() {
  return (
    <>
      <header className={styles.header}>
        <h1>
          Delicious meals, create
          <span className={styles.highlight}> by you</span>
        </h1>
        <p>Choose your favorite recipe and cook it yourself. It is easy and fun!</p>
        <p className={styles.cta}>
          <Link href="/meals/share">
            Share Your Favorite Recipe
          </Link>
        </p>
      </header>
      <main className={styles.main}>
        <Suspense fallback={<MealsLoadingPage />}>
          <Meals />
        </Suspense>
      </main>
    </>
  );
}
```

## 오류 처리

- `error.js` 파일을 추가하여 `reserved file name`을 활용할 수 있다. 
- 중첩 및 세분화를 이용하여, 아무 페이지의 아무 곳에서나 일어나는 에러를 잡을 수 있다.
- `error.js` 파일에서 컴포넌트를 구성할 때, **NextJS**는 해당 컴포넌트에 `props`를 자동으로 전달한다.
  - 전달되는 `props`는 `error`인데, 오류에 대한 정보를 가지고 있다.
  - 그러나, 굳이 `error`를 활용하지 않아도 된다.
- `error.js` 파일은 무조건 클라이언트 컴포넌트이어야 된다.
  - **NextJS**에서 페이지가 서버에서 렌더링 된 후에, 기본적으로 클라이언트 사이드에서 발생하는 오류를 포함한, 해당 컴포넌트의 모든 오류를 잡을 수 있도록 보장하기 때문이다.

```javascript
'use client';

export default function Error({ error }) {
  return (
    <main className="error">
      <h1>An error occured!</h1>
      <p>Failed to fetch meal data, please try again later.</p>
    </main>
  );
}
```

### 404 Not Found

- 앱 라우터에서 사용가능한 기능으로, `app` 디렉토리에 간단하게 `not-found.js` 파일을 생성하면 된다.
- 파일을 만들지 않아도 자동으로 생성되지만, 이 파일을 만듬으로써 간단히 커스텀할 수 있다. 
- [공식 문서](https://nextjs.org/docs/app/api-reference/file-conventions/not-found)

```javascript
export default function NotFound() {
  return (
    <main className="not-found">
      <h1>Not found</h1>
      <p>Unfortunately, we could not find the requested page or resource.</p>
    </main>
  );
}
```

### 존재하지 않는 경로 접근시

- 존재하지 않는 경로로 접근시, `notFound()`를 이용하면 `not-found` 페이지로 쉽게 이동시킬 수 있다.

```javascript
if (!data) {
  notFound();
}
```

## 커스텀 이미지 피커

- `onClick`과 같은 이벤트 핸들러를 이용하기 위해서 클라이언트 컴포넌트로 정의하였다.
- `useRef`를 사용하여, `current` 속성의 `click`을 이용하여 버튼이 클릭되면, `input`을 클릭한 것과 같이 이용되었다.
- 자바스크립트의 `FileReader` 클래스를 활용한다.
  - `FileReader`  객체에 있는 `load` 속성에 값을 지정하는 것으로 생성되는 `DataURL`을 활용한다.
  - 만약, 여러 개의 사진을 삽입하고 싶으면 `input` 태그에 `multiple` 속성을 추가한다. 

```javascript
'use client';

import { useState, useRef } from 'react';
import Image from 'next/image';
import styles from './image-picker.module.css';

export default function ImagePicker({ label, name }) {
  const [pickedImage, setPickedImage] = useState(null);

  const imageInputRef = useRef();

  function handleClick() {
    imageInputRef.current.click();
  }

  function handleImageChange(event) {
    const file = event.target.files[0];

    if (!file) {
      setPickedImage(null);
      return;
    }

    const fileReader = new FileReader();

    fileReader.readAsDataURL(file);

    fileReader.onload = () => {
      setPickedImage(fileReader.result);
    };
  }

  return (
    <div className={styles.picker}>
      <label htmlFor={name}>
        {label}
      </label>
      <div className={styles.controls}>
        <div className={styles.preview}>
          {pickedImage
            ? (
              <Image
                src={pickedImage}
                alt="The image selected by the user."
                fill
              />
            )
            : <p>No image picked yet.</p>}
        </div>
        <input
          className={styles.input}
          type="file"
          id={name}
          accept="image/png, image/jpeg"
          name={name}
          ref={imageInputRef}
          onChange={(event) => handleImageChange(event)}
          required
        />
        <button className={styles.button} type="button" onClick={handleClick}>Pick an Image</button>
      </div>
    </div>
  );
}
```

## form 제출

- 리액트에서는 `onSubmit` 속성을 이용하여, 백엔드로 `form`을 제출하였다.
- 그러나 **NextJS**는 백엔드와 프론트엔드가 공존하는 풀스택 애플리케이션이기 때문에, `onSubmit`을 이용할 필요가 없다.
- `form`이 있는 컴포넌트에 함수를 만들고, 그 함수에 특별한 지시어인 `use server`를 추가한다.
  - `use server`를 사용하면, `Server Action`을 생성한다.
  - 이 기능은 오직 서버에서만 실행될 수 있게 보장해주는 기능이다.
- `Server Action`을 가지고, `form`의 `action` 속성에 할당할 수 있다.
  - 이로 하여금   `form`이 제출되면 **NextJS**가 자동으로 요청을 생성하여 웹사이트를 제공하는 **NextJS** 서버로 보낸다.
  - 그 해당 함수가 서버 사이드에서 실행되고, `form`의 제출을 제어할 수 있게 된다.
  - 또한, 이 함수는 자동적으로 제출된 `formData`를 받게 되는데, `form`의 `input` 태그들이 모인 데이터들이 `formData` 객체로 수집된다.
    - 이는 자바스크립트에서 제공하는 `formData` 클래스다.  
- 그리고, `Server Action`이 실행되면 그 결과가 데이터 베이스에 저장되도록 해야 한다.
- 그러나 이 `Server Action`은 클라이언트 컴포넌트에서 사용할 수 없기 때문에, 보통 다른 파일로 분리하여 사용한다.
  - `lib` 폴더에 배치할 수도 있다.
- 아웃소싱하는 파일의 맨 위에서 `use server`를 추가하면, 해당 파일에서 정의하는 모든 함수가 `Server Action`이 된다.

```javascript
'use server';

export default async function shareMeal(formData) {
  'use server';

  const meal = {
    title: formData.get('title'),
    summary: formData.get('summary'),
    instructions: formData.get('instructions'),
    image: formData.get('image'),
    creator: formData.get('name'),
    creator_email: formData.get('email'),
  };
}
```

- 같은 파일 안에서 클라이언트 사이드 코드와 서버 사이드 코드를 정의하는 것에 대해서, **NextJS**에서 사용되는 빌드 프로세스는 기본적으로 이를 명확하게 분리하는 것이 불가능하다.
  - 이는 서버 사이드 코드가 클라이언트 사이드에 위치하여 보안 문제나 다른 문제를 야기시킬 가능성이 생긴다.
- 이러한 이유로 같은 파일에 클라이언트 사이드와 서버 사이드의 코드를 혼용하지 않고, `Server Action`을 다른 파일로 분리하여서 사용함으로써 해결할 수 있다.

## XSS 보호를 위한 슬러그 생성 및 유저 입력 무결성 검사

- **XSS**는 크로스 사이트 스크립팅으로, 웹에 악성 스크립트를 삽입하여 사용자의 정보를 탈취하거나, 비정상적인 기능을 수행하게 만들 수 있다.
  - 주로 다른 웹사이트와 정보를 교환하는 식으로 작동하기 때문에 사이트 간 스크립팅이라고도 한다.
- `xss` 패키지의 도움으로 **XSS** 공격을 막을 수 있다.

```
yarn add xss
```
- 오타를 조심하자, 자꾸 `image`를 `imgae`라고 써서 버그 수정하는데 시간을 많이 들였다.

<details>
  <summary>코드 보기</summary>

```javascript
/* eslint-disable no-param-reassign */
import fs from 'node:fs';

import sql from 'better-sqlite3';
import slugify from 'slugify';
import xss from 'xss';

const db = sql('meals.db');

export async function getMeals() {
  await new Promise((resolve) => setTimeout(resolve, 2000));
  return db.prepare('SELECT * FROM meals').all();
}

export function getMeal(slug) {
  return db.prepare('SELECT * FROM meals WHERE slug = ?').get(slug);
}

export async function saveMeal(meal) {
  meal.slug = slugify(meal.title, { lower: true });
  meal.instructions = xss(meal.instructions);

  const extension = meal.image.name.split('.').pop();
  const fileName = `${meal.slug}.${extension}`;

  const stream = fs.createWriteStream(`public/images/${fileName}`);
  const bufferedImage = await meal.image.arrayBuffer();

  stream.write(Buffer.from(bufferedImage), (error) => {
    if (error) {
      throw new Error('Saving image faileds');
    }
  });

  meal.image = `/images/${fileName}`;

  db.prepare(`
    INSERT INTO meals
      (title, summary, instructions, creator, creator_email, image, slug)
    VALUES (
      @title,
      @summary,
      @instructions,
      @creator,
      @creator_email,
      @image,
      @slug
    )
  `).run(meal);
}
```
</details>

## useFormStatus를 이용한 폼 제출 상태 관리

- 사용자 경험을 향상시키기 위해서, 폼이 제출되었으면 요청이 진행중인 것을 알려줘야 한다.
- `react-dom`에서 제공하는 `useFormStatus`를 사용한다.
  - `pending` 속성을 통해 폼 제출을 하고 있는지 파악할 수 있다.
- 클라이언트 사이드이므로, 따로 컴포넌트를 분리하여 사용한다.

```javascript
'use client';

import { useFormStatus } from 'react-dom';

export default function MealsFormSubmit() {
  const { pending } = useFormStatus();

  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Submiting...' : 'Share Meal'}
    </button>
  );
}
```

### useFormState

- `react-dom`에서 제공하는 `useFormState`는 리액트의 `useState`와 비슷하게 동작한다.
  - 이는, `Server Action`을 통해 제출되는 `form`을 사용하는 페이지나 컴포넌트의 `state`를 관리하기 때문이다.
- `useFormState`는 두 가지의 인수를 필요로 한다.
  1. `form`이 제출될 때 동작하는 실제 `Server Action`
  2. 컴포넌트의 초기 `state`
- `useState`와 비슷하게 선언하나, `[ state, formAction ]`으로 선언한다.
- 그리고, `state`를 인수로 넣은 `Server Action`의 실행과 응답에 따라서 변경된다.
- 이로 하여금 `useFormState`가 일종의 미들웨어와 유사한 동작을 한다.

```javascript
const [state, formAction] = useFormState(shareMeal, { message: null });

return <form className={classes.form} action={formAction} />
```

- `useFormState`를 사용할 때는, `Server Action`이 다른 형태이어야 한다.
- `Server Action`이 두 가지 인수를 갖게 되는데, 첫 번째 인수로 이전 상태인 `prevState`를 갖고, 두 번째 인수는 이전과 같이 `formDate`를 가진다.
- 또한, `useFormState`가 클라이언트를 수정하려고 하기 때문에, 클라이언트로 실행되어야 한다.

<details>
  <summary>코드 보기</summary>

```javascript
'use client';

import { useFormState } from 'react-dom';

import ImagePicker from '@/components/meals/image-picker';
import classes from './page.module.css';
import shareMeal from '@/lib/action';
import MealsFormSubmit from '@/components/meals/meals-form-submit';

export default function ShareMealPage() {
  const [state, formAction] = useFormState(shareMeal, { message: null });

  return (
    <>
      <header className={classes.header}>
        <h1>
          Share your
          {' '}
          <span className={classes.highlight}>favorite meal</span>
        </h1>
        <p>Or any other meal you feel needs sharing!</p>
      </header>
      <main className={classes.main}>
        <form className={classes.form} action={formAction}>
          <div className={classes.row}>
            <p>
              <label htmlFor="name">Your name</label>
              <input type="text" id="name" name="name" required />
            </p>
            <p>
              <label htmlFor="email">Your email</label>
              <input type="email" id="email" name="email" required />
            </p>
          </div>
          <p>
            <label htmlFor="title">Title</label>
            <input type="text" id="title" name="title" required />
          </p>
          <p>
            <label htmlFor="summary">Short Summary</label>
            <input type="text" id="summary" name="summary" required />
          </p>
          <p>
            <label htmlFor="instructions">Instructions</label>
            <textarea
              id="instructions"
              name="instructions"
              rows="10"
              required
            />
          </p>
          <ImagePicker label="Your image" name="image" />
          {state.message ? <p>{state.message}</p> : null}
          <p className={classes.actions}>
            <MealsFormSubmit />
          </p>
        </form>
      </main>
    </>
  );
}
```

- 바뀐 `Server Action`

```javascript
'use server';

import { redirect } from 'next/navigation';
import { saveMeal } from './meals';

function isInvalidText(text) {
  return !text || text.trim() === '';
}

export default async function shareMeal(prevState, formData) {
  const meal = {
    title: formData.get('title'),
    summary: formData.get('summary'),
    instructions: formData.get('instructions'),
    image: formData.get('image'),
    creator: formData.get('name'),
    creator_email: formData.get('email'),
  };

  if (isInvalidText(meal.title)
    || isInvalidText(meal.summary)
    || isInvalidText(meal.instructions)
    || isInvalidText(meal.creator)
    || isInvalidText(meal.creator_email)
    || !meal.creator_email.includes('@')
    || !meal.image
    || meal.image.size === 0) {
    return {
      message: 'Invalid input.',
    };
  }
  await saveMeal(meal);
  redirect('/meals');
}
```
</details>

## NextJS 캐싱






