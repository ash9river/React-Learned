# Debugging

## React Type Error

### stack trace

<img src="https://github.com/ash9river/React-Learned/assets/121378532/c9f18380-eec7-4657-8631-5bfd04d8ffc8" alt="image" width="60%" height="60%">


2 Uncaught TypeError: Cannot read properties of undefined Results.jsx:8 (reading 'valueEnd0fYear')

- 에러 발견, undefined value가 있음
- stack trace 읽기

### 분석하기

- 모든 오류가 오류 메세지를 제공하지 않는다.
- 작성한 코드에 논리적인 오류가 있을 수도 있다.
- 항상 논리적으로 생각하려 해야 한다.
- 실제로 실행과정에서 사용되는 값들을 사용해 실행되는 시점을 살펴봐야 한다.


## 이 강의를 들으면서 스스로 발견하고 고친 오류들

1. [틱택토 게임](https://github.com/ash9river/React-Learned/tree/main/section04#%EA%B0%95%EC%9D%98%EC%97%90%EC%84%9C-%EA%B3%A0%EC%B9%98%EC%A7%80-%EC%95%8A%EC%9D%80-%EC%98%A4%EB%A5%98)
2. [Investment Calculator](https://github.com/ash9river/React-Learned/tree/main/section05)

