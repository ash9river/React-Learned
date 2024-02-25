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











