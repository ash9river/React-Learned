# Data Fetch & HTTP Requests

## HTTP Request

- **React**로 직접 데이터를 가져오거나 저장하는 것을 절대 해서는 안 된다.
- 만약에 클라이언트 내부에서 DB에 직접 연결을 하게 된다면, 브라우저의 자바스크립트 코드를 통해 DB의 인증 정보를 노출시킬 수 있다.
- 브라우저에서 실행되는 모든 자바스크립트 코드는 브라우저뿐만이 아니라 클라이언트도 접근할 수 있다.

## GET

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

### async & await

- 좀 더 가독성이 좋게 비동기 함수들을 정의할 수 있다.
- `useEffect` 안에서 `async`를 통해 함수를 정의하고 `await`을 통해 값을 가져온다.
- 그리고 정의한 함수를 호출하여 작업을 마무리짓는다.

```javascript
  useEffect(() => {
    async function fetchPlaces() {
      const response = await fetch('http://localhost:3000/places');
      const resData = await response.json();
      setAvailablePlaces(resData.places);
    }

    fetchPlaces();
  }, []);
```

- 데이터를 받아 올 때, 항상 에러가 나타날 수 있다는 것을 염두해야 한다.
  - 네트워크 연결이 충돌하는 상황
  - 백엔드가 에러 응답을 보내는 상황
  - **etc...** 
- `try & catch`를 사용하여 에러를 잡아낼 수 있다.

```javascript
  useEffect(() => {
    const fetchPlaces = async () => {
      try {
        const response = await fetch('http://localhost:3000/places');
        const resData = await response.json();

        setAvailablePlaces(resData.places);
      } catch (err) {
        console.log(err);
      }
    };

    fetchPlaces();
  }, []);
```

- 그런데 에러 응답도 여러 케이스로 분리할 수 있다.
- **HTTP** 요청이 성공적으로 이루어졌지만, 응답이 **HTTP** 상태 코드가 성공 범위가 아닌 경우.
  - `Response` 객체의 `ok`를 이용해서 `try` 블록에서 `response.ok`가 `false`인 경우로 따로 분리할 수도 있다.

```javascript
  useEffect(() => {
    const fetchPlaces = async () => {
      try {
        setIsFetching(true);
        const response = await fetch('http://localhost:3000/places');
        const resData = await response.json();

        if (!response.ok) {
          throw new Error('Failed to fetch places');
        }

        setAvailablePlaces(resData.places);
      } catch (err) {
        setError({
          message:
            err.message || 'Could not fetch places, please try again later.',
        });
      }
      setIsFetching(false);
    };

    fetchPlaces();
  }, []);
```

### 사용자 경험을 향상시키는 fallback

- 네트워크 속도에 따라서 이미지의 로딩이 오래 걸릴 수도 있다.
- 이미지가 로딩되는 동안 대체 이미지나 대체 텍스트를 통해 사용자 경험을 향상시키는 편이 좋다.

> **GET** 요청시 로딩, 데이터, 에러의 총 세 가지 `state`를 사용하게 된다.

## POST





ㅁ
