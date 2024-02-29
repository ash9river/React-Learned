# React Query/Tanstack Query

## Tanstack Query(React Query)

- `React-Query`로도 불리며, **HTTP** 요청을 전송하고, 프론트엔드의 **UI**를 백엔드 데이터와 동기화시키는 데에 도움을 주는 라이브러리이다.
- 무조건 `Tanstack Query`(이하 리액트 쿼리)를 사용할 필요가 없고, `useEffect` 혹은 `axios` 등으로도 작업을 할 수 있다.
- 하지만 `Tanstack Query`를 이용하면 코드가 매우 간결해지고, 내장된 고급 기능을 이용하면 작업이 좀 더 수월해진다.
- 빈번하게 변경되는 데이터가 아니라면 매번 서버에 요청을 날리는 것보다, 캐싱된 정보를 제공하는 것이 서버의 부담을 줄이면서도 사용자 경혐을 향상시킨다.

### 리액트 쿼리는 다음과 같은 이슈를 해결한다.

- `동일한 데이터에 대한 여러 요청`
  - 리액트 쿼리는 데이터를 캐싱하여 동일한 요청이 반복될 때 네트워크 요청을 중복하여 보내지 않고, 이전에 가져온 데이터를 재사용한다.
  - 이를 캐싱이라고 하는데, 이 캐싱을 통해 네트워크 요청의 중복을 제거하고 불필요한 데이터 전송을 방지하여 애플리케이션의 성능을 향상시킨다.
- `해당 데이터가 오래된 것을 파악`
  - 리액트 쿼리는 캐시된 데이터의 상태를 추적하고 관리한다.
  - 데이터가 캐시될 때 해당 데이터의 최신 상태와 만료 시간을 함께 저장한다.
  - 이를 통해 데이터가 오래되었는지 여부를 식별하고, 필요할 경우 데이터를 업데이트할 수 있다. 
- `오래된 데이터인 경우 업데이트`
  - 리액트 쿼리는 주기적으로 데이터를 리프레시하여 최신 데이터로 업데이트한다.
  - 캐시된 데이터의 만료 시간을 추적하고, 만료 시간이 지난 경우 자동으로 데이터를 업데이트한다.
  - 또한 `refetch` 함수를 사용하여 데이터를 수동으로 다시 불러올 수 있다. 

## useQuery

- `useQuery` 훅을 통해 `state`의 관리와 `useEffect`를 `Tanstack Query`로 변환할 수 있다.
- 이 `useQuery`는 자체적으로 작동해서 **HTTP**요청을 전송하고, 필요한 데이터를 가져온다.
- 또한, 로딩 상태에 대한 정보도 제공하여 요청 전송 중에 발생한 오류도 알 수 있다.
- 그리고 이 `useQuery`는 `promise`를 반환하는 함수를 필요로 하는 것이 특징이다.

### queryFn

- `useQuery`에 객체를 전달할 수 있는데, 그 중에서도 `queryFn` 속성을 이용한다.
- `queryFn`은 쿼리 함수를 의미하는데, 이 함수를 이용해 실제 요청을 전송할 때 실행할 실제 코드를 정의한다.
- 리액트 쿼리 는 **HTTP** 요청을 전송하는 로직이 내장되어 있지 않다.
  - 대신에 요청을 관리하는 로직을 제공한다.

### queryKey

- `useQuery`를 사용할 때, 전송하는 모든 **HTTP** 요청에는 쿼리 키가 있다.
- 리액트 쿼리는 내부에서 이 `queryKey`를 이용하여 요청으로 생성된 데이터를 캐시 처리한다.
- 나중에 동일한 요청을 전송하면, 이전 요청의 응답을 재사용할 수 있다.
- 그래서 모든 쿼리에는 이런 키가 필요한데, 이 키는 배열로 이루어져 있다.
- 배열은 리액트 쿼리를 내부적으로 저장하여, 유사한 값으로 이루어진 유사한 배열을 사용할 때마다, 리액트 쿼리는 이 배열을 확인하고 기존 데이터를 재사용한다.

### useQuery 비구조화

- `useQuery`가 실행되면 객체를 얻을 수 있는데, `data` 속성을 추출할 수 있다.
  - 이 `data` 속성은 실제 응답 데이터가 값으로 들어 있다.
- 또한, 이 객체는 `isPending` 속성도 갖는데, 요청이 여전히 실행 중인지, 아니면 응답을 받았는지 알 수 있다. 

## QueryClientProvider

- 리액트 쿼리를 이용할려면 `Context`를 이용할 떄처럼 `QueryClientProvider`로 래핑하는 작업이 필수적이다.

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

## 쿼리 동작 - 캐시 및 만료된 데이터

- 리액트 쿼리는 응답 데이터를 캐시처리한다.
  - 특정 `queryKey`를 가진 인스턴스가 마운트 되었을 때, `queryKey`와 관련된 데이터를 캐싱하고, 이전 요청 결과를 재사용하는 방식으로 동작한다.
- 리액트 쿼리는 특정 `queryKey`가 이미 사용되었고, 데이터가 캐시처리됨을 확인하면, 데이터를 즉시 제공한다.
  - 이와 동시에 내부적으로 쿼리 요청을 다시 전송해서 업데이트된 데이터가 있는지 확인한다.
  - 만약, 업데이트된 데이터가 있으면 자체적으로 교체한다.
- 즉각적인 결과와 업데이트된 데이터가 내부적으로 처리되는 요청을 통해 실현된다.

### cache

- 리액트 쿼리는 설정한 `cacheTime`만큼[^rkrwn] 데이터를 메모리에 저장해 놓는다.
  - 리액트에서의 캐시는 `QueryCache` 객체의 `queries` 배열과 `queriesInMap` 객체에 `Query` 객체가 존재하는 것을 말한다.
- 중요한 것은 캐시가 존재한다고 해서 `data refetching`을 하지 않는 것이 아니다.
- 캐시가 존재하더라도 해당 데이터(`Query` 객체)가 `stale` 상태라면 `refetching`을 수행한다.

> 캐시는 `cacheTime`만큼만 유지되지는 않는다. 만약 현재 **UI**에 해당 `Query` 객체의 데이터가 필요한 상황이라면, `cacheTime`이 지났더라도 캐시를 유지한다.
> 그렇지 않으면, 현재 **UI**에 캐시 데이터를 사용하고 있는 컴포넌트가 있음에도 불구하고 캐시를 삭제할 수 있기 때문이다.

[^rkrwn]: 기본값은 5분이다.

 ### stale

- 리액트 쿼리에는 캐시와는 별도로 `stale`이라는 개념이 있다.
- 설정한 `staleTime`만큼[^tmxodlf] 데이터가 fresh 상태로 존재하였다가 그 후에는 `stale` 상태로 바뀐다.
- `fresh`와 `stale` 단어의 대비에서 알 수 있듯이 `fresh`는 데이터를 그대로 사용해도 좋을 만큼 신선한 상태라는 뜻이며, `stale`은 데이터를 새롭게 `fetch`해 오는 것이 필요할 만큼 신선하지 못한 상태라는 뜻이다.
- 그러나 `stale` 상태가 되었다고 해서 `refetching`이 곧바로 일어나는 것이 아니라, `stale` 상태가 되고 특정조건을 만족해야 `refetching`이 일어난다.

<details>
  <summary> refetching이 일어나는 특정조건 </summary>

- 새로운 `Query Instance`가 마운트 될 때 (페이지 이동 후 등의 상황)
- 브라우저 화면을 다시 `focus` 할 때
- 인터넷이 재연결되었을 때
- `refetchInterval`이 설정되어있을 때
</details>

[^tmxodlf]: 기본값은 0초이다.

### staleTime

- 데이터가 `fresh `상태에서 `stale` 상태로 변경되는데 걸리는 시간이다.
- `fresh` 상태일때는 `Query Instance`가 새롭게 마운트되어도 `fetch`가 일어나지 않는다.
- 데이터가 한번 `fetch` 되고 나서 `staleTime`이 지나지 않았다면 언마운트 후에 다시 마운트 되어도 `fetch`가 일어나지 않는다.

### gcTime

- 캐시에 데이터를 보관하는 시간이다.
- 데이터와 캐시를 얼마나 오랫동안 보관할지 임의로 정할 수 있다.

## 쿼리 구성 객체 및 요청 취소

- 리액트 쿼리는 기본적으로 전달되는 쿼리 함수에 데이터를 전달한다.
- 이 데이터에는 쿼리에 사용된 `queryKey`와 `signal`이 있는 객체이다.
- 여기서 `signal`은 요청을 취소할 때 필요하다.
- 사용할 때는 구조분해할당으로 전달하면 된다.

> 기본 객체를 그대로 사용한다면 함수의 포인터를 전달하듯이 사용해도 가능하지만, 커스텀 데이터를 전달하고자 한다면 구조분해할당을 통한 전달이 필수적이다.


<details>
  <summary>코드 보기</summary>

- 쿼리 함수로 객체 전달

```javascript
const [searchTerm, setSearchTerm] = useState('');

const { data, isPending, isError, error } = useQuery({
  queryKey: ['events', { search: searchTerm }],
  queryFn: ({ signal }) => fetchEvents({ signal, searchTerm }),
});
```
- `config`로 `signal` 전달

```javascript
import axios from 'axios';

export async function fetchEvents({ signal, searchTerm }) {
  let url = 'http://localhost:3000/events';
  if (searchTerm) {
    url += `?search=${searchTerm}`;
  }

  try {
    const response = await axios.get(url, {
      signal,
    });

    return response.data.events;
  } catch (error) {
    if (error.response) {
      const err = new Error('An error occurred while fetching the events');
      err.code = error.response.status;
      err.info = error.response.data;
      throw err;
    } else {
      throw new Error('An error occurred while fetching the events');
    }
  }
}
```
</details>

## useMutation

- `useQuery`는 데이터를 가져올 때만 사용하기 때문에, **POST** 요청을 하려면 `useMutation`을 이용해야 한다.
- `useMutataion`은 데이터를 변경하는 쿼리에 최척화되어 있다.
- 그러나 `useMutation`은 `useQuery`와 다르게 컴포넌트가 렌더링될 때, 자동으로 요청을 전송하지 않는다.
- `useMutation`의 `mutation` 속성을 통해서 요청을 언제 실행할 지 결정해야 한다. 

```javascript
import { Link, useNavigate } from 'react-router-dom';
import { useMutation } from '@tanstack/react-query';

import Modal from '../UI/Modal';
import EventForm from './EventForm';
import { createNewEvent } from '../../util/http';
import ErrorBlock from '../UI/ErrorBlock';

export default function NewEvent() {
  const navigate = useNavigate();

  const { mutate, isPending, isError, error } = useMutation({
    mutationFn: createNewEvent,
  });

  function handleSubmit(formData) {
    mutate({
      event: formData,
    });
  }

  return (
    <Modal onClose={() => navigate('../')}>
      <EventForm onSubmit={handleSubmit}>
        {isPending && 'Submitting...'}
        {!isPending && (
          <>
            <Link to="../" className="button-text">
              Cancel
            </Link>
            <button type="submit" className="button">
              Create
            </button>
          </>
        )}
      </EventForm>
      {isError && (
        <ErrorBlock
          title="Failed to create event"
          message={
            error.info?.message ||
            'Failed to create event. Please check your inputs and try again later'
          }
        />
      )}
    </Modal>
  );
}
```

### mutation 성공시 동작 및 쿼리 무효화

- `useMutation`을 활용한 **HTTP** 통신을 성공한 경우, 사용자에게 피드백을 주어야 한다.
- `useMutation`의 `onSuccess` 속성에 함수를 전달함으로써, 성공시 동작을 지정할 수 있다.

```javascript
const { mutate, isPending, isError, error } = useMutation({
  mutationFn: createNewEvent,
  onSuccess: () => {
    navigate('../');
  },
});
```

- 또한, `mutation`을 통해 데이터가 변경되었으므로 기존 쿼리를 무효화시켜야 한다.
- `QueryClient` 객체의 `invalidateQueries()`를 이용하여 기존 쿼리를 무효화시킬 수 있다.
- 이 때, `QueryClient`는 `QueryClientProvider`로 전달한 것이어야 한다.
- `QueryClient.invalidateQueries()` 이런 방식으로 사용한다.
- 여기서 `invalidateQueries()`는 인수로 객체를 받는데, 이 객체에 쿼리 키를 전달한다.
- 쿼리 키가 완전히 일치하지 않아도, 전달된 키가 포함된 모든 쿼리를 무효화시킨다.

```javascript
const { mutate, isPending, isError, error } = useMutation({
  mutationFn: createNewEvent,
  onSuccess: () => {
    queryClient.invalidateQueries({
      queryKey: ['events'],
    });
    navigate('/events');
  },
});
```

- 만약 정확히 일치하는 쿼리 키를 가진 쿼리만 무효화하고 싶으면 `exact`를 `true`로 설정하면 된다.

```javascript
const { mutate, isPending, isError, error } = useMutation({
  mutationFn: createNewEvent,
  onSuccess: () => {
    queryClient.invalidateQueries({
      queryKey: ['events'],
      exact: true,
    });
    navigate('/events');
  },
});
```

> 쿼리 무효화를 통해 모든 쿼리가 최신 데이터를 사용하도록 보장할 수 있다.

### 직접 이용해본 useQuery & useMutation

<details>
  <summary>코드 보기</summary>

```javascript
import { Link, Outlet, useNavigate, useParams } from 'react-router-dom';
import { useMutation, useQuery } from '@tanstack/react-query';

import Header from '../Header';
import { deleteEvent, fetchEvent, queryClient } from '../../util/http';
import LoadingIndicator from '../UI/LoadingIndicator';
import ErrorBlock from '../UI/ErrorBlock';

export default function EventDetails() {
  const navigate = useNavigate();
  const { id } = useParams();

  const {
    data: event,
    isPending,
    isError,
    error,
  } = useQuery({
    queryKey: [
      'events',
      {
        id,
      },
    ],
    queryFn: ({ signal }) => fetchEvent({ id, signal }),
    staleTime: 5000,
  });

  const { mutate } = useMutation({
    mutationFn: deleteEvent,
    onSuccess: () => {
      queryClient.invalidateQueries({
        queryKey: ['events'],
      });
      navigate('/events');
    },
  });

  function deleteCurrentEvent() {
    mutate({
      id,
    });
  }

  return (
    <>
      <Outlet />
      <Header>
        <Link to="/events" className="nav-item">
          View all Events
        </Link>
      </Header>
      <article id="event-details">
        {isPending && <LoadingIndicator />}
        {event && (
          <>
            <header>
              <h1>{event.title}</h1>
              <nav>
                <button onClick={deleteCurrentEvent}>Delete</button>
                <Link to="edit">Edit</Link>
              </nav>
            </header>
            <div id="event-details-content">
              <img src={`http://localhost:3000/${event.image}`} alt={event.title} />
              <div id="event-details-info">
                <div>
                  <p id="event-details-location">{event.location}</p>
                  <time dateTime="Todo-DateT$Todo-Time">
                    {event.date} @ {event.time}
                  </time>
                </div>
                <p id="event-details-description">{event.description}</p>
              </div>
              {isError && (
                <ErrorBlock
                  title="error"
                  message={error.info?.message || 'error message'}
                />
              )}
            </div>
          </>
        )}
      </article>
    </>
  );
}
```

- `axios`를 이용한 **HTTP** 통신

```javascript
export async function fetchEvent({ id, signal }) {
  try {
    const response = await axios.get(`http://localhost:3000/events/${id}`, {
      signal,
    });

    return response.data.event;
  } catch (err) {
    if (err.response) {
      const error = new Error('An error occurred while creating the event');
      error.code = err.response.status;
      error.info = err.response.data;
      throw error;
    } else {
      throw new Error('An error occurred while fetching the events');
    }
  }
}

export async function deleteEvent({ id }) {
  try {
    const response = await axios.delete(`http://localhost:3000/events/${id}`);

    return response.data;
  } catch (err) {
    if (err.response) {
      const error = new Error('An error occurred while creating the event');
      error.code = err.response.status;
      error.info = err.response.data;
      throw error;
    } else {
      throw new Error('An error occurred while fetching the events');
    }
  }
}
```
</details>

## 쿼리 무효화 후, 자동 다시 가져오기 비활성화

<img height="80%" width="80%" src="https://github.com/ash9river/React-Learned/assets/121378532/b6675b31-5f19-4d9e-85b0-58b462a7fb41" />

- `DELETE`가 일어난 후, 모든 관련 데이터의 쿼리가 무효화되지만 여전히 그 데이터 페이지에 있다.
- 이로 인해 리액트 쿼리가 즉시 이 세부 데이터의 다시 가져오기를 실행시킨다.(404 Not Found 유발)
- 이 동작을 방지하려면 `invalidateQueries`에 `refetchType` 속성을 `none`으로 설정해야 한다.

```javascript
const { mutate } = useMutation({
  mutationFn: deleteEvent,
  onSuccess: () => {
    queryClient.invalidateQueries({
      queryKey: ['events'],
      refetchType: 'none',
    });
    navigate('/events');
  },
});
```

- 이를 통해 `invalidateQueries`가 호출될 때, 기존 쿼리가 즉시 자동 실행을 막고, 기존 쿼리는 무효화되고 다음에 요청할 때 다시 실행된다.

##### 참고자료

[참고자료1](https://hjk329.github.io/react/react-query-queries/)
[참고자료2](https://www.timegambit.com/blog/digging/react-query/03)

