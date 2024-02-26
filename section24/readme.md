# React Query/Tanstack Query

## Tanstack Query

- `React-Query`로도 불리며, **HTTP** 요청을 전송하고, 프론트엔드의 **UI**를 백엔드 데이터와 동기화시키는 데에 도움을 주는 라이브러리이다.
- 무조건 `Tanstack Query`를 사용할 필요가 없고, `useEffect` 혹은 `axios` 등으로도 작업을 할 수 있다.
- 하지만 `Tanstack Query`를 이용하면 코드가 매우 간결해지고, 내장된 고급 기능을 이용하면 작업이 좀 더 수월해진다.

## useQuery

- `useQuery` 훅을 통해 `state`의 관리와 `useEffect`를 `Tanstack Query`로 변환할 수 있다.
- 이 `useQuery`는 자체적으로 작동해서 **HTTP**요청을 전송하고, 필요한 데이터를 가져온다.
- 또한, 로딩 상태에 대한 정보도 제공하여 요청 전송 중에 발생한 오류도 알 수 있다.
- 그리고 이 `useQuery`는 `promise`를 반환하는 함수를 필요로 하는 것이 특징이다.

### queryFn

- `useQuery`에 객체를 전달할 수 있는데, 그 중에서도 `queryFn` 속성을 이용한다.
- `queryFn`은 쿼리 함수를 의미하는데, 이 함수를 이용해 실제 요청을 전송할 때 실행할 실제 코드를 정의한다.
- `Tanstack Query`는 **HTTP** 요청을 전송하는 로직이 내장되어 있지 않다.
  - 대신에 요청을 관리하는 로직을 제공한다.

### queryKey

- `useQuery`를 사용할 때, 전송하는 모든 **HTTP** 요청에는 쿼리 키가 있다.
- `Tanstack Query`는 내부에서 이 `queryKey`를 이용하여 요청으로 생성된 데이터를 캐시 처리한다.
- 나중에 동일한 요청을 전송하면, 이전 요청의 응답을 재사용할 수 있다.
- 그래서 모든 쿼리에는 이런 키가 필요한데, 이 키는 배열로 이루어져 있다.
- 배열은 리액트 쿼리를 내부적으로 저장하여, 유사한 값으로 이루어진 유사한 배열을 사용할 때마다, `Tanstack Query`는 이 배열을 확인하고 기존 데이터를 재사용한다.

### useQuery 비구조화

- `useQuery`가 실행되면 객체를 얻을 수 있는데, `data` 속성을 추출할 수 있다.
  - 이 `data` 속성은 실제 응답 데이터가 값으로 들어 있다.
- 또한, 이 객체는 `isPending` 속성도 갖는데, 요청이 여전히 실행 중인지, 아니면 응답을 받았는지 알 수 있다. 

## QueryClientProvider

- `Tanstack Query`를 이용할려면 `Context`를 이용할 떄처럼 `QueryClientProvider`로 래핑하는 작업이 필수적이다.

```javascript
const queryClinet = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClinet}>
      <RouterProvider router={router} />;
    </QueryClientProvider>
  );
}
```

<details>
  <summary>기존의 코드</summary>

```javascript
useEffect(() => {
    async function fetchEvents() {
      setIsLoading(true);
      const response = await fetch('http://localhost:3000/events');

      if (!response.ok) {
        const err = new Error('An error occurred while fetching the events');
        err.code = response.status;
        err.info = await response.json();
        throw err;
      }

      const { events } = await response.json();

      return events;
    }

    fetchEvents()
      .then((events) => {
        setData(events);
      })
      .catch((err) => {
        setError(err);
      })
      .finally(() => {
        setIsLoading(false);
      });
  }, []);
```
</details>
