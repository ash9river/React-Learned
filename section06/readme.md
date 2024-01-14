# CSS with React

## Vanilla CSS

### 장점

- jsx에서 css 코드 분리
- 내가 작업하고 있는 jsx 코드를 다른 사람이 스타일링할 수 있다.(충돌방지 가능)

### 단점

- 다른 컴포넌트 간에 스타일 충돌이 발생할 수 있다.

## InLine Style

- style 객체를 키와 값으로 전달하는 방식

```javascript
<p style={{color:"red"}} >My Style</p>
```

### 장점

- 추가하기 쉽다.
- 추가하는 요소에만 영향을 미치고 다른 요소에는 영향을 미치지 않는다.

### 단점

- 모든 요소를 개별적으로 스타일해야한다.
- 협업시 충돌이 일어나기 쉽다.


## 동적 스타일
<!--바닐라 하겐다즈 먹고싶다-->
### 바닐라

- className 동적 부여

```javascript
<p className={isValid?valid:inValid} >My Style</p>
```


### 인라인 방식


```javascript
const isValid=conditionWhatYouWant;

<p style={{color:isValid?"red":"blue"}} >My Style</p>
```














