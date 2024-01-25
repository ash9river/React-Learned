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

> 만약 리렌더링이 일어나고, 재생성이 일어나지 않을 때, 컴포넌트를 재생성 시키고 싶으면 컴포넌트의 `key` 속성을 이용한다.

### 예시

- 자식 컴포넌트로 `progress bar`가 있는 상황에서, `useCallback`을 이용해서, **전달되는 함수의 객체가 재생성하지 않게 되었다.**
- 부모 컴포넌트의 `userAnswer` **상태**가 변경되어서 리렌더링이 발생했을 때, 자식컴포넌트의 `progress bar`의 **상태**가 변하지 않는 상황이 발생했다.
- 그 이유는 부모 컴포넌트가 리렌더링이 발생하여도 자식컴포넌트로 전달되는 값이 변경되지 않기 때문에, 자식 컴포넌트가 리렌더링은 발생하지만 재생성 되지 않아, 자식 컴포넌트의 `remainingTime` **상태**가 초기화되지 않기 때문이다.
- 그 해결법으로 하위 컴포넌트에 `key` 값을 주어서 재생성을 일으키도록 강제한다.

> 리렌더링은 컴포넌트의 UI를 갱신하고, 상태와 속성 값의 변경 등을 반영하는 과정이지만, 상태의 초기값이나 컴포넌트가 처음 마운트되었을 때의 설정은 리렌더링 시에 재설정되지 않는다. <br/>
> ❗❗❗ 초기 설정은 컴포넌트가 처음 마운트될 때만 이루어지고, 리렌더링이 발생하더라도 초기화되지 않는다.
```javascript
import { useState, useCallback } from 'react';

import QUSETIONS from '../question';
import quizCompleteImg from '../assets/quiz-complete.png';
import QuestionTimer from './QuestionTimer';

export default function Quiz() {
  const [userAnswers, setUserAnswers] = useState([]);

  const activeQuestionIndex = userAnswers.length;

  const handleSelectAnswer = useCallback(function handleSelectAnswer(
    selectedAnswer,
  ) {
    setUserAnswers((prev) => {
      return [...prev, selectedAnswer];
    });
  }, []);

  const handleSkipAnswer = useCallback(
    () => handleSelectAnswer(null),
    [handleSelectAnswer],
  );

  return (
    <div id="quiz">
      <div id="question">
        <QuestionTimer
          key={activeQuestionIndex}
          timeout={5000}
          onTimeout={handleSkipAnswer}
        />
      </div>
    </div>
  );
}

```

```javascript
import { useEffect, useState } from 'react';

export default function QuestionTimer({ timeout, onTimeout }) {
  const [remainingTime, setRemainingTime] = useState(timeout);

  useEffect(() => {
    const timer = setTimeout(onTimeout, timeout);

    return () => {
      clearTimeout(timer);
    };
  }, [onTimeout, timeout]);

  useEffect(() => {
    const interval = setInterval(() => {
      setRemainingTime((prevRemainingTime) => prevRemainingTime - 100);
    }, 100);

    return () => {
      clearTimeout(interval);
    };
  }, []);

  return <progress id="question-time" max={timeout} value={remainingTime} />;
}
```

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
