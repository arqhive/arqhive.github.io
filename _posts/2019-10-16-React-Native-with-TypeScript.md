---
date: 2019-10-16 14:33:00
layout: post
title: TypeScript로 React Native 프로젝트 시작하기
subtitle: TypeScript로 React Native 프로젝트 시작하기
description: React Native 애플리케이션을 TypeScript 기반으로 작성 할 수 있게 프로젝트를 구축합니다.
image: https://raygun.com/blog/wp-content/uploads/2016/07/Callums-post-on-Typescript.png
optimized_image: https://raygun.com/blog/wp-content/uploads/2016/07/Callums-post-on-Typescript.png
category: Dev
tags:
  - React
  - React Native
  - TypeScript
author: arqhive
---

**React Native** 프로젝트를 **TypeScript** 기반으로 작성 할 수 있게 프로젝트를 생성하고 설정 해보겠습니다.

기본적으로 React Native 앱이 PC 혹은 랩탑에서 실행 가능한 상태여야 합니다. [React Native 공식홈페이지](https://facebook.github.io/react-native/docs/getting-started.html)

해당 포스트에서는 패키지 매니저로 **yarn**을 사용합니다. 에디터로 **Visual studio code**를 사용합니다.

---

## 초기화

먼저 프로젝트를 생성합니다.

```bash
react-native init MyProject
```

저희는 TypeScript를 사용할 것이므로 생성된 프로젝트 내 `.flowconfig`는 삭제해 줍니다.

## TypeScript 추가

프로젝트에 TypeScript 관련 패키지들을 설치합니다.

```bash
yarn add --dev typescript
yarn add --dev react-native-typescript-transformer
```

TypeScript를 사용하기 위해서 typescript를 설치합니다.

babel을 이용하여 TypeScript 파일이 JavaScript 로 변경 될 수 있도록 react-native-typescript-transformer를 설치합니다.

> 예상 할 수 없는 오류를 방지하기 위해 typescript를 로컬로 설치 하는 것을 권장합니다.

TypeScript Transformer가 동작 할 수 있게 프로젝트 루트에 `rn-cli.config.js` 파일을 생성하고 다음 내용을 입력합니다.

```javascript
module.exports = {
  getTransformModulePath() {
    return require.resolve("react-native-typescript-transformer");
  },
  getSourceExts() {
    return ["ts", "tsx"];
  }
};
```

다음으로 `tsconfig.json` 파일을 생성합니다.

```bash
yarn tsc --init --pretty --jsx react-native
```

위 명령어를 실행하면 프로젝트 루트에 `tsconfig.json`파일이 생성됩니다. 파일을 열어봅시다.

파일을 열면 아래와 같은 옵션에 주석이 되어있는데 주석을 해제하도록 합니다.

```json
{
  ...
  // "allowSyntheticDefaultImports": true,
  ...
}
```

이 옵션은 기본 내보내기가 없는 모듈에서 기본 가져오기를 허용하는 옵션입니다. 코드에 영향은 없으며, 타입 검사에만 영향을 미칩니다.

아래는 제가 사용하는 `tsconfig.json` 입니다.

```json
{
  "compilerOptions": {
    /* Basic Options */
    "target": "es5",
    "allowJs": true,
    "checkJs": true,
    "module": "commonjs",
    "jsx": "react-native",
    "baseUrl": "./src",
    "resolveJsonModule": true,
    "noEmit": true,

    /* Strict Type-Checking Options */
    "strict": true,
    "noImplicitAny": false,

    /* Additional Checks */
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,

    /* Module Resolution Options */
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true
  },
  "exclude": ["node_modules"]
}
```

마지막으로 React와 React Native typings를 설치합니다.

```bash
yarn add --dev @types/react @types/react-native
```

이것으로 기본적인 프로젝트 설정을 마쳤습니다. 더 진행되는 내용은 옵션이므로 입맛에 따라 진행하시면 됩니다.

---

## Jest 설정

**Jest** 를 이용해 `.ts, .tsx` 파일을 테스트 하는 경우 `ts-jest`를 설치합니다.

```bash
yarn add --dev ts-jest
```

그리고 `package.json` 파일을 열고 기본 `"jest"`부분을 아래처럼 바꿔줍니다.

```json
"jest": {
  "preset": "react-native",
  "moduleFileExtensions": [
    "ts",
    "tsx",
    "js"
  ],
  "transform": {
    "^.+\\.(js)$": "<rootDir>/node_modules/babel-jest",
    "\\.(ts|tsx)$": "<rootDir>/node_modules/ts-jest/preprocessor.js"
  },
  "testRegex": "(/__tests__/.*|\\.(test|spec))\\.(ts|tsx|js)$",
  "testPathIgnorePatterns": [
    "\\.snap$",
    "<rootDir>/node_modules/"
  ],
  "cacheDirectory": ".jest/cache"
}
```

테스트에 필요한 typings도 설치해 주어야 겠죠?

```bash
yarn add --dev @types/jest @types/react-test-renderer
```

이제 Jest를 실행하면 `ts-jest`와 함께 `.ts, .tsx` 파일이 테스트 됩니다.

> 이제 .jest 폴더가 생성됩니다. 이 폴더는 추적될 필요는 없으므로 git을 이용하고 있다면 .gitignore에 .jest/ 를 추가합니다.

---

## TSLint 및 Prettier 적용하기

프로젝트에 TSLint와 Prettier를 적용합시다.

먼저 필요한 패키지들을 설치합니다.

>아래는 저의 설정에 맞게 작성하였습니다. 정답은 아니므로 여러분이 필요에 따라 수정해서 사용 할 수 있습니다.

```bash
yarn add --dev prettier prettier-tslint tslint tslint-config-airbnb tslint-config-prettier tslint-plugin-prettier
```

다음 프로젝트 루트에 `tslint.json`과 `.prettierrc`파일을 생성합니다. (RN Cli 버전에 따라 .prettier.js 파일이 있는 경우도 있음)

먼저 `.prettierrc`를 열고 아래와 같이 작성합니다.

```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "semi": true,
  "bracketSpacing": true,
  "jsxBracketSameLine": true,
  "singleQuote": true,
  "trailingComma": "all"
}
```

다음은 `tslint.json`을 열고 아래와 같이 작성합니다.

```json
{
  "rulesDirectory": ["tslint-plugin-prettier"],
  "rules": {
    "prettier": [true, ".prettierrc"],
    "ordered-imports": false,
    "import-name": false,
    "variable-name": false
  },
  "extends": [
    "tslint:recommended",
    "tslint-config-prettier",
    "tslint-config-airbnb"
  ]
}
```

확장에 따른 조금 귀찮은 옵션은 꺼두었습니다.

마지막으로 Visual Studio Code 확장인 TSLint를 설치하면 마무리 됩니다.

---

## 절대 경로로 파일 불러오기

파일의 경로가 깊어지면

```javascript
import SomeComponents from '../../../../SomeComponents';
```

처럼 불러오게될 일이 생깁니다. 조금 불편하겠죠? 그래서 절대 경로로 파일을 불러 올 수 있도록 하겠습니다.

이를테면

```javascript
import SomeComponents from 'components/SomeComponents'
```

처럼 말이죠!

먼저 프로젝트 루트에 `src` 폴더를 만들고  아래의 패키지를 설치합니다.

```bash
yarn add --dev babel-plugin-module-resolver
```

프로젝트 루트에 `babel.config.js`파일을 열고 아래의 내용을 추가합니다.

```javascript
module.exports = {
  ...
  plugins: [
    [
      'module-resolver',
      {
        root: ['./src'],
        extensions: ['.ts', '.tsx', 'js', 'jsx'],
      },
    ],
  ],
  ...
}
```

`tsconfig.json` 파일을 열고 아래의 내용을 추가합니다.

```json
{
  "compilerOptions": {
    ...
    "baseUrl": "./src",
    ...
  },
}
```

이제 `src` 폴더로 부터 시작해서 파일을 불러 올수 있게 되었습니다.

---

## 기타

`tsconfig.json`에서 `babel.config.js` 관련 경고가 나오는 경우 `tsconfig.json`에 다음 옵션을 추가합니다.

```json
{
  "compilerOptions": {
    ...
    "noEmit": true,
    ...
  }
}
```

자 이제 모든 설정이 끝났습니다! TypeScript로 해피코딩 하시길 바랍니다! 잘못된 부분이 있다면 댓글로 알려주세요.
