# Javascript

1. In the Browser that can be executed.
2. On any Computer, also, can be executed using node.js, etc...
3. On mobile Devices...

# Adding Javascript code to a website

1. Between <script> tag
2. Via <script> import

<br/>

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
<br/>
<br/>

## Variables

1. `Reusability`
2. `Readability`

## function

```javascript
function combine(a,b,c){
    return a*b/c;
}
```

## 익명함수 export

```javascript
export default function(userName,message){
    return userName+message;
}
```

또는

```javascript
export default (userName,message)=>{
    return userName+message;
}
```
## 화살표 함수

- 화살표 함수를 다룰 때에는 `구문 단축키`를 사용할 수 있다.
1. 화살표 함수가 정확히 하나의 매개변수만 사용하는 경우, 묶는 괄호를 생략할 수 있다.
    - 함수에 매개변수가 없는 경우에는 괄호를 생략할 수 없다.
    - 함수가 둘 이상의 매개변수를 받는 경우에도 괄호를 생략하면 안된다.
      
```javascript
const whatIsYourName = userName => {
    return userName;
}
```

2. 화살표 함수에 반환문 외에 다른 구문이 없으면, `return`과 중괄호를 생략할 수 있다.

```javascript
const plusThree = number => number+3;
```

3. 객체만 반환하는 경우
    - 자바스크립트는 중괄호를 js 객체를 생성하는 코드가 아니라, 함수 본문 wrapper로 생각한다.
    - 객체를 생성하고 반환하려면, 객체와 중괄호를 추가 괄호로 감싸야한다.
    - 자바스크립트는 이를 중괄호가 함수 본문을 정의하는 것이 아닌, 객체를 생성하기 위한 것이라고 이해한다.
    - 따라서, 객체가 반환된다.
      
```javascript
const MakeAge = (number) => {
    return ({age:number});
}
```

<br/>

## 배열

- map은 기존 배열 수정 x, 새로운 원소로 반환

```javascript
myArray.findIndex(item=>item==='the index of item which i wanna find');

myArray.map((item)=>{
    return item+'!';
});

myArray.map(item=>item+'?');

const newArray=myArray.map((item)=>{
    return {text:item};
});

const newArray2=myArray.map(item=>({text:item}));
```

### 배열 분해

```javascript
const [] = ["ash","9","river"];
const [top,mid,bottom] = ["ash","9","river"];
```

















