# Forms & User Input

## Forms

- 입력 필드의 집합
- `Form`의 용도
  1. 사용자의 양식 제출을 관리하고 사용자가 입력한 값을 추출
  2. 사용자가 입력한 데이터를 검증

### 폼 제출 다루기

- `class`와 `for`은 자바스크립트에서 사용되는 이름이기 때문에 속성이름으로 사용할 수 없다.
- 따라서 `htmlFor`를 사용해야 한다.

```html
<label htmlFor="email">Email</label>
<input id="email" type="email" name="email" />
```

- 폼에서 버튼은 폼을 제출하는 용도로 쓰인다.
- **HTTP** 요청이 발생하고, 웹사이트의 서버로 전송된다.
- 다음과 같은 절차로 진행된다.
    1. `서버로 재전송` : 사용자가 폼을 작성하고 제출하면, 입력된 데이터는 **HTTP POST** 요청을 통해 서버로 전송된다. 전송된 데이터는 서버로 넘어가게 되며, 이때 사용자가 입력한 정보가 포함된다.
    2. `서버에서의 데이터 처리` : 서버는 전송된 데이터를 받아와서 필요한 프로세스를 수행한다. 이 과정에서 서버는 받은 데이터를 기반으로 폼에 정의된 필드에 따라 다양한 작업을 수행할 수 있다.
    3. `서버에서의 폼 데이터 관리` : 서버는 전송된 폼 데이터를 관리하고 저장한다. 이 데이터는 필요한 경우에 다시 활용되어 사용자에게 제공되거나, 분석되어 통계 정보나 보고서를 생성하는 등의 용도로 활용될 수 있다.
    4. `서버 응답 및 클라이언트 갱신` : 서버는 폼 데이터를 받아 처리한 후, 클라이언트에게 응답을 보낸다. 클라이언트는 서버에서 받은 응답을 기반으로 화면을 업데이트하거나 사용자에게 적절한 메시지를 제공할 수 있다.

<br/>

> 폼 제출의 주요 목적은 사용자가 입력한 정보를 서버로 전송하여, 서버에서 해당 정보를 적절히 처리하고 관리하는 것이다. <br/>
> 이를 통해 웹 애플리케이션은 동적이면서도, 실시간으로 데이터를 관리하며 사용자에게 최신 정보를 제공할 수 있다.

<br/>

- 그러나 **Next.js**같은 풀스택 **React** 방식을 제외하고, **React**로 만드는 대부분의 경우 버튼이 문제가 된다.
- **HTML** 폼에서 버튼이 웹사이트의 서버로 **HTTP** 요청을 발생시키고, 페이지를 리로딩시키기 때문이다.
- 그래서 **HTML** 폼의 기본 작동 먼저 막아야 한다.

1. 버튼의 속성 설정

- 폼의 버튼은 기본으로 `submit`으로 되어 있기 때문에 `button` 속성을 부여한다

```html
<button type="button" className="button" onClick={handleSubmit}>
  Login
</button>
```

2. 이벤트 객체

- 양식 속성에 `onSubmit`을 추가함으로써, 제출되었을 때 이벤트 객체 값을 사용할 수 있다.
- `event.preventDefault()`를 사용함으로써, 브라우저의 기본 구성, 즉 **HTML** 폼에 의한 페이지 리로딩을 방지한다.

```javascript
export default function Login() {
  function handleSubmit(event) {
    event.preventDefault();
    console.log("submitted");
  }

  return (
    <form onSubmit={handleSubmit}>
        // 각종 로직들...
        <button className="button">
          Login
        </button>
      </p>
    </form>
  );
}
```

### state를 통한 사용자 입력 관리

- `state`로 객체를 다루어서 여러 입력을 한번에 관리하고, `identifier`를 대괄호로 묶어서 동적으로 `state`를 입력할 수 있게 하였다.
- 또한, `onChange` 이벤트에서 익명함수를 통해 `identifier`와 `event` 객체의 값만 전달할 수 있었다.
- `input`의 `value`를 통해 `state`와 바인딩할 수 있었다.

```javascript
import { useState } from "react";

export default function Login() {
  const [enteredValues, setEnteredValues] = useState({
    email: "",
    password: "",
  });

  function handleSubmit(event) {
    event.preventDefault();
    console.log(
      "submitted\n" +
        "email : " +
        enteredValues.email +
        "\n password : " +
        enteredValues.password
    );
  }

  function handleInputChange(identifier, value) {
    setEnteredValues((prevValues) => {
      return {
        [identifier]: value,
        ...prevValues,
      };
    });
  }
  return (
    <form onSubmit={handleSubmit}>
      <h2>Login</h2>

      <div className="control-row">
        <div className="control no-margin">
          <label htmlFor="email">Email</label>
          <input
            id="email"
            type="email"
            name="email"
            onChange={(event) => handleInputChange("email", event.target.value)}
            value={enteredValues.email}
          />
        </div>

        <div className="control no-margin">
          <label htmlFor="password">Password</label>
          <input
            id="password"
            type="password"
            name="password"
            onChange={(event) =>
              handleInputChange("password", event.target.value)
            }
            value={enteredValues.password}
          />
        </div>
      </div>

      <p className="form-actions">
        <button className="button button-flat">Reset</button>
        <button className="button">Login</button>
      </p>
    </form>
  );
}
```

### Refs를 통한 사용자 입력 수집

- `Refs` 를 이용하여 관리할 수도 있다.
- `Refs`의 `current` 속성을 이용하여 값을 얻는다.
- `state`를 이용하는 것보다 좀 더 간편하다.
- 그러나 값을 재설정하는 것이 까다롭다.
  - 만약 복잡한 양식을 가지고 있으면, 많은 참조를 만들어야 하고, 참조를 하나하나 연결해야 한다.

```javascript
import { useRef } from "react";

export default function Login() {
  const email = useRef();
  const password = useRef();

  function handleSubmit(event) {
    event.preventDefault();

    const enteredEmail = email.current.value;
    const enteredPassword = password.current.value;

    console.log(
      "submitted\n" +
        "email : " +
        enteredEmail +
        "\n password : " +
        enteredPassword
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <h2>Login</h2>

      <div className="control-row">
        <div className="control no-margin">
          <label htmlFor="email">Email</label>
          <input id="email" type="email" name="email" ref={email} />
        </div>

        <div className="control no-margin">
          <label htmlFor="password">Password</label>
          <input id="password" type="password" name="password" ref={password} />
        </div>
      </div>

      <p className="form-actions">
        <button className="button button-flat">Reset</button>
        <button className="button">Login</button>
      </p>
    </form>
  );
}
```

### FormData








ㅁ