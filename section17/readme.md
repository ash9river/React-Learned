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
- 폼에서 버튼을 클릭하면 **HTTP** 요청이 발생하고, 웹사이트의 서버로 전송된다.
- 다음과 같은 절차로 폼 제출이 진행된다.
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
- 그래서 **HTML** 폼의 기본 작동 방식을 먼저 막아야 한다.

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

- 브라우저에 내장된 함수로써, 폼에 입력된 각기 다른 값들을 쉽게 얻을 수 있게 도와주는 객체이다.
- `FormData`에 인수로서 `event.target`을 넣는데, 이 `event.target`은 **폼**이다.(**폼**이 제출되었기 때문이다.)
- `FormData` 객체가 **폼**에 있는 `input`에 추가된, 모든 데이터로 접근할 수 있게 해준다.
- 값을 추출할려는 모든 `input` 필드에 `name` 속성이 있어야 한다.
- 심지어 입력창이 아니라 `select` 필드 같은 다른 창일지라도 `name` 속성이 있어야 한다.
- `FormData` 객체의 모든 값들을 그룹화 하기 위해서 `Object` 클래스의 `fromEntries()`를 사용한다. 

```javascript
function handleSubmit(event){
    event.preventDefault();
    
    const fd=new FormData(event.target);
    Object.fromEntries(fd.entries());
}
```

- 그러나 다음과 같이, 같은 이름의 다양한 값이 있는 체크박스같은 입력창은 `entry`나 `fromEntries()`를 사용할 때는 빠져있다.

```html
<fieldset>
  <legend>How did you find us?</legend>
  <div className="control">
    <input
      type="checkbox"
      id="google"
      name="acquisition"
      value="google"
    />
    <label htmlFor="google">Google</label>
  </div>

  <div className="control">
    <input
      type="checkbox"
      id="friend"
      name="acquisition"
      value="friend"
    />
    <label htmlFor="friend">Referred by friend</label>
  </div>

  <div className="control">
    <input type="checkbox" id="other" name="acquisition" value="other" />
    <label htmlFor="other">Other</label>
  </div>
</fieldset>
```

- 이러한 값들은 `formData` 객체의 `getAll()` 메서드를 이용해서 따로 모은다.

```javascript
function handleSubmit(event) {
  event.preventDefault();

  const fd = new FormData(event.target);
  const acquisitionChannel = fd.getAll("acquistion");
  const data = Object.fromEntries(fd.entries());
  data.acquisition = acquisitionChannel;
  console.log(data);
}
```

### Form 초기화하기

1. 폼의 빌트인 기능인 `type`의 속성 `reset`을 이용한다.

```html
<button type="reset" className="button button-flat">
  Reset
</button>
```

2. `state`로 상태를 관리하고 있다면 단순히 `state`를 초기화한다.
3. `ref`로 상태를 관리하고 있다면 단순히 `current.value`를 초기화한다.
  - 그러나 `ref`의 초기화는 조심스러워야 한다. 왜나하면 **DOM**의 업데이트는 **React**가 전담해야 하는 것이 보편적이기 때문이다.
  - 그래서 `event.target.reset()`을 호출하여, 상대적으로 덜 강제적인 참조 초기화를 사용한다.

## 유효성 검증

### state로 키보드 입력마다 유효성 검증

- `blur`로 `input`이 `focus`를 잃을 때, 유효성을 확인한다.
- 그러나 사용자가 타이핑을 다시 할려고 모든 입력을 지워도 오류가 남아있어 사용자 경험이 감소된다.

```javascript
const [didEddit, setDidEdit] = useState({
    email: false,
    password: false,
  });

const emailIsInvalid = didEddit.email && !enteredValues.email.includes('@');
function handleInputBlur(identifier) {
  setDidEdit((prevEdit) => {
    return {
      ...prevEdit,
      [identifier]: true,
    };
  });
}
```
```html
<div className="control no-margin">
  <label htmlFor="email">Email</label>
  <input
    id="email"
    type="email"
    name="email"
    onBlur={() => handleInputBlur('email')}
    onChange={(event) => handleInputChange('email', event.target.value)}
    value={enteredValues.email}
  />
  <div className="control-error">
    {emailIsInvalid && <p>Please enter a valid email address.</p>}
  </div>
</div>
```

> 자주 쓰이는 이메일 정규식이 따로 있으나 여기선 간단하게 사용하였다.

### ref로 form 제출시 유효성 검증

- 제출 시에만 유효성 검증한다.
- 하지만 제출 시에만 유효성 검증하는 것보다 짬뽕이 더 낫다.

```javascript
const [emailIsInvalid, setEmailIsInvalid] = useState(false);

const email = useRef();
const password = useRef();

function handleSubmit(event) {
  event.preventDefault();

  const enteredEmail = email.current.value;
  const enteredPassword = password.current.value;

  const emailIsvalid = enteredEmail.includes('@');

  if (!emailIsvalid) {
    setEmailIsInvalid(true);

    return;
  }

  setEmailIsInvalid(false);

  console.log('Sending HTTP Request, etc...');

  console.log(
    `submitted\n 
      email : ${enteredEmail}\n password : ${enteredPassword}`,
  );
}
```

```html
<div className="control no-margin">
  <label htmlFor="email">Email</label>
  <input id="email" type="email" name="email" ref={email} />
  <div className="control-error">
    {emailIsInvalid && <p>Please enter a valid email address.</p>}
  </div>
</div>
```

### props로 유효성 검사

- `required` : 브라우저가 사용자 입력을 검증할 수 있도록 `input`에 설정할 수 있는 빌트인 속성
- `required`에 의해 `type`가 고려된다.
  - 만약 `type`의 속성이 `email`이면 브라우저 자체에서 `email` 입력을 검증한다. 
- `minLength` : `required`와 같이 빌트인 속성으로, 최소 길이를 검증한다.

```html
<div className="control-row">
  <div className="control">
    <label htmlFor="password">Password</label>
    <input
      id="password"
      type="password"
      name="password"
      required
      minLength={6}
    />
  </div>

  <div className="control">
    <label htmlFor="confirm-password">Confirm Password</label>
    <input
      id="confirm-password"
      type="password"
      name="confirm-password"
    />
  </div>
</div>
```








ㅁ
