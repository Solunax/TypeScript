# Week2 Review
## 1. 오류 검출
```javascript
// message의 toLowerCase 메소드 호출
message.toLowerCase();

//message 함수 호출
message();
```
위 코드를 JavaScript에서는 그냥 실행하고, 개발자가 원하는 결과물을 반환하지 않음,<br>
하지만 TypeScript는 실행할 수 없다는 오류를 표시함, 위 코드는 다음과 같이 고칠 수 있음<br>

```javascript
var message = "Hello World";
// message의 toLowerCase 메소드 호출
message.toLowerCase();

// 변수를 함수처럼 호출할 수 없음
// message();
```
위 코드는 message 변수에 담긴 Hello World를 전부 소문자로 변환함(개발자가 기대한 결과물 + **실수 방지**)<br><br>

```javascript
function fn(x){
    return x.flip();
}
```
위 코드는 전달된 인자가 flip을 가져야만 개발자가 원하는 동작을 수행함,<br>TypeScript는 코드가 실행되기 전 인자인 x가 flip을 가지고 있는지 검사하고, 없으면 **오류**를 발생시킴

<br>

## 2. 정적 타입 검사
```javascript
const message = "hello";

//message 함수 호출, message는 변수이기에 호출 불가능
message();
```
TypeScript에선 위 코드 실행 이전에 오류 메시지를 보여줌

<br>

## 3. 예외가 아닌 실행 실패
```javascript
const user = {
    name : "Daniel",
    age : 26,
}

user.location; //undefined
```
위 코드는 JavaScript에서 undefined를 반환함,<br>
하지만 이는 개발자가 원하는 결과가 아님.<br>
TypeScript에선 location이 user에 정의되지 않았다고 오류를 발생시킴(원치 않은 결과 발생 방지)<br>

- 오타
    ```javascript
    const announcement = "Hello World!";
    
    // Case의 c가 소문자
    announcement.toLocaleLowercase();
    // Locale에서 e가 없음
    announcement.toLocalLowerCase();
    
    // 적으려던 메소드
    announcement.toLocaleLowerCase();
    ```
- 호출되지 않은 함수
    ```javascript
    function flipCoin(){
        // Math에는 random이란 속성이 없음
        // 정상적인 실행을 위해 Math.random()으로 수정
        return Math.random < 0.5;
    }
    ```
- 논리 오류
    ```javascript
    const value = Math.random() < 0.5 ? "a" : "b";
    if(value !== "a"){
        // Do
    }else if (value === "b"){
        // 이 구문은 항상 false임
        // 위 if문에서 value는 b인게 확실함, 하지만 else if 구문에서 한번 더 검사
    }
    ```
TypeScript를 사용하면, 위 코드들과 같은 다양한 오류들을 실행 전에 찾아서 수정할 수 있음 **d(^_^)b**

<br>

## 4.프로그래밍 도구
- 타입 검사기
    - 변수 또는 속성에 올바르게 접근하는지 검사
    - 사용 가능한 속성들을 제안(자동 완성)
- Quick Fixes(오류 자동 수정)
- 리팩토링(코드 재조직)
- 네비게이션(변수의 정의로 이동)
- 변수에 대한 참조 검색 기능

<br>

## 5. TypeScript 컴파일러 tsc
- 설치
    - npm install -g typescript(node.js 설치 필요)
- 사용
    1. TypeScript 파일 생성(FileName.ts)
    1. 코드 작성 후 tsc FileName.ts로 검사 수행
    1. TypeScript 코드를 JavaScript 코드로 변환해 줌
    - 만약 tsc FileName.ts중 "이 시스템에서 스크립트를 실행할 수 없으므로 ~" 에러 발생시 다음 [링크](https://hellcoding.tistory.com/entry/VSCode-%EC%98%A4%EB%A5%98-%EC%9D%B4-%EC%8B%9C%EC%8A%A4%ED%85%9C%EC%97%90%EC%84%9C-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC-%EC%8B%A4%ED%96%89%ED%95%A0-%EC%88%98-%EC%97%86%EC%9C%BC%EB%AF%80%EB%A1%9C) 참조 (지옥 불구덩이님 티스토리)
- 오류 발생시키기
    - TypeScript 검사 과정에서 오류가 생겨도 JavaScript 파일은 생성됨
    - 검사 과정에서 오류 발생시 JavaScript 파일 생성을 막으려면 --noEmitOnError 플래그를 사용, tsc --noEmitOnError FileName.tsc

## 6. 명시적 타입
```javascript
function greet(person: string, date: Date){
    console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
```
위 코드에서 person과 date에 **타입을 표기(string, Date)**<br>
greet 함수는 String 타입의 person, Date 타입의 date를 인자로 가짐<br>

```javascript
function greet(person: string, date: Date){
    console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
// string is not assignable to parameter type Date
greet("Maddison", Date());
```
위와 같은 상황에서 오류가 발생함<br>
Date()는 string을 반환하는데 인자로 Date를 요구하기 때문<br>
따라서 위의 Date()를 new Date()로 변경하면 정상 동작
```javascript
// msg : string 추론함
let msg = "hello there!";
```
위 코드에선 TypeScript가 **타입 추론**을 통해 자동으로 타입을 지정함

<br>

## 7. 지워진 타입
```javascript
"use strict";
function greet(person, date) {
    console.log("Hello ".concat(person, ", today is ").concat(date.toDateString(), "!"));
}
greet("Maddison", new Date());
```
위 6번에서 작성한 greet 함수를 tsc로 컴파일한 결과물<br>
1. person과 date는 타입을 표기하지 않음
1. `${}` 템플릿 문자열과 백틱을 사용하여 작성한 문장은 연결 연산자로 이루어진 일반 문자열로 변환됨

TypeScript를 수정 없이 실행할 수 있는 브라우저나 런타임이 없기에 TypeScript 코드를 JavaScript코드로 **변환**해야함<br>
변환의 결과로 TypeScript **전용 코드**와 **타입 표기**가 지워짐

<br>

## 8. 다운 레벨링
```javascript
// TypeScript
`Hello ${person}, today is ${date.toDateString()}!`;
// JavaScript
"Hello " + person + ", today is " + date.toDateString() + "!";
```
TypeScript에서 사용하는 템플릿 문자열은 ECMAScript 2015에서 등장함<br>
TypeScript는 이를 ECMAScript 예전 버전으로 변환함<br>
이를 **다운 레벨링**이라 함<br>
기본 동작은 ES3를 타겟으로 변환함<br>
tsc --target (version) FileName.ts, --target 플래그를 사용해 임의로 지정에 변환할 수 있음

<br>

## 9. 엄격도
- nolmplicitAny
    - any로 추론되는 변수에 대해 오류를 발생시킴
    - 일부 경우에 TypeScript는 타입 추론 없이 any로 간주하는 경우가 있음
    - any를 사용함에 따라 생기는 버그를 막기 위해 사용
- strictNullChecks
    - Null, Undefined에 대해 오류 발생
    - Null, Undefined의 처리를 잊는 것은 수많은 버그의 원인임