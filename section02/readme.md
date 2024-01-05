# Javascript

1. In the Browser that can be executed.
2. On any Computer, also, can be executed using node.js, etc...
3. On mobile Devices...

# Adding Javascript code to a website

1. Between <script> tag
2. Via <script> import

# import & export


## import

- `import` : 다른 모듈에서 내보낸 변수, 함수, 클래스 등을 현재 파일에서 사용할 수 있게 가져오는 데 사용된다.

```javascript
import { add, subtract } from './math';
//main.js파일
console.log(add(5, 3));      // 8
console.log(subtract(8, 3)); // 5
```

위의 코드에서 `main.js` 파일에서 `math.js` 모듈에서 내보낸 add와 subtract 함수를 가져와 사용하고 있다.

## export

```javascript
//math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
```

위의 코드에서 `add`와 `subtract` 함수를 `math.js` 모듈에서 내보내고 있다.

## export default

- 모듈은 export default라는 특별한 문법을 지원한다. export default를 사용하면 `해당 모듈엔 개체가 하나만 있다`는 사실을 명시한다.

```javascript
export default App
```

