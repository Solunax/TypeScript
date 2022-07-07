# Week3 Review
## 1. 원시 타입
```javascript
var a:string = "Hello World";
var b:number = 199;
var c:boolean = true;

var a1:stinrg[] = ["YEAH"];
var b1:number[] = [198];
var c1:boolean[] = [true];

var a:Array<Number> = [1, 2, 3]
```
- 위 코드에서 타입은 다음과 같다,<br>
    - string : 변수 a, 문자열
    - number : 변수 b(다른 언어의 int나 float같은 것이 존재하지 않음, 숫자는 number로 통일), 숫자
    - boolean : 변수 c(true/false), 논리연산
    - string배열 : 변수 a1
    - number배열 : 변수 b1
    - boolean배열 : 변수 c1
<br>

배열은 string[], number[], boolean[]와 같이 선언할 수 있지만, Array\<type\>과 같은 방식으로도 선언할 수 있음

<br>

## 2. any
```javascript
let obj: any = { x: 0 };

obj.foo();
obj();
obj.bar = 100;
obj = "hello";
const n: number = obj;
```
any는 특정 값으로 인해 타입 검사 오류가 발생하는 것을 원치 않을때 사용 가능<br>
- 타입이 any일 경우 가능한 것
    - 해당 값의 임의의 속성에 접근
    - 함수처럼 호출
    - 임의 타입에 할당 및 할당 받기
    - 구문적으로 유효한 모든 것

noImplicitAny : TypeScript는 문맥으로 부터 추론이 불가능하면 any 타입을 부여함(원치 않는 오류 발생 확률 증가)<br>
any 타입은 타입 검사가 이루어지지 않음<br>
컴파일러 플래그인 noImplicitAny를 사용하면 any 타입에 대해 오류를 발생시킴

<br>

## 3. 타입 표기
const, var, let등을 사용하여 변수를 선언할 때 변수의 타입을 명시적으로 지정함, 선택사항임
```javascript
let myName: string = "Alice";
const yourName: string = "DOG";
var other : number = 19999;
let value1 = 19
// const : 반드시 선언과 동시에 초기화, 재선언 및 재할당 불가능
// let : 중복 선언 불가능, 재할당 가능
// var : 선언과 초기화가 동시에 진행, 중복 선언 가능
```
타입을 따로 표기하지 않으면 TypeScript가 타입을 유추함<br>

단, 타입을 표기할 떄 다음과 같이
```java
int v1 = 10;
```
타입을 왼쪽에 쓰지 않음, 타입 표기는 항상 변수 이름 뒤에 위치함

<br>

## 4. 타입 추론
```javascript
var a:string = "Hello World";
var b:number = 199;
var c:boolean = true;

var a1 = "Hello World";
var b1 = 199;
var c1 = true;
```
위의 변수 a, b, c는 타입을 표기하였지만, 아래처럼 타입을 표기하지 않아도 TypeScript가 알아서 타입을 **유추**함<br>

<br>

## 5. 함수
```javascript
function sayHello(name: string){
    console.log("Hello" + name.toUpperCase());
}
```
매개 변수 뒤에 타입을 표기할 수 있음<br>
타입을 표기하면 해당 함수에 대한 인자는 검사를 진행함

```javascript
function sayHello(name: string){
    console.log("Hello" + name.toUpperCase());
}
sayHello(19);
```
위와 같이 코드를 작성하면 인자로 number 타입이 제공 되었기에 오류가 발생함.<br>
타입을 표기하지 않았다면, TypeScript는 인자의 갯수가 올바르게 전달 되었는지 검사함<br>

<br>

## 6. 반환 타입
```javascript
function goodNumber(): number{
    return 100
}
```
위 코드와 같이 매개변수 목록 뒤에 타입을 표기하여 반환 타입을 지정할 수 있음<br>
반환 타입을 표기하지 않더라도 return문을 바탕으로 반환 타입을 **추론**함

```javascript
function goodNumber(): number{
    return "100"
}
```
위 코드처럼 반환 타입과 return 구문의 타입이 일치하지 않으면 오류가 발생함

<br>

## 7. 익명 함수
정의 : 이름 없는 함수
```javascript
const name = ["Son", "Kane"]
name.forEach(function (s){
    console.log(s.toUpperCase());
})

name.forEach((s) => {
    console.log(s.toUpperCase());
})
```
TypeScript는 코드상에서 함수가 위치한 곳을 보고 함수의 매개변수에 타입을 부여함<br>
위와 같은 익명함수에선 s를 string타입으로 추론함<br>
만약, toUpperCase()에서 오타가 발생하면 이를 알려줌(string 타입 추론)

<br>

## 8. 객체 타입
```javascript
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```
위 코드의 매개변수 pt가 객체 타입임<br>
프로퍼티를 가지는 JavaScript 값이 객체 타입<br>
객체 타입을 정의하려면 객체의 프로퍼티들과 타입을 나열하면 됨 ex) pt : { x: number; y: number }<br>
프로퍼티를 구분할 때 , 또는 ;를 사용할 수 있음, 마지막에 위치한 구분자의 표기는 선택사항

<br>

## 9. 옵셔널 프로퍼티
객체 타입의 일부 또는 모든 프로퍼티의 타입을 선택적인 타입으로 지정 가능함
```javascript
function printName(obj: { first: string; last?: string }) {

}

printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });
```
위 코드에서 last?: string 처럼 프로퍼티 이름 뒤에 ?를 붙이면 지정 가능<br>
JavaScript에선 존재하지 않는 프로퍼티에 접근하면 undefined값을 얻음<br>
TypeScript는 이에 대해 오류를 발생시키므로, 해당 값을 사용하기 전 undefined 여부를 확인해야 함

<br>

## 10. 유니언 타입
파이프라인을 사용하여 데이터 유형을 결합
```javascript
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}

printId(101);
printId("202");
printId({ myID: 22342 });
```
printId 함수의 인자인 id는 number와 string 타입을 인자로 받음<br>
printId 101, "202"는 오류 없이 동작<br>
마지막 {myId: 22342}는 유니언 타입으로 지정한 number, string에 해당하지 않기에 오류가 발생<br>

```javascript
function printId(id: number | string) {
  console.log(id.toUpperCase());
}
```
이 printId함수는 오류가 발생함<br>
유니언 타입은 **모든 멤버**에 대하여 **유효**한 작업일 때만 허용됨(string은 toUpperCase메소드가 있지만, number는 없음)<br>
이를 해결하기 위해 좁히기를 사용함(타입에 따른 개별 동작)

```javascript
function printId(id: number | string) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id);
  }
}
```
typeof를 사용해 string타입일 경우에만 toUpperCase메소드를 사용함<br>
그 외(여기에선 number)는 단순히 출력만 진행함

<br>

## 11. 타입 별칭
긴 타입을 짧게 이름지어서 사용할 수 있음
```javascript
type Point = {
  x: number;
  y: number;
};
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```
위 7번에서 사용한 코드와 동일한 코드임<br>
타입을 재사용하거나 다른 이름으로 부르고 싶은 경우 사용함<br>
유니언 타입에 대해서도 타입 별칭을 부여할 수 있음
```javascript
type ID = number | string;
```

<br>

## 12. 인터페이스
type과 유사함, 구조와 기능을 인터페이스로 선언할 수 있음
```javascript
interface Point {
  x: number;
  y: number;
}
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```
단일 혹은 객체 형태로 type값을 명시 및 적용할 수 있음

<br>

## 13. 타입과 인터페이스의 차이점
- 확장
  - 인터페이스(extends)
    ```javascript
    interface Animal{
      name: string
    }

    interface Bear extends Animal{
      honey: boolean
    }

    const bear = getBear()
    bear.name
    bear.honey
    ```
  - 타입(교집합 &)
    ```javascript
    type Animal = {
      name: string
    }

    tyep Bear = Animal & {
      honey: Boolean
    }

    const bear = getBear();
    bear.name;
    bear.honey;
    ```
- 새 필드 추가
  - 인터페이스
    ```javascript
    interface Window {
      title: string
    }

    interface Window {
      ts: TypeScriptAPI
    }

    const src = 'const a = "Hello World"';
    window.ts.transpileModule(src, {});
    ```
  - 타입
    ```javascript
    type Window = {
      title: string
    }

    type Window = {
      ts: TypeScriptAPI
    }
    // Error: Duplicate identifier 'Window'.
    ```
  - 인터페이스는 재선언을 통해 확장이 가능함<br> 하지만 타입은 재선언하면 오류가 발생함<br>
  
  <br>

- 인터페이스는 항상 확장 가능, 타입은 생성된 뒤 새 프로퍼티를 추가할 수 없음<br>
- 인터페이스와 타입 중 뭘 사용할지 모르겠다면?<br>
  - 우선 인터페이스 사용, 문제가 발생하면 타입 사용

<br>

## 14. 타입 단언
타입에 대한 정보가 확실할 경우 타입 단언을 통해 구체적으로 명시할 수 있음
```javascript
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;

const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```
- as 혹은 꺽쇠괄호를 사용하여 타입 단언 가능<br>
- 타입 단언은 컴파일때 제거됨, 이와 관련된 검사는 런타임 중에 이루어지지 않음(타입 단언이 틀려도 예외 발생하거나 null이 생성 되지 않음)

<br>

## 15. 리터럴 타입
숫자 혹은 문자열을 타입으로 사용할 수 있음
```javascript
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "centre");
```
해당 코드에서 alignment는 left, right, center라는 문자열을 리터럴 타입으로 사용함<br>
printText("G'day, mate", "centre") 는 centre가 alignment에 존재하지 않기에 오류가 발생함

<br>

## 16. null, undefined
초기화되자 않은 값을 가리키는 원시값<br>
코드를 작성할 때 null, undefined는 원치 않는 버그를 유발함<br>

strictNullCheck 설정 :  null과 undefined를 타입으로 평가할 수 있음
```javascript
function doSomething(x: string | undefined) {
  if (x === undefined) {
    
  } else {
    console.log("Hello, " + x.toUpperCase());
  }
}
```
매개변수 x의 타입이 undefined면 아무런 동작을 수행하지 않고, 문자열 타입이면 출력함

```javascript
function liveDangerously(x?: number | undefined) {
  console.log(x!.toFixed());
}
```
접미사 !(Null 아님 단언 연산자)를 통해 null, undefined를 제거할 수 있음

<br>

## 17. 열거형
특정한 값에 의밀르 부여하는 방법<br>
JavaScript에 없는 기능<br>
TypeScript에서 런타임 수준으로 추가하는 기능
```javascript
enum Direction {
  Up = 1,
  Down,
  Left,
  Right,
}
```
Up : 1, Down : 2, Left : 3, Right : 4

<br>

## 18. 자주 사용하지 않는 원시형 타입
- bigint
  - 아주 큰 정수를 다루기 위한 원시타입
  - number 타입이 안정적으로 나타낼 수 있는 최대치인 2<sup>53</sup> - 1 이상의 수를 표현할 수 있음
  ```javascript
  //BigInt 함수로 생성
  const oneHundred: bigint = BigInt(100);

  // 리터럴 구문으로 생성
  const anotherHundred: bigint = 100n;
  ```
  <br>

- symbol
  - 전역적으로 고유한 참조값을 생성
  - symbol은 불변, 유일해야 함
  ```javascript
  const firstName = Symbol("name");
  const secondName = Symbol("name");
  
  if (firstName === secondName) {
  //symbol은 유일하기에 항상 false임
  //name으로 symbol의 이름은 같지만, 서로 다름
  }
  ```  