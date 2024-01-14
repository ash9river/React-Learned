# React Core 2

## 동적 호출 관련

- 분명 앞서 정적으로 만든 것은 디폴트 값이 제대로 발생하는데, 동적인 값은 제대로 호출이 안되어서 뭐가 문제지 싶었다.

```javascript
import LableAndInput from "./LableAndInput";

export default function UserInput({ myVal, handleChangeValue }) {
  function handleChange(whatIWantToChagne, e) {
    e.preventDefault();
    handleChangeValue(whatIWantToChagne, e.target.value);
  }
  return (
    <section id="user-input">
      <div className="input-group">
        <p>
          <label htmlFor="initial-investment">initial-investment</label>
          <input
            type="number"
            id="initial-investment"
            value={myVal.initialInvestment}
            onChange={(e) => handleChange("initialInvestment", e)}
            required
          />
        </p>
        <LableAndInput
          myVal={myVal}
          nameOfValue="annualInvestment"
          handleChange={handleChange}
        />
        <LableAndInput
          myVal={myVal}
          nameOfValue="expectedReturn"
          handleChange={handleChange}
        />
        <LableAndInput
          myVal={myVal}
          nameOfValue="duration"
          handleChange={handleChange}
        />
      </div>
    </section>
  );
}
export default function LableAndInput({ myVal, nameOfValue, handleChange }) {
  return (
    <p>
      <label htmlFor={nameOfValue}>{nameOfValue}</label>
      <input
        type="number"
        id={nameOfValue}
        value={myVal.nameOfValue}
        onChange={(e) => handleChange(nameOfValue, e)}
        required
      />
    </p>
  );
}
```

- javascript에서 동적인 속성 이름을 사용할려면 .이 아니라 []를 사용해야 한다.
- 항상 해당 속성의 이름이 고정된 것으로 인식되기 때문에, 대괄호 접근법을 사용해야 한다.

```javascript
export default function LableAndInput({ myVal, nameOfValue, handleChange }) {
  return (
    <p>
      <label htmlFor={nameOfValue}>{nameOfValue}</label>
      <input
        type="number"
        id={nameOfValue}
        value={myVal[nameOfValue]}
        onChange={(e) => handleChange(nameOfValue, e)}
        required
      />
    </p>
  );
}
```
