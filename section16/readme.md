# Custom Hooks

## React Hooks Rules

1. 컴포넌트의 내부에서 호출해야 한다.
2. 최상위 레벨에서 호출해야한다.

### 생성하기

- 보통 `hooks`라는 폴더를 따로 만들어서 관리한다.
- 함수의 이름은 무조건 `use`로 시작한다.
  - `use`로 시작하는 함수는 훅으로 인식되며, 특정 규칙이 부여된다.
- 컴포넌트의 간결화와 재사용성을 높이기 위해 주로 사용된다.

### State 관리 및 반환

- `state`를 이용하기 위해서, 새롭게 값을 입력하고, 사용한다.
- 연관된 모든 `state` 값을 관리하면서 재사용성이 높아지게 된다.
- 커스텀 훅에서 `state` 값을 업데이트하면 컴포넌트 안에서 `state` 값을 조정한 것가ㅏㅌ은 효과가 나타난다.


```javascript
import { useEffect, useState } from 'react';

function useFetch(fetchFunction) {
  const [isFetching, setIsFetching] = useState();
  const [fetchedData, setFetchedData] = useState();
  const [error, setError] = useState();

  useEffect(() => {
    async function fetchData() {
      setIsFetching(true);
      try {
        const data = await fetchFunction();
        setFetchedData(data);
      } catch (err) {
        setError({ message: err.message || 'Failed to fetch data.' });
      }

      setIsFetching(false);
    }

    fetchData();
  }, [fetchFunction]);

  return {
    isFetching,
    fetchedData,
    error,
  };
}
```




```
