# React Core

## React.createElement

- **React**가 요소들 제어한다.

## JSX

- **JSX**는 무조건 하나의 요소만 반환해야 한다.
- 대책으로 빈 태그로 모두 감싼다.

```javascript
  return (
    <>
    </>
  )
```

- `Fragment`도 가능하다. (이게 원조)
  
```javascript
  return (
    <Fragment>
    </Fragment>
  )
```

## 컴포넌트 분리

- 컴포넌트를 잘게 쪼개서 리렌더링을 최적화시킴
- `Feature`와 `State`로 컴포넌트 분리

```javascript
import Header from "./components/Header";
import Examples from "./components/Examples";
import CoreConcepts from "./components/CoreConcepts";

const App = () => {
  return (
    <>
      <Header />
      <main>
        <CoreConcepts />
        <Examples />
        <h2>Time to get started!</h2>
      </main>
    </>
  );
};

export default App;
```
## 내부요소로 props 전달

- 커스텀 컴포넌트에 자동으로 연결해주지 않음

### forward props & proxy props

- javascript의 `Rest Property`를 사용하여 가져온다.

```javascript
export default function Section({ title, children, ...props }) {
  return (
    <>
      <section {...props}>
        <h2>{title}</h2>
        {children}
      </section>
    </>
  );
}
```

## 이미지 저장소

- 빌드 프로세스에 의해 처리되지 않는 이미지는 `public/` 폴더를 사용해야 한다.
  - 예를 들면 `index.html` 파일이나 파비콘과 같은 이미지.
- 컴포넌트 내에서 사용되는 이미지는 일반적으로 `src/` 폴더에 저장되어야 한다.

### public/

- 파일이 프로젝트 개발 서버 및 빌드 프로세스에 의해 공개적으로 제공.
- 브라우저 내에서 직접 방문할 수 있다.
- 다른 파일에 의해 요청될 수도 있다.
- `require`를 통해 이미지를 표현할 수 있다.


### src/assets/

- 파일이 공개적으로 제공되지 않는다. (웹사이트 방문자가 접근 불가)
- 코드 파일에 가져온 이미지는 빌드 프로세스에 의해 인식되어 최적화되며 웹사이트에 제공하기 직전에 `public/` 폴더에 `삽입`된다.
- 가져온 이미지는 참조한 위치에서 자동으로 링크가 생성되어 사용된다.
- `import`를 통해 이미지를 표현할 수 있다.

## state 변경

- React에서 상태를 변환하고 싶을 때, 해당 상태의 이전 값을 변경하는 경우 업데이트 함수를 만들어야 한다.
- 업데이트 함수를 만들지 않고 상태를 변경하면, 즉각적으로 실행되는 것이 아니라 미래에 수행하기 위해 `상태 변경 스케쥴을 조율`하는 것이다.
  
```javascript
function handleEditClick() {
  setIsEditing(!isEditing);
}
```

- 차이는 몇 밀리초밖에 나지 않지만, 즉각적인 차이가 나타나느냐 아니냐가 중요하다.
- 함수 형태를 사용하면, **리액트가 이 상태는 가장 최신 버전이다**라고 보장할 수 있다. 

```javascript
function handleEditClick() {
  setIsEditing(editing=>!editing);
}
```

## state 불변성을 지키면서 업데이트

- 불변성을 지키지 않은 코드
  
```javascript
function handleSelectSquare(rowIndex,colIndex,symbol){
  setGameBoard((prevGameBoard)=>{
    prevGameBoard[rowIndex][colIndex]=symbol;
    return prevGameBoard;
  })
}
```

- 불변성을 지킨 코드
- 스프레드 연산자를 사용해서 배열 복사, 그 후 새로운 배열 return

```javascript
function handleSelectSquare(rowIndex,colIndex,symbol){
  setGameBoard((prevGameBoard)=>{
    const updatedBoard=[...prevGameBoard.map(innerArray=>[...innerArray])];
    updatedBoard[rowIndex][colIndex]=symbol;
    return updatedBoard;
  })
}
```

## state 끌어올리기

- 컴포넌트 리렌더링을 생각하면서 끌어올릴 state와 끌어올리지 않을 state를 구분해야 한다.

1. 자식 컴포넌트에서 state를 제거한다.
2. 공통 부모 컴포넌트에 하드 코딩된 데이터를 전달한다.
3. 공통 부모 컴포넌트에 state를 추가하고 이벤트 핸들러와 함께 전달한다.
   
### 양방향 바인딩

- parent와 child에서 데이터를 모두 업데이트 시킬 수 있도록, state 끌어올리기를 통해 구현한다.
- 부모 컴포넌트에서 자식 컴포넌트에 `state` 값과 `state`를 업데이트 시킬 수 있는 함수를 `props`로 전달한다.

## 강의에서 고치지 않은 오류

- 강의에서는 버튼을 빠르게 연타하면 Player 1이 이겨야 할 상황에서도 Player 2가 이기는 버그가 발생한다.
- GameOver의 css의 pop-in 애니메이션이 0.5초로 되어있어서, 0.5초만에 여러번 더 클릭할 수 있었다.
- 버튼 선택시 winner가 결정되었으면 버튼 비활성화를 추가하여 Player 2가 이기는 것을 막는다.

```javascript
<button
  onClick={() => onSelectSquare(rowIndex, colIndex)}
  disabled={playerSymbol !== null || winner !== null}
>
```

- 강의에서 주어진 css가 원인이다. 실제로 css의 애니메이션을 비활성화하면 disabled의 조건을 추가하지 않아도 버그가 나지 않는다.
  
```css
#game-over {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: rgba(40, 38, 23, 0.95);
  animation: pop-in 0.5s cubic-bezier(0.68, -0.55, 0.65, 0.52) forwards;
}
```












