# User Authentication

## 사용자 인증의 원리

- 사용자가 로그인 요청을 백엔드에 보낸다.
- 그러면 서버는 어떻게 응답을 해야 하는가?
  - 단순히 응답에 `yes`라고 대답하면 안된다. 가짜 응답으로 정보를 탈취할 수 있기 때문이다.
- 보통 다음에 저술될 두 가지 방법을 사용한다.

### 서버 사이드 세션(Server-side Sessions)

- 프론트엔드와 백엔드가 분리되지 않은 풀스택 앱에서 자주 사용하는 대중적인 방법이다.
- 클라이언트가 로그인하고 인증된, 서버에 고유 식별자를 저장한다.
- 기본적으로 `yes`를 서버에 저장하고, 식별자를 이용해, 그 대답을 특정 클라이언트와 연결한다.
- 그리고 그 식별자를 다시 클라이언트에 보낸다.
- 그 이후, 클라이언트가 요청을 통해 보호되어 있는 리소스에 접근할 때, 해당 식별자를 전송한다.
- 서버는 이 클라이언트에 그 리소스에 접근할 권한이 있는지 이 식별자를 통해 확인한다.

> 이 방법은 백엔드가 클라이언트 관련 정보를 반드시 저장해야 하기 때문에, 백엔드와 프론트엔드 사이의 긴밀한 결합이 필요하다. <br/>
> 그러나 리액트에서는 보통 백엔드와 분리되어 있기 때문에, 적절하진 않다.

### 인증 토큰(Authentication Tokens)

- 클라이언트가 서버에 유효한 자격 증명을 전송한다. 그 다음에 서버에서 허가 토큰(permission token)을 생성한다.
- 이 허가 토큰은 기본적으로 생성되었을 때, 어느 저장 공간에도 저장되지 않는다.
  - 이 토큰은 또한, 알고리즘에 따라 생성된 스트링으로 몇 가지 정보를 포함한다.
- 백엔드에서는 이 토큰을 생성하고, 클라이언트에게 전송한다.
  - 이 토큰을 생성한 백엔드만 해당 토큰의 유효성을 확인하고 검증할 수 있다.(백엔드만 알 수 있는 개인 키 사용)
- 이후, 클라이언트에서 백엔드에 요청을 보낼 때, 해당 토큰을 첨부하면 백엔드에서 검증하고, 보호된 리소스에 대한 접근을 승인한다.


> 결국 서버는 클라이언트가 로그인을 하면, `yes` 같은 응답이 아니라, 인증 토큰을 포함한 응답을 회신한다. <br/>
> 클라이언트 측 리액트에서는 이 토큰을 저장하고, 이후에 보낼 요청에서 해당 토큰을 통해 사용자의 로그인 요청을 판단하는 인디케이터로 사용한다.

## 쿼리 매개변수

- 쿼리 매개변수, 즉 검색 매개변수는 **URL**에서 `?` 뒤에 붙는 매개 변수이다.
- **ID**나 이름, 또는 특정 데이터를 조회할 때에 사용된다. 
- 리액트 라우터에서 쿼리 매개변수에 쉽게 접근할 수 있는 `useSearchParams()` 훅을 사용한다.
- `useSearchParams`를 호출하면 배열을 반환하는데, 이 배열은 두 가지 요소가 있다.
  1. 현재 설정된 쿼리 매개변수에 접근권을 주는 객체
  2. 현재 설정된 쿼리 매개변수를 업데이트하는 함수

```javascript
const [searchParamas, setSearchParams] = useSearchParams();

const isLogin = searchParamas.get('mode') === 'login';
```

- `get` 메서드를 통해 가져오고 싶은 쿼리 매개변수의 값을 가져온다.

### action에서 쿼리 매개변수 추출

- `action`은 컴포넌트가 아니기 때문에 리액트 훅을 사용할 수 없다.
- 따라서, 브라우저가 제공하는 내장 **URL** 생성자 함수를 사용한다.

```javascript
const searchParamas = new URL(request.url).searchParams;
const mode = searchParamas.get('mode') || 'login';
```

- 사용자 인증 폼을 통한 액션은 다음과 같다.

```javascript
export async function action({ request, params }) {
  const searchParamas = new URL(request.url).searchParams;
  const mode = searchParamas.get('mode') || 'login';

  const data = await request.formData();
  const authData = {
    email: data.get('email'),
    password: data.get('password'),
  };

  const response = await fetch(`http://localhost:8080/${mode}`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(authData),
  });

  if (response.status === 422 || response.status === 401) {
    return response;
  }

  if (!response.ok) {
    throw json(
      {
        message: 'Could not authenticate user.',
      },
      {
        status: 500,
      },
    );
  }

  return redirect('/');
}
```

### 사용자 관련 유효성 검증 오류 확인

- `useActionData`를 통해 `action`에서 전달된 데이터를 받는다.
- 또한, `useNavigation`을 통해 제출 중인지 상태 파악을 한다.
- **JS**의 `Object.values()`를 통해 객체의 값들만 뽑아내는 것은 덤.

<details>
  <summary>코드 보기</summary>
  
```javascript
import {
  Form,
  Link,
  useActionData,
  useNavigation,
  useSearchParams,
} from 'react-router-dom';

import classes from './AuthForm.module.css';

function AuthForm() {
  const data = useActionData();
  const navigation = useNavigation();

  const [searchParamas, setSearchParams] = useSearchParams();

  const isLogin = searchParamas.get('mode') === 'login';
  const isSubmitting = navigation.state === 'submitting';

  return (
    <Form method="post" className={classes.form}>
      <h1>{isLogin ? 'Log in' : 'Create a new user'}</h1>
      {data && data.errors && (
        <ul>
          {Object.values(data.errors).map((err) => (
            <li key={err}>{err}</li>
          ))}
        </ul>
      )}
      {data && data.errors && <p>{data.message}</p>}
      <p>
        <label htmlFor="email">Email</label>
        <input id="email" type="email" name="email" required />
      </p>
      <p>
        <label htmlFor="image">Password</label>
        <input id="password" type="password" name="password" required />
      </p>
      <div className={classes.actions}>
        <Link to={`?mode=${isLogin ? 'signup' : 'login'}`}>
          {isLogin ? 'Create new user' : 'Login'}
        </Link>
        <button disabled={isSubmitting}>
          {isSubmitting ? 'Submitting...' : 'Save'}
        </button>
      </div>
    </Form>
  );
}

export default AuthForm;
```
</details>

### 백엔드 요청에 토큰 추가하기

- `action`에서 작업하는 코드는 브라우저에서 구동되기 때문에, 값들을 저장할 때 브라우저 **API**를 사용하여 로컬 저장소를 사용하여 저장한다.

```javascript
const resData = await response.json();
const { token } = resData;

localStorage.setItem('token', token);
```

- 따로 파일을 만들어서 함수를 만들어도 된다.

```javascript
export function getAuthToken() {
  const token = localStorage.getItem('token');

  return token;
}
```

- 백엔드에 토큰을 첨부해서 요청을 보낼려면 헤더에 `authorization`을 추가한다.(키)
- 토큰 앞에 `Bearer`가 붙는데, 이것은 **JWT**와 **OAuth**를 나타내는 인증 타입이다.([참고자료](https://overcome-the-limits.tistory.com/741))

```javascript
export async function action({ params, request }) {
  const { eventId } = params;
  const token = getAuthToken();
  const response = await fetch(`http://localhost:8080/events/${eventId}`, {
    method: request.method,
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });

  if (!response.ok) {
    throw json(
      { message: 'Could not delete event.' },
      {
        status: 500,
      },
    );
  }
  return redirect('/events');
}
```

## 로그아웃

- 리액트 라우터가 제공하는 `action`을 사용하기 위해 로그아웃 페이지를 따로 만든다.
- 하지만 이 페이지는 빈 페이지에, 아무것도 렌더링하지 않는다. 단순히 `action`만 존재한다.
- 이 `action`에서 `localStorage`에 접근해서 토큰을 삭제한다.
- [로그아웃이 진행된 이후에, 권한이 필요하면 로그인 페이지로 리다이렉트하고, 권한이 필요없으면 그대로 새로고침하는 방식도 있다.](https://github.com/ash9river/React-Learned/blob/main/section22/readme.md#%EB%9D%BC%EC%9A%B0%ED%8A%B8-%EB%B3%B4%ED%98%B8-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0)

```javascript
import { redirect } from 'react-router-dom';

export function action() {
  localStorage.removeItem('token');

  return redirect('/');
}
```

- 라우트 구조는 이런 방식으로 추가하면 된다.

<img height="80%" width="80%" src="https://github.com/ash9river/React-Learned/assets/121378532/744b377f-152a-4f94-9a31-166cadc3f092" />

- 리액트 라우터에서 제공하는 `Form`을 이용하여 다른 페이지의 `action`을 가져올 수 있기 때문에, `logout` 페이지의 `action`을 가져온다.

```html
<li>
  <Form action="/logout" method="POST">
    <button>Logout</button>
  </Form>
</li>
```

## 로그인 상태에 따른 UI 업데이트

- 토큰이 삭제되면, 컴포넌트도 재평가되어야 하기 때문에 **리액트 컨텍스트**를 통해 앱 전반에 걸쳐 관리를 할 수도 있다.
- 또는, 리액트 라우터의 `loader`를 이용한다.

<details>
  <summary>구조 보기</summary>



![image](https://github.com/ash9river/React-Learned/assets/121378532/a93c9ddc-8d2f-4155-bd13-92bef9cb7724)

![image](https://github.com/ash9river/React-Learned/assets/121378532/c754694d-adff-4ac8-9f3f-37f67bb98413)

```javascript
import { NavLink, Form, useRouteLoaderData } from 'react-router-dom';

import classes from './MainNavigation.module.css';
import NewsletterSignup from './NewsletterSignup';

function MainNavigation() {
  const token = useRouteLoaderData('root');

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
              end
            >
              Home
            </NavLink>
          </li>
          <li>
            <NavLink
              to="/events"
              className={({ isActive }) =>
                isActive ? classes.active : undefined
              }
            >
              Events
            </NavLink>
          </li>
          <li>
            <NavLink
              to="/newsletter"
              className={({ isActive }) =>
                isActive ? classes.active : undefined
              }
            >
              Newsletter
            </NavLink>
          </li>
          {!token ? (
            <li>
              <NavLink
                to="/auth?mode=login"
                className={({ isActive }) =>
                  isActive ? classes.active : undefined
                }
              >
                Authentication
              </NavLink>
            </li>
          ) : (
            <li>
              <Form action="/logout" method="POST">
                <button>Logout</button>
              </Form>
            </li>
          )}
        </ul>
      </nav>
      <NewsletterSignup />
    </header>
  );
}

export default MainNavigation;
```

</details>

### 라우트 보호 추가하기

- **URL**로 페이지를 접속하면, 접근할 수 있는 것을 방지하기 위해서 라우트 보호가 필요하다.
- 토근 유무를 파악해서, 토근이 없으면 `redirect`를 한다.















