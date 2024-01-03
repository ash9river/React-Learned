## 처음 시작

index.js에서 document.getElementById('root')가 선택되면 public폴더의 index.html에서 <div id="root"></div>가 선택된다. 
그 후, 리액트가 이 div와 그 내용을 제어한다.

```
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```
