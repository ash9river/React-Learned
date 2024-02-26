# React Query/Tanstack Query

## Tanstack Query

- `React-Query`로도 불리며, **HTTP** 요청을 전송하고, 프론트엔드의 **UI**를 백엔드 데이터와 동기화시키는 데에 도움을 주는 라이브러리이다.
- 무조건 `Tanstack Query`를 사용할 필요가 없고, `useEffect` 혹은 `axios` 등으로도 작업을 할 수 있다.
- 하지만 `Tanstack Query`를 이용하면 코드가 매우 간결해지고, 내장된 고급 기능을 이용하면 작업이 좀 더 수월해진다.

### useQuery

- `useQuery` 훅을 통해 `state`의 관리와 `useEffect`를 `Tanstack Query`로 변환할 수 있다.
- 이 `useQuery`는 자체적으로 작동해서 **HTTP*요청을 전송하고, 필요한 데이터를 가져온다.
- 또한, 로딩 상태에 대한 정보도 제공하여 요청 전송 중에 발생한 오류도 알 수 있다.

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
