---
description: 프리티어 설정, es lint 설정
---

# prettier, es lint

### es lint 설치

```
npm eslint --lint
```

es lint를 설치하면 아래와 같은 질문이 나오고 아래 내용을 참고해서 답변하면 설치 완료

| 질문                                                 | 해석                             | 내 설정                              |
| -------------------------------------------------- | ------------------------------ | --------------------------------- |
| How would you like to use ESLint?                  | ESLint를 어떻게 사용하시겠습니까?          | To check syntax and find problems |
| What type of modules does your project use?        | 프로젝트에서 사용하는 모듈 유형은 무엇인가요?      | Javascript modules                |
| Which framework does your project use?             | 프로젝트에서 사용하는 프레임워크는 무엇인가요?      | React                             |
| Does your project use TypeScript?                  | 프로젝트에서 TypeScript를 사용하나요?      | NO                                |
| Where does your code run?                          | 코드는 어디에서 실행되나요?                | Browser                           |
| What format do you want your config file to be in? | 구성 파일의 형식을 어떤 형식으로 지정하고 싶으신가요? | Json                              |
| Which package manager do you want to use?          | 어떤 패키지 관리자를 사용하시겠습니까?          | npm                               |

* `eslint .` 하면 eslint를 실행 시킬 수 있다.

package.json \[script] 설정을 llint package.json 설정을 하고 npm run lint 해도 실행 가능

```
"scripts": {
    "lint": "eslint ."
}
```

standard 템플릿을 사용해서 lint 설치를 했는데 덮어 쓰고 싶거나 추가적인 설정을 하고싶으면 .eslintrc.json 파일에 `rules`  에다가 추가로 넣고 싶은 규칙을 적으면 된다. 템플릿과 설정한 룰이 겹친다면 나중에 설정한 규칙이 적용된다.



### 이번 프로젝트에서 설정한 룰

```
"rules": {
  "no-console": "warn",                          // 콘솔 출력문을 경고로 표시
  "react/react-in-jsx-scope": "off",              // JSX에서 React를 사용할 때 React import 확인하지 않음
  "react/prop-types": "warn",                     // React 컴포넌트의 prop 타입 정의를 경고로 표시
  "no-undef": "off",                              // 정의되지 않은 변수 사용에 대한 경고 미표시
  "brace-style": ["error", "stroustrup"],         // 중괄호 스타일을 stroustrup 스타일로 지정
  "comma-dangle": ["error", "always-multiline"],  // 여러 줄에 걸친 객체 또는 배열의 마지막 요소 뒤에 쉼표가 필요
  "comma-spacing": ["error", { "before": false, "after": true }], // 쉼표 앞에는 공백을 없애고, 쉼표 뒤에는 공백을 추가
  "eol-last": ["error", "always"],                // 파일의 끝에 개행문자가 있어야 함
  "indent": ["error", 2],                         // 들여쓰기에 2개의 공백 사용
  "keyword-spacing": ["error", { "before": true, "after": true }], // 키워드 앞뒤에 공백을 추가
  "no-multiple-empty-lines": ["error", { "max": 1 }], // 연속된 빈 줄은 1줄까지 허용
  "quotes": ["error", "single"],                   // 문자열 리터럴에는 작은따옴표를 사용
  "semi": ["error", "always"]                      // 문장의 끝에 세미콜론을 사용
}

```



### esllint-config-prettier

```
npm install -D eslint-config-prettier
```

eslint와 prettier간 충돌 나는 규칙을 비활성해주는 eslint 설정











