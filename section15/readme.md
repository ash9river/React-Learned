# Data Fetch & HTTP Requests

## HTTP Request

- **React**로 직접 데이터를 가져오거나 저장하는 것을 절대 해서는 안 된다.
- 만약에 클라이언트 내부에서 DB에 직접 연결을 하게 된다면, 브라우저의 자바스크립트 코드를 통해 DB의 인증 정보를 노출시킬 수 있다.
- 브라우저에서 실행되는 모든 자바스크립트 코드는 브라우저뿐만이 아니라 클라이언트도 접근할 수 있다.

### fetch()

- 브라우저가 제공하는 `fetch()` 인자로 url을 받는다.
- `fetch()`는 값을 감싸는 객체인 `Promise`를 반환하는데, 이 때 `Promise`는 받은 `Response` 객체를 감싼다.
  - `Promise`는 자바스크립트 표준 객체이며, `Promise`의 상태에 따라 다른 값을 산출해낸다.
  - API 호출이 성공했을 경우에는 `Response` 객체를 **resolve**하고, 실패했을 경우에는 `error` 객체를 **reject**한다.
- `fetch()`로 불러온 결과를 `then()`에서 함수를 전달하여 함수를 정의하면 `fetch()`의 **resolve**된 `Promise`로부터 `Respone`를 받는다.
  - `then()`에 전달되는 함수는 `Response`를 받은 이후에, 브라우저를 통해 한 번만 실행된다.
  - 즉각적으로 일어나지 않아서, `await`를 이용하여 `Respone`에 접근한다.
  - **비동기**로 코드가 실행할 수 있다.
  - 그러나 컴포넌트 함수 자체는 비동기가 허용되지 않는다.(**React**가 지정한 제약이다.)
- `Response` 객체에는 자체 내장 `json()` 함수가 있다.
  - 이 함수는 `Response`의 **JSON**을 자동으로 자바스크립트 객체로 변환시켜준다.
 
## useEffect를 통한 HTTP GET 요청

- `state`만 가지고 데이터를 받으면 무한루프를 생성할 가능성이 있다.

```javascript
const [availablePlaces, setAvailablePlaces] = useState([]);

  fetch('http://localhost:3000/places')
    .then((response) => {
      return response.json();
    })
    .then((resData) => {
      setAvailablePlaces(resData.places);
    });
```

- `useEffect`로 감싸서 처음 실행 딱 한 번만 하도록 만들 수 있다.

```javascript
  const [availablePlaces, setAvailablePlaces] = useState([]);

  useEffect(() => {
    fetch('http://localhost:3000/places')
      .then((response) => {
        return response.json();
      })
      .then((resData) => {
        setAvailablePlaces(resData.places);
      });
  }, []);
```

- **HTTP** 요청 전송은 일종의 `SideEffect`로 컴포넌트의 `state`를 바꿔버리기 때문에 무한 루프에 빠질 위험성이 있다.
- `useEffect`의 의존성 배열을 비워둠으로써 무한 루프를 방지한다.



