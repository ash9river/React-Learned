# Custom Hooks

## React Hooks Rules

1. 컴포넌트의 내부에서 호출해야 한다.
2. 최상위 레벨에서 호출해야한다.

### 생성하기

- 보통 `hooks`라는 폴더를 따로 만들어서 관리한다.
- 함수의 이름은 무조건 `use`로 시작한다.
  - `use`로 시작하는 함수는 훅으로 인식되며, 특정 규칙이 부여된다.

> 컴포넌트의 간결화와 재사용성을 높이기 위해 주로 사용된다.

### State 관리 및 반환

- `state`를 이용하기 위해서, 새롭게 값을 입력하고, 사용한다.
- 연관된 모든 `state` 값을 관리하면서 재사용성이 높아지게 된다.
- 커스텀 훅에서 `state` 값을 업데이트하면 컴포넌트 안에서 `state` 값을 조정한 것과 같은 효과가 나타난다.


```javascript
import { useEffect, useState } from 'react';

export function useFetch(fetchFunction,initialVal) {
  const [isFetching, setIsFetching] = useState();
  const [fetchedData, setFetchedData] = useState(initialVal);
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
    setFetchedData,
    fetchedData,
    error,
  };
}
```

- 커스텀 훅의 `state` 값들이 여러 컴포넌트에서 각각 사용될 때, 그 각기 다른 컴포넌트는 서로 영향을 미칠 수 없다.
- 각각 독립적인 `state`를 부여받기 때문이다.

>  커스텀 훅을 사용해서 만든 기능은 각 컴포넌트에서 독립된 상태를 가진다. 

- 컴포넌트에서 사용할 때, 비구조화 할당을 이용해서 별칭을 붙일 수 있다.

```javascript
  const {
    isFetching,
    fetchedData: userPlaces,
    setFetchedData: setUserPlaces,
    error,
  } = useFetch(fetchUserPlaces, []);
```

### 유동적인 Custom Hooks

- 자바스크립트에서 프로미스가 아닌 함수와 API를 프로미스 기반 함수로 바꾸기 위해 프로미스 객체를 리턴한다.
- `resolve`로 이행된 결과를 받을 수 있고, `reject`로 실패된 결과값을 받을 수 있다.

```javascript
async function fetchSortedPlaces() {
  const places = await fetchAvailablePlaces();

  return new Promise((resolve, reject) => {
    navigator.geolocation.getCurrentPosition((position) => {
      const sortedPlaces = sortPlacesByDistance(
        places,
        position.coords.latitude,
        position.coords.longitude,
      );

      resolve(sortedPlaces);
    });
  });
}
```

- 컴포넌트에서 `useFetch()`로 이용하고 싶은 함수와 초기 값을 넣고, 그 함수에서 반환되는 `Promise` 객체를 정의함으로써 `Custom Hooks`를 재사용하였다.
- `useFetch()` -> `fetchSortedPlaces()` $=$ `fetchFunction()` -> `fetchAvailablePlaces()`
- `fetchAvailablePlaces()`에서 `response`의 `Promise` 파싱 $=$ `response.json()` -> `resData` -> `resData.places` 반환
- `fetchSortedPlaces()`에서 `places`를 이용하여 `Promise`의 `resolve` 반환
- `useFetch()`에서 `fetchSortedPlaces()` $=$ `fetchFunction()`에서 반환된 `Promise`로 `data`를 얻고 `fetchedData: availablePlaces`를 사용하게 된다.

```javascript
const {
  isFetching,
  error,
  fetchedData: availablePlaces,
} = useFetch(fetchSortedPlaces, []);
```

```javascript
export async function fetchAvailablePlaces() {
  const response = await fetch('http://localhost:3000/places');
  const resData = await response.json();

  if (!response.ok) {
    throw new Error('Failed to fetch places');
  }

  return resData.places;
}
```

- 상당히 이해하기 어려웠지만, 자바스크립트를 좀 더 이해하는 데에 도움이 되었다.
- 현업에서는 `axios`를 더 이용하지만, `axios`는 저번 프로젝트에서 사용해보아서 `fetch()`함수만 이용해보았다.
- [참고자료](https://heytech.tistory.com/245)
