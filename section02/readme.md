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

# function

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

### 배열 순회

```javascript
for(const element of array){
    console.log(element);
}
```
### 배열 분해

```javascript
const [] = ["ash","9","river"];
const [top,mid,bottom] = ["ash","9","river"];
```

### 객체 분해

```javascript
const {name,age}={
    name:"ash9river",
    age:24,
};
```

## Destructuring

### 배열 디스트럭처링

```javascript
// 기본 배열 디스트럭처링
const numbers = [1, 2, 3];
const [a, b, c] = numbers;
console.log(a); // 1
console.log(b); // 2
console.log(c); // 3

// 생략 가능한 요소
const [x, , y] = numbers;
console.log(x); // 1
console.log(y); // 3
```

### 객체 디스트럭처링

```javascript
// 기본 객체 디스트럭처링
const person = { name: 'John', age: 30 };
const { name, age } = person;
console.log(name); // John
console.log(age);  // 30

// 다른 변수명으로 할당
const { name: personName, age: personAge } = person;
console.log(personName); // John
console.log(personAge);  // 30

// 기본 값 설정
const { city = 'Unknown' } = person;
console.log(city); // Unknown
```

- 중첩 객체 디스트럭처링

```javascript
const user = {
    name: 'Ssong',
    address: {
        city: 'seoul'
    }
};

 //adress 프로퍼티 키로 객체를 추출하고 이 객체의 city 프로퍼티 키로 값을 추출
 const { address: { city } } = user;
 console.log(city); //seoul

 // 스프레드 연산자 ...을 사용할 수 있다.
 const { x, ...rest } = { x: 1, y: 2, z: 3 };
 console.log(x, rest) = 1, { y: 2, z: 3 }
```

### 함수 매개변수 목록에서 디스트럭처링

- `비구조화 할당`
- 함수가 객체를 포함하는 매개 변수를 할당하는 경우, 객체 프로퍼티를 `꺼내어` 지역 범위 변수로 사용할 수 있도록 함수를 비구조화 할 수 있다.

  
### 함수 매개변수 디스트럭처링 예시

```javascript
// 함수 매개변수에서 배열 디스트럭처링
const printNumbers = ([x, y, z]) => {
  console.log(x, y, z);
};
printNumbers([1, 2, 3]); // 1 2 3

// 함수 매개변수에서 객체 디스트럭처링
const printPerson = ({ name, age }) => {
  console.log(name, age);
};
printPerson({ name: 'Alice', age: 25 }); // Alice 25
```

## 스프레드 연산자(전개 연산자)

- `...` : ES6에서 도입된 스프레드 문법(spread syntax) 
- 하나로 뭉쳐 있는 여러 값들의 집합을 펼쳐서 개별적인 값들의 목록으로 만든다.

```javascript
console.log(...[1,2,3]); // 1 2 3
console.log(...'hello, World'); // h e l l o ,   W o r l d
```
- 스프레드 문법을 사용할 수 있는 대상은 순회할 수 있는 `이터러블`에 한정된다.
- Array, String, Map, Set은 `이터러블`이다.
  
> 스프레드 문법의 결과는 값이 아니다. <br/>
> 즉 스프레드 문법은 값을 생성하는 연산자가 아니다. 따라서 스프레드 문법의 결과는 변수에 할당할 수 없다.

### 함수 호출문의 인수 목록에서 사용

```javascript
const arr = [1, 2, 3];
console.log(Math.max(...arr)); // 3
```

### 배열 리터럴 내부에서 사용

- 서로 다른 배열을 하나의 배열로 결합할 수 있다.
  
```javascript
const arr = [...[1, 2], ...[3, 4]];
console.log(arr); // [1, 2, 3, 4]
```

- `splice` 함수로 새로운 배열을 만들 때 활용할 수 있다.

```javascript
const arr1 = [1, 4];
const arr2 = [2, 3];
arr1.splice(1, 0, arr2); // 배열 자체가 들어감. [1, [2, 3], 4]
arr1.splice(1, 0, ...arr2); // ES6 
console.log(arr1); //배열 내부의 값이 들어감. [1, 2, 3, 4]
```

- 스프레드 문법을 이용해 배열을 `복사`할 수 있다.

```javascript
const origin = [1, 2];
const copy = [...origin];

console.log(copy); // [1, 2]
console.log(copy === origin); // false
```

> ❗ 스프레드 연산자는 `depth`가 `1`일 경우에만 깊은 복사이고, `depth`가 `2`이상이면 얕은 복사를 수행한다.

### 이터러블을 배열로 변환

```javascript
function sum() {
  return [...arguments].reduce((pre, cur) => pre + cur, 0);
}
console.log(sum(1, 2, 3)); //6
/*
return [...arguments].reduce((pre, cur) => pre + cur, 0); : arguments를 배열로 변환하고, 배열의 reduce 메서드를 사용하여 모든 요소를 더한 값을 반환.
arguments: 함수 내부에서 사용 가능한 특별한 변수로, 함수에 전달된 인자. 이터러블이면서, 유사 배열 객체.

[...arguments]: 전개 연산자(...)를 사용하여 arguments를 배열로 변환.
.reduce((pre, cur) => pre + cur, 0): 배열의 모든 요소를 더하는 reduce 메서드를 사용.

pre: 누적된 합계.
cur: 현재 처리 중인 배열의 요소.
0: 초기 누적 값으로 0을 설정.
*/
```

- Rest 파라미터를 활용하는 것이 더 간편하다.

```javascript
//Rest 파라미터는 args는 함수에 전달된 인수들의 목록을 배열로 전달받는다.
const sum = (...args) => args.reduce((pre, cur) => pre + cur, 0);
console.log(sum(1, 2, 3,)); // 6
```

> ❗ 이터러블이 아닌 유사 배열 객체는 스프레드 문법의 대상이 될 수 없다. <br/>
> ✅ ES6에서 도입된 `Array.from` 메서드를 사용하여 이터러블이 아닌 유사 배열 객체를 배열로 변경 할 수 있다.

### 객체 리터럴 내부에서 사용

```javascript
// 객체 병합. 프로퍼티가 중복되는 경우 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = { ... { x:1, y:2 }, ... { y:10, z:3} };
console.log(merged); // { x:1, y:10, z:3 }

// 특정 프로퍼티 변경
const changed = { ... {x: 1, y: 2 }, y: 100 };
console.log(changed); // { x:1, y:100 }

// 프로퍼티 추가
const added = { ... {x:1, y:2}, z: 0};
console.log(added) // { x:1, y:2, z:0 }
```

### Rest 파리미터 vs 스프레드 

- Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받기 위해 매개변수 이름 앞에 ...을 붙이는 것이다.
- 스프레드 문법은 여러 개의 값이 하나로 뭉쳐 있는 배열과 같은 이터러블을 펼처서 개별적인 값들의 목록을 만드는 것이다.
- 따라서 Rest 파라미터와 스프레드 문법은 서로 반대의 개념이다.





