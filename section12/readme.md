# Make a Quiz App

## 재생성과 리렌더링의 차이

### 재성성 (Remounting)

- 재성성은 컴포넌트가 완전히 파괴되고 다시 생성되는 과정.
- 주로 부모 컴포넌트에서 해당 자식 컴포넌트를 제거하고 다시 추가하는 경우에 발생.
- 부모 컴포넌트의 상태나 속성이 변경되어 자식 컴포넌트를 다시 마운트해야 하는 상황에서 발생.

### 리렌더링 (Rerendering)

- 리액트 컴포넌트가 상태(state) 또는 속성(props)의 변경으로 인해 다시 그려지는 과정.
- 리렌더링은 컴포넌트의 가상 돔(Virtual DOM)을 다시 계산하고, 변경된 부분만 실제 DOM에 반영하는 과정.
- 함수 컴포넌트에서는 상태(state)나 속성(props)이 변경될 때마다 컴포넌트가 리렌더링된다.
- 클래스 컴포넌트에서는 render 메서드가 호출되고, 가상 돔이 업데이트되어 실제 DOM에 반영된다.

> 만약 리렌더링이 일어나고, 재생성이 일어나지 않을 때, 컴포넌트를 재생성 시키고 싶으면 컴포넌트의 `key` 속성을 이용하는게 좋다.


## 불변성 유지하며 업데이트

```javascript
function handleSelectAnswer(selectedAnswer) {
  setUserAnswers((prev) => {
    return [...prev, selectedAnswer];
  });
}
```

### 간단한 자바스크립트 랜덤으로 섞기

```javascript
const shuffledAnswers = [...QUSETIONS[activeQuestionIndex].answers];
shuffledAnswers.sort((a, b) => Math.random() - 0.5);
```









ㅁ
