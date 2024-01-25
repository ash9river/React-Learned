# Make a Quiz App

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
