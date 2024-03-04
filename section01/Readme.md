## Building

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

## Eslint & Prettier with Window

- vscode extension으로 eslint와 prettier가 이미 설치되어있어야 한다.

1. eslint의 airbnb 의존성 확인

```
yarn info eslint-config-airbnb peerDependencies
```
  - 결과는 다음과 같다.

```
yarn info v1.22.19
{
  eslint: '^7.32.0 || ^8.2.0',
  'eslint-plugin-import': '^2.25.3',
  'eslint-plugin-jsx-a11y': '^6.5.1',
  'eslint-plugin-react': '^7.28.0',
  'eslint-plugin-react-hooks': '^4.3.0'
}
```

2. 의존성 설치

```
yarn add -D eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks
```

  - 설치시 `package.json`의 개발자 의존성에 다음과 같이 설치된다.

```json
"devDependencies": {
  "eslint": "^8.56.0",
  "eslint-config-airbnb": "^19.0.4",
  "eslint-plugin-import": "^2.29.1",
  "eslint-plugin-jsx-a11y": "^6.8.0",
  "eslint-plugin-react": "^7.33.2",
  "eslint-plugin-react-hooks": "^4.6.0"
}
```

3. 추가 패키지 설치

```
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```

4. 루트 폴더에 `.eslintrc.js`를 만든다.

- 내 코드 컨벤션이다.(타입스크립트는 추후 추가 설정 예정)

```javascript
module.exports = {
  env: {
    browser: true,
    es6: true,
    node: true,
  },
  extends: ['airbnb', 'plugin:prettier/recommended'],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 2020,
    sourceType: 'module',
  },
  plugins: ['react', 'prettier'],
  rules: {
    'react/react-in-jsx-scope': 0,
    'react/prefer-stateless-function': 0,
    'react/jsx-filename-extension': 0,
    // 'react/jsx-one-expression-per-line': ['error', { allow: 'literal' }],
    'no-nested-ternary': 0,
    // 'prettier/prettier': 'off',
    'import/no-named-as-default': 0,
    'import/prefer-default-export': 'off',
    'no-unused-vars': 'off',
    // '@typescript-eslint/no-used-vars': ['error'],
    'react/button-has-type': 'off',
    'react/prop-types': 'off',
    'jsx-a11y/label-has-associated-control': 'off',
    'react/jsx-props-no-spreading': 'off',
    'react/jsx-no-bind': 'off',
    'default-param-last': 0,
    'no-param-reassign': [
      'error',
      {
        props: true,
        ignorePropertyModificationsFor: ['state'],
      },
    ],
    'import/no-extraneous-dependencies': 'off',
  },
};
```

5. `.prettierrc` 파일 만든다.

```json
{
  "endOfLine": "auto",
  "singleQuote": true,
  "semi": true,
  "useTabs": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 80,
  "arrowParens": "always"
}
```

6. vscode settings

![image](https://github.com/ash9river/React-Learned/assets/121378532/31c4671f-89cb-450a-b9fd-1c7c7d5d3fee)

![image](https://github.com/ash9river/React-Learned/assets/121378532/1c68844a-e812-4bb5-bb39-906fb5b31037)

- 최종 의존성

```json
"devDependencies": {
  "eslint": "^8.56.0",
  "eslint-config-airbnb": "^19.0.4",
  "eslint-config-prettier": "^9.1.0",
  "eslint-plugin-import": "^2.29.1",
  "eslint-plugin-jsx-a11y": "^6.8.0",
  "eslint-plugin-prettier": "^5.1.3",
  "eslint-plugin-react": "^7.33.2",
  "eslint-plugin-react-hooks": "^4.6.0",
  "prettier": "^3.2.3"
}
```

- 만약에 `.eslintrc.json`으로 만든다면 오류가 뜰 수 있다.
- 위 설정들은 내가 입맛대로 넣은 설정을 추가했다.

```
[eslint] Cannot read config file: 주소
Error: Unexpected token } in JSON at position 76
```

### 디렉토리

![image](https://github.com/ash9river/React-Learned/assets/121378532/e322bce2-0bd3-448e-be9c-f65cb3a0c5e8)

### 추가 의존성

```
yarn add @babel/plugin-proposal-private-property-in-object --dev
```
```
yarn add redux react-redux
```
```
yarn add react-router-dom
```

### 서버 배포시 설정

```javascript
settings: {
  'import/resolver': {
    node: {
      extensions: ['.js', '.jsx', '.ts', '.tsx'],
    },
  },
},
```

```javascript
module.exports = {
  env: {
    browser: true,
    es6: true,
    node: true,
  },
  extends: ['airbnb', 'plugin:prettier/recommended'],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 2020,
    sourceType: 'module',
  },
  plugins: ['react', 'prettier'],
  rules: {
    'react/react-in-jsx-scope': 0,
    'react/prefer-stateless-function': 0,
    'react/jsx-filename-extension': 0,
    // 'react/jsx-one-expression-per-line': ['error', { allow: 'literal' }],
    'no-nested-ternary': 0,
    // 'prettier/prettier': 'off',
    'import/no-named-as-default': 0,
    'import/prefer-default-export': 'off',
    'no-unused-vars': 'off',
    // '@typescript-eslint/no-used-vars': ['error'],
    'react/button-has-type': 'off',
    'react/prop-types': 'off',
    'jsx-a11y/label-has-associated-control': 'off',
    'react/jsx-props-no-spreading': 'off',
    'react/jsx-no-bind': 'off',
    'default-param-last': 0,
    'no-param-reassign': [
      'error',
      {
        props: true,
        ignorePropertyModificationsFor: ['state'],
      },
    ],
    'import/no-extraneous-dependencies': 'off',
  },
  settings: {
    'import/resolver': {
      node: {
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
    },
  },
};
```

# Building wiht Typescript

- 수정중

```
yarn create react-app capstone-design --template typescript
```

```
yarn add -D eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks
```

```
yarn add -D eslint-config-airbnb-typescript
```

```
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```

- `.eslintrc.js`

```
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint', 'prettier'],
  extends: [
    'airbnb',
    'plugin:react/recommended',
    'plugin:jsx-a11y/recommended', 
    'plugin:import/errors',
    'plugin:import/warnings',
    'plugin:@typescript-eslint/recommended',
    'plugin:prettier/recommended',
  ],
  rules: {
    'linebreak-style': 0,
    'import/prefer-default-export': 0,
    'import/extensions': 0,
    'no-use-before-define': 0,
    'import/no-unresolved': 0,
    'react/react-in-jsx-scope': 0,
    'import/no-extraneous-dependencies': 0,
    'no-shadow': 0,
    'react/prop-types': 0,
    'react/jsx-filename-extension': [
      2,
      { extensions: ['.js', '.jsx', '.ts', '.tsx'] },
    ],
    'jsx-a11y/no-noninteractive-element-interactions': 0,
    '@typescript-eslint/explicit-module-boundary-types': 0,
  },
  settings: {
    'import/resolver': {
      node: {
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
    },
  },
};
```

- `.prettierrc`

```
{
  "endOfLine": "auto",
  "singleQuote": true,
  "semi": true,
  "useTabs": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 80,
  "arrowParens": "always",
  "orderedImports": true,
  "bracketSpacing": true,
  "jsxBracketSameLine": false
}
```

- `tsconfig.json`

```
{
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "baseUrl": "./src",
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": [
    "src"
  ]
}
```

- `.vscode` 폴더 생성, `setting.json` 파일 생성

```
{ 
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "window.zoomLevel": -1,
  "editor.formatOnSave": true,
  "[javascript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[javascriptreact]":{ "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[typescriptreact]": { "editor.defaultFormatter": "esbenp.prettier-vscode" }
}
``` 
