# Week10 Review
## Module
- JavaScript는 이전부터 모듈화 코드를 처리하는 다양한 방법을 가지고 있음
- TypeScript는 2012년부터 사용되었으며, ES모듈(ES6 모듈) 형식으로 수렴됨
- ES모듈은 2015년 JavaScript 사양에 추가됨

<br>

## 1. JavaScript의 모듈 정의 방법
- ECMA Script 2015와 마찬가지로 TypeScript에서도 최상위 수준의 import / export가 포함된 파일은 모듈로 간주함
- 최상위 import / export 선언이 없는 파일은 전역 범위의 스크립트 파일로 간주
- 모듈은 자체 범위 내에서 실행(전역범위가 아님)
- 명시적으로 내보내지 않는 한 모듈 외부에서 볼 수 없음
- 다른 모듈에서 내보낸 값을 사용하려면 import 형식 중 하나를 사용해야 함

<br>

## 2. 비모듈
모듈이 아닌 스크립트는 두가지 종류가 있다
1. export가 없는 JavaScript 파일
1. 최상위 await가 없는 JavaScript 파일

- 스크립트 파일 변수와 타입은 공유 전역 범위에 있다고 선언함
- outFile 컴파일러 옵션을 사용해 입력 파일을 하나의 출력 파일에 결합 가능
- HTML에서 \<script> 태그를 사용해 파일을 순서대로 로드할 수 있음
- import / export가 없지만 모듈로 처리하려는 파일이 있을 경우
    - ```export{}``` 구문을 추가함
    - 위 구문은 파일을 아무것도 내보내지 않는 모듈로 변경함

<br>

## 3. TypeScript의 모듈
주의사항(아래의 세 가지 항목은 꼭 있어야 함)
1. 구문 : 항목을 가져오거나 내보낼 때 사용할 구문
1. 모듈 확인 : 모듈 이름과 디스크 파일간의 관계
1. 모듈 출력 대상 : 내보내기한 JavaSript 모듈의 모양

<br>

### 3-1. ES 모듈 구문
파일은 export 기본값을 통해 내보내기 선언 가능
```javascript
// @filename: hello.ts
export default function helloWorld() {
  console.log("Hello, world!")
}
// -----
import helloWorld from "./hello.js"
helloWorld()
```
- 모듈 파일의 이름은 hello.ts -> 변환하면 hello.js
- import를 통해 모듈을 불러오고, 모듈의 helloWorld 함수를 실행시킴

<br>

기본 내보내기 외에도 기본값(default)을 생략하여 내보내기를 통해 변수 및 함수를 둘 이상 내보내기가 가능함
```javascript
// @filename: maths.ts
export var pi = 3.14
export let squareTwo = 1.41
export const phi = 1.61
 
export class RandomNumberGenerator {}
 
export function absolute(num: number) {
  if (num < 0) return num * -1
  return num
}
// -----
import { pi, phi, absolute } from "./maths.js"
 
console.log(pi)
const absPhi = absolute(phi)
```
- maths.ts 모듈파일에는 다수의 변수와 함수를 내보내기 하고 있음
- import 구문으로 maths 모듈의 pi, phi, absolute를 가져옴
- absPhi는 absolute 함수의 반환 타입인 number가 됨

<br>

### 3-2. 추가 import 구문
import { old as new} 로 이름을 바꿔서 모듈의 값을 가져올 수 있음
```javascript
import { pi as π } from "./maths.js"
 
console.log(π)
```
- maths 모듈의 pi 값을 π로 사용함

위 코드를 단일 항목 가져오기로 혼합하여 사용할 수 있음
```javascript
// @filename: maths.ts
export const pi = 3.14
export default class RandomNumberGenerator {}
// -----
import RandomNumberGenerator, { pi as π } from "./maths.js"
 
RandomNumberGenerator

console.log(π)
```
- ```import RandomNumberGenerator, { pi as π } from "./maths.js"``` 이 부분이 혼합하여 사용한 부분임
- RandomNumberGenerator 클래스는 그냥 불러옴
- 모듈의 pi 는 π로 사용함

<br>

export한 모든 개체를 import * as name으로 가져올 수 있음
```javascript
// @filename: app.ts
import * as math from "./maths.js"
 
console.log(math.pi)
const positivePhi = math.absolute(math.phi)
```

<br>

주의사항
```javascript
// @filename: app.ts
import "./maths.js"
 
console.log("3.14")
```
- 위 코드의 import 구문은 아무런 동작을 수행하지 않음
- 가져온 모듈을 사용하지 않아도 maths의 코드는 평가됨
    - 이로 인해 개발자가 원하지 않는 동작을 수행할 수 도 있음

<br>

### 3-3. TypeScript의 특정 ES 모듈 구문
타입은 JavaScript값과 동일한 구문을 사용해 import / export 가능함

```javascript
// @filename: animal.ts
export type Cat = { breed: string; yearOfBirth: number }
 
export interface Dog {
  breeds: string[]
  yearOfBirth: number
}
// -----
import { Cat, Dog } from "./animal.js"
type Animals = Cat | Dog
```
- 모듈에 Cat, Dog 타입을 생성
- 이를 다른 파일에서 모듈로 불러옴

TypeScript는 타입 가져오기를 선언하기 위한 두가지 개념으로 가져오기 구문을 확장함
- import type : 타입만 가져오기 가능
    ```javascript
    // @filename: animal.ts
    export type Cat = { breed: string; yearOfBirth: number }
    export const createCatName = () => "fluffy"
    
    // @filename: valid.ts
    import type { Cat, Dog } from "./animal.js"
    export type Animals = Cat | Dog
    // -----
    import type { createCatName } from "./animal.js"
    const name = createCatName()
    ```
    - 위 예시에서 import type으로 createCatName 함수를 가져오려고 함
    - createCatName은 타입이 아니기에 오류가 발생함
- inline 타입 가져오기 : TypeScript 4.5는 가져온 참조가 타입임을 나타내기 위해 개별 접두사 사용 가능
    ```javascript
    import { createCatName, type Cat, type Dog } from "./animal.js"
    
    export type Animals = Cat | Dog
    const name = createCatName()
    ```
    - 위의 createCatName을 가져온 것과는 다르게 { } 안에 타입일 경우 타입을 명시
    - createCatName은 일반적인 import
    - Cat, Dog는 import type을 사용함

<br>

### 3-4. CommonJS 동작을 사용하는 ES 모듈 구문
- TypeScript에는 CommonJS 및 AMD 요구사항과 직접 상관관계가 있는 ES 모듈 구문이 있음
- ES 모듈을 사용한 import는 대부분 환경의 요구사항과 동일

```javascript
import fs = require("fs")
const code = fs.readFileSync("hello.ts", "utf8")
```
위 구문을 TypeScript 파일에서 CommonJS와 1:1로 일치함

<br>

## 4. CommonJS 구문
- npm의 대부분 모듈이 전달되는 형식임
- ES 모듈 구문을 사용하여 작성할 경우 공통점의 이해가 필요함
- CommonJS구문 작업은 더 쉽게 디버깅할 수 있도록 
도와줌

<br>

### 4-1. 내보내기, 가져오기
```javascript
function absolute(num: number) {
  if (num < 0) return num * -1
  return num
}
 
module.exports = {
  pi: 3.14,
  squareTwo: 1.41,
  phi: 1.61,
  absolute,
}
```
- 식별자는 모듈이라는 글로벌에서 내보내기 속성을 사용하여 내보냄

위 예시처럼 내보내기한 모듈은 require 구문을 사용해 가져올 수 있음
```javascript
// 1
const maths = require("maths")
maths.pi

// 2
const { squareTwo } = require("maths")
squareTwo
```
- 1번은 구조분해를 사용하지 않은 require 구문임
- 2번처럼 JavaSCript의 구조분해를 사용하여 단순하게 작성할 수 있음

<br>

### 4-2. CommonJS 및 ES모듈 상호 운용
- 기본 가져오기 및 모듈 네임스페이스 개체 가져오기 간의 구별과 관련하여 CommonJS와 ES 모듈 간의 기능이 일치하지 않음
- TypeScript에는 esModule과 함께 서로 다른 두 개의 제약조건 간의 마찰을 줄이기 위한 컴파일 플래그가 존재

<br>

## 5. TypeScript 모듈 확인 옵션
- 모듈 확인은 import 또는 export 또는 require문에서 문자열을 가져와 해당 문자열이 참조하는 파일을 결정하는 프로세스
- TypeScript는 두가지 확인 전략이 있음
    1. 클래식 : 컴파일러 옵션 모듈이 CommonJS가 아닐 때의 기본값은 이전 버전과의 호환성을 위해 포함됨
    2. 노드 : CommonJS에서 .ts, .d.ts의 추가적인 확인을 포함한 Node.JS가 작동하는 방식을 복제
- TypeScript는 moduleResolution, baseUrl, paths, rootDirs와 같은 TS 구성 플래그가 있음

<br>

## 6. TypeScript 모듈 출력 옵션
내보낸 JavaScript 출력에 영향을 주는 옵션은 두가지가 있음
1. JS 기능이 하향조정되고(이전 버전 JavaScript 런타임에서 실행) 그대로 유지되는지를 결정하는대상
1. 모듈이 상호작용하는데 사용되는 코드를 결정하는 모듈

<br>

- 사용 대상은 TypeScript 코드를 실행할 것으로 예상되는 JavaScript 런타임에 따라 결정됨
    - 지원하는 웹 브라우저 버전, Node.Js의 버전, Electron과 같은 런타임의 제약
- 모듈간의 통신은 모듈 로더를 통해 이루어짐
- 컴파일러 옵션은 모듈을 어떤것이 사용되는지 결정
- 런타임 모듈 로더는 모듈 실행 전 모듈의 종속성을 찾고 실행

```javascript
import { valueOfPi } from "./constants.js"
export const twoPi = valueOfPi * 2
```
위 구문은 모듈의 옵션에 따라 달라짐
- CommonJS
    ```javascript
    "use strict"
    Object.defineProperty(exports, "__esModule", { value: true })
    exports.twoPi = void 0
    const constants_js_1 = require("./constants.js")
    exports.twoPi = constants_js_1.valueOfPi * 2    
    ```
- UMD
    ```javascript
    (function (factory) {
        if (typeof module === "object" && typeof module.exports === "object") {
            var v = factory(require, exports)
            if (v !== undefined) module.exports = v
        }
        else if (typeof define === "function" && define.amd) {
            define(["require", "exports", "./constants.js"], factory)
        }
    })(function (require, exports) {
        "use strict"
        Object.defineProperty(exports, "__esModule", { value: true })
        exports.twoPi = void 0
        const constants_js_1 = require("./constants.js")
        exports.twoPi = constants_js_1.valueOfPi * 2
    })
    ```

<br>

## 7. TypsScript 네임스페이스
- TypeScript는 ES 모듈 표준보다 앞선 네임스페이스라는 자체 모듈 형식을 가짐
- 복잡한 정의 파일을 만드는데 유용한 기능을 가지고 있음
- 네임스페이스의 대부분 기능은 ES 모듈에 존재함