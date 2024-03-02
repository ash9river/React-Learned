# Typescript

## 타입스크립트

- 타입스크립트는 자바스크립트의 `superset` 언어이다.
  - 자바스크립트를 기반으로 하나, 자바스크립트보다 더 확장된 프로그래밍 언어이다.
- 타입스크립트는 리액트와는 다르게 자바스크립트 라이브러리가 아니다.
  - 이로 하여금 자바스크립트의 기존 기능을 기반으로 새로운 기능을 만들거나, 기존 기능을 확장하지 않는다.
  - 대신에 자바스크립트의 주요 문법보다 확장된 문법을 가진다. 
- 타입스크립트는 정적 타입(statically Typed)의 특징을 갖는다.
  - 자바스크립트는 동적 타입(dynamically Typed) 언어임을 생각하면 대조적이다. 

### 원시 타입(Primitives Type)

- 자바스크립트의 기본형 데이터는 숫자형, 문자열, 불리언(boolean)형, `null`, `undefined`이 있다.

```javascript
let age: number;

age = 18;

let myAge: number = 26;

let userName: string = 'ash9river';

let isInstructor: boolean = true;
```

- `null`과 `undefinde`는 자기 자신의 값만 받을 수 있다.([설정 값에 따라 아닐 수도 있음](https://yamoo9.gitbook.io/typescript/types/null-undefined))

```javascript
let isNull: null = null;

let isUndefined: undefined = undefined;
```

### 배열 및 객체 타입

- 배열에는 단순히 대괄호`[]`를 추가.
- 객체는 지정을 안할 시에는 원시 타입으로 `any`가 지정되나, 자바스크립트랑 다를 바가 없어서 좋지 않은 방법이다.
- 또한, 객체에 타입 지정을 하지 않은 필드는 저장할 수 없다.

```javascript
// arrays

let hobbies: string[];

hobbies = ['Sports', 'Cooking', 'Games'];

// bad

let person;

person = {
  name: 'ash9river',
  age: 26,
};

// good

let man: {
  name: string;
  age: number;
};

man = {
  name: 'ash9river',
  age: 26,
};

// object array

let people: {
  name: string;
  age: number;
}[];
``` 

### 타입 추론(Type Inference)

- 타입스크립트는 가능한 많은 타입을 유추할려고 한다.
- 명시적인 타입 표기가 없어도 타입 추론을 통해 타입을 지정한다.
- 타입 추론을 이용해서 코드를 작성하는게 권장된다.(형식 지정에 따른 불필요한 코드를 줄이기 위함)

```javascript
let course = 'React - The Complete Guide';

course = 1234; // error
```

### 유니온 타입(Union Type)

- 하나의 변수에 두 개 이상의 타입을 지정할 수 있게 해주는 기능으로, 유니온 타입 기능이라 한다.

```javascript
let course: string | number = 'React - The Complete Guide';

course = 1234;
```

### 타입 별칭(Type Aliases)




