# Debugging

## React Type Error

## stack trace

- `stack trace` 읽기

<img src="https://github.com/ash9river/React-Learned/assets/121378532/c9f18380-eec7-4657-8631-5bfd04d8ffc8" alt="image" width="60%" height="60%">


> 2 Uncaught TypeError: Cannot read properties of undefined Results.jsx:8 (reading 'valueEnd0fYear')

- 에러 발견, `undefined value`가 있음


## 분석하기

- 모든 오류가 오류 메세지를 제공하지 않는다.
- 작성한 코드에 논리적인 오류가 있을 수도 있다.
- 항상 논리적으로 생각하려 해야 한다.
- 실제로 실행과정에서 사용되는 값들을 사용해 실행되는 시점을 살펴봐야 한다.

## Strict Mode

- React가 제공하는 컴포넌트.
- 애플리케이션 어디서나 사용가능하다. 컴포넌트 트리 깊은 곳에서 `StrictMode`를 `import` 해서 사용가능하다.
- 모든 컴포넌트 함수를 **개발 단계에서만** 두 번 실행하여 에러를 좀 더 발견하기 쉽게 된다.

## 이 강의를 들으면서 스스로 발견하고 고친 오류들

1. [틱택토 게임](https://github.com/ash9river/React-Learned/tree/main/section04#%EA%B0%95%EC%9D%98%EC%97%90%EC%84%9C-%EA%B3%A0%EC%B9%98%EC%A7%80-%EC%95%8A%EC%9D%80-%EC%98%A4%EB%A5%98)
2. [Investment Calculator](https://github.com/ash9river/React-Learned/tree/main/section05)

- 알고리즘 문제 풀이할 때, 스스로 테스트 코드를 작성해본 경험이 도움이 되는 것 같다.
