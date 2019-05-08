# Webpack 연습

- babel, webpack, es6, etc...

## babel

es6 이상을 완벽하게 지원하는 브라우저가 없다.  
es6 이상의 문법으로 프로젝트를 진행하는데 모든 브라우저에 문제 없게 하려면 babel 이 필요하다.  
babel 은 es6+ 를 es5 이하로 변경해주는 트랜스파일러(transpiler) 중 하나다.

### 사용하기

#### 패키지 설치

```bash
$ yarn add @babel/core @babel/cli @babel/preset-env --dev
```

#### 설정

`.babelrc` 파일을 생성

```
{
  "presets": ["@babel/preset-env"]
}
```

`preset-env` 를 사용하겠다는 의미

```
"scripts": {
    "build": "babel src/js -w -d dist/js"
},
```

`package.json` 에 scripts 추가

```
-w
타깃 폴더에 있는 모든 파일들의 변경을 감지하여 자동으로 트랜스파일한다. (--watch 옵션의 축약형)
```

```
-d
트랜스파일링된 결과물이 저장될 폴더를 지정한다. (--out-dir 옵션의 축약형)
```

`src/js/**` 파일 생성 후 es6 문법의 코드를 작성

```
$ yarn run build
```

#### 결과

- es6

```javascript
var [a, , b] = [1, 2, 3];
```

- result

```javascript
"use strict";

var _ref = [1, 2, 3],
  a = _ref[0],
  b = _ref[2];
```

## webpack

- 사용하는 이유: es6 모듈을 현재 브라우저에서 사용하려면 모듈 로더 또는 의존 관계에 있는 모듈들이 미리 합쳐져 있어야한다.

Webpack은 의존 관계에 있는 모듈들을 하나의 자바스크립트 파일로 번들링하는 모듈 번들러이다.  
Webpack을 사용하면 의존 모듈이 하나의 파일로 번들링 되므로 모듈 로더가 따로 필요 없다.

### 사용하기

#### 패키지 설치

```bash
$ yarn add webpack webpack-cli --dev
$ yarn add babel-loader --dev
```

webpack 이 모듈을 번들링 할 때 es6+ 에서 es5 로 트랜스파일링하기 위해선 `babel-loader` 패키지를 설치한다.

#### 설정

`webpack.config.js` 파일을 생성

```javascript
const path = require("path");

module.exports = {
  // 번들링 할 최상위 js
  entry: "./src/js/entry.js",
  // 번들링 결과가 저장 될 위치와 파일 이름
  output: {
    path: path.resolve(__dirname, "dist/js"),
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [path.resolve(__dirname, "src/js")],
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"]
          }
        }
      }
    ]
  },
  devtool: "source-map",
  // https://webpack.js.org/concepts/mode/#mode-development
  mode: "development"
};
```

```
"scripts": {
    "build": "webpack -w"
}
```

`package.json` 에 scripts 추가

## sass

### 사용하기

#### 패키지 설치

```bash
$ yarn add style-loader css-loader sass-loader node-sass --dev
```

DOM 에 적용하기 위해선 `style-loader`, `css-loader` 가 필요합니다.

#### 설정

```
entry: ["./src/js/entry.js", "./src/sass/main.scss"],

...

rules: [{
    test: /\.scss$/,
    include: [path.resolve(__dirname, "src/sass")],
    exclude: /node_modules/
    use: [
        "style-loader", // creates style nodes from JS strings
        "css-loader", // translates CSS into CommonJS
        "sass-loader" // compiles Sass to CSS, using Node Sass by default
    ]
}]
```

`webpack.config.js` 룰에 추가

참고: https://github.com/webpack-contrib/sass-loader
