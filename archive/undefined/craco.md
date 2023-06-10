---
description: craco
---

# craco 절대경로 설정

### craco란?

CRA에 config 설정을 덮어쓰기 위한 패키지. 기존 cra 설정에서 내가 적용한 항목들을 오버라이딩 한다고 보면됨!

#### tsconfig.json에서 설정안하고 craco 라이브러리를 사용하는 이유

tsconfig.json을 사용해서 절대경로 설정을 하려면 cra eject해서 숨겨진 웹팩 설정을 바꿔야한다. 그걸 하지 않기 위해서 craco를 사용하여 cra 내부 설정을 건드리지 않고 추가 설정을 오버라이딩하여 경로 설정을 할 수 있다.

***

#### craco 설치 명령어

```jsx
$ npm install --save-dev @craco/craco craco-alias
```

#### craco.config.js

```jsx
const CracoAlias = require('craco-alias');

module.exports = {
  plugins: [
    {
      plugin: CracoAlias,
      options: {
        source: 'tsconfig',
        tsConfigPath: 'tsconfig.paths.json',
      },
    },
  ],
};
```

#### tsconfig.json

```jsx
{
	...추가 설정,
	"extends": "./tsconfig.paths.json",
}
```

#### tsconfig.paths.json

```jsx
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@assets/*": ["src/assets/*"],
      "@components/*": ["src/components/*"],
      "@constants/*": ["src/constants/*"],
      "@common/*": ["src/components/common/*"],
      "@utils/*": ["src/utils/*"],
      "@hooks/*": ["src/hooks/*"],
      "@pages/*": ["src/pages/*"],
      "@styles/*": ["src/styles/*"]
    }
  }
}
```

* tsconfig.paths.json 안쓰고 tsconfig.json에서도 `“paths” : {}` 설정 가능

#### package.json

```jsx
"scripts": {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test",
  },
```

***

### eslint가 craco 설정 막을 경우

둘 중 하나 설정하면 해당 파일 eslint 무시

#### 1. eslintignore

```json
craco.config.js
```

#### 2. package.json

```json
{
  "eslintIgnore": ["./craco.config.js"]
}
```

***

#### trouble shooting

lint error

```jsx
'ignorePatterns' is invalid:
Severity should be one of the
following: 0 = off, 1 = warn, 2 = error (you passed '"./craco.config.js"').
```

***

### 참고 자료

[ESLint - Ignoring Code - ESLint를 사용하여 코드 문제를 식별하고 수정할 수 있습니다.기본적으로](https://runebook.dev/ko/docs/eslint/user-guide/configuring/ignoring-code)

[React Typescript 절대 경로 설정하기 (Craco)](https://velog.io/@alstnsrl98/React-Typescript-%EC%A0%88%EB%8C%80-%EA%B2%BD%EB%A1%9C-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-Craco)

[\[TS\] CRA에서 tsconfig paths로 절대경로 설정하기 (react-app-alias)](https://tesseractjh.tistory.com/232)
