# Week4 Review
## 1. 좁히기?
좁히기란 무엇인가?<br>
다음 코드를 보면 이해하기 좀 쉬울 것이다.
```javascript
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
    //여기선 padding이 number
  }
  return padding + input;
  // 여기선 padding이 string
}
```
1. 위 padLeft 함수는 padding 인자로 number와 string을 허용하는 유니언 타입이다.
1. ```if(typeof padding === "number")```라는 조건 분기를 통해 padding이 number 타입인지 판단한다.
1. 만약 위 조건 분기가 true일 경우(여기선 number 타입), padding의 수 만큼 공백 + input 내용을 출력함
1. false일 경우 (string 타입), padding과 input을 한 문자열로 합친 값을 출력함

여기서 저 if 조건 분기를 통해 진행한 것이 **좁히기**다.<br>

만약 여기서 저 좁히기를 사용하지 않는다면 어떻게 될까?<br>
다음의 코드를 확인해 보자

```javascript
function padLeft(padding: number | string, input: string) {
  return " ".repeat(padding) + input;
}
```
이 코드는 TypeScript에서 오류로 판단한다. 왜?<br>
1. padLeft 함수의 인자인 padding은 number와 string을 허용하는 **유니언 타입**(중요) 이다.
1. repeat 메소드는 인자로 받는 값의 수치(number)만큼 반복을 진행한다.
1. 그런데 여기서 padding에 string값이 인자로 전달되면? -> 문자열 만큼 반복할 수 없기에 **오류발생**

위에 서술한 상황이 발생하는 것을 방지하기 위해 좁히기가 필요하다.

<br>

## 2. typeof 타입 가드
JavaScript는 값의 유형에 대한 정보를 제공하는 typeof 연산자를 제공함<br>
TypeScript에선 이를 통해 특정 문자열 집합을 반환할 수 있음<br>

- TypeScript에서 반환하는 문자열(타입)
    - string
    - number
    - bigint
    - boolean
    - symbol
    - undefined
    - object
    - function

위 1번에서 예시로 든 코드를 보면 typeof 연산자를 사용해 변수의 타입을 확인하고 있음을 확인할 수 있다.<br>
```if(typeof padding === "number")``` <- 이 부분이다.<br>

다음은 typeof 연산자를 통해 좁히기를 수행하는 코드이다.
```javascript
function printAll(strs: string | string[] | null) {
  if (typeof strs === "object") {
    for (const s of strs) {
        // 이 부분의 strs에서 오류가 발생함
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    // 아무것도 안해요
  }
}
```
위 주석을 보면 strs에서 오류가 발생하는데 왜 그럴까?
1. JavaScript에서 배열과 null은 모두 object 타입으로 판단함
1. 함수 printAll의 인자 strs는 string, string[], null을 허용하는 유니언 타입임
1. ```if (typeof strs === "object")``` 이 조건 분기에서 true인 타입은 string[], null 이다.
1. 조건 분기 true 내부의 코드를 보면, 배열에서 한글자씩 꺼내서 출력을 진행하는데, 만약 넘어온게 string[]이 아니라 null이면? -> 개발자가 의도한 동작이 아님, 따라서 오류

위 설명을 기반으로 코드를 고치려면 무엇을 해야하는가? 이건 뒤에 나오는 내용과 같이 설명하겠다.

<br>

## 3. true/false 좁히기
JavaScript에선 &&, ||, if 조건문, !(부정) 등의 표현식을 사용해 true/false를 판단함

```javascript
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `There are ${numUsersOnline} online now!`;
  }
  return "Nobody's here. :(";
}
```
위와 같은 코드의 동작을 설명하면
1. 인자로 받은 numUsersOnline을 조건문으로 분기함
1. true일 경우 -> There are ${numUsersOnline} online now! 라는 문구 출력
1. false일 경우 -> Nobody's here. :( 라는 문구 출력

단순한 동작을 수행하는 코드이다.<br>
허나 이상한점이 하나 있다. 숫자로 true/false가 판단이 되나?<br>

JavaScript는 가능 합니다!<br>
왜냐하면 강제로 부울값으로 전달하고 그 결과에 따라 분기합니다.
- JavaScript가 false로 판단하는 값들
    - 0
    - NaN
    - ""(빈 문자열)
    - 0n(bigint 스타일 0)
    - null
    - undefined

위에 표시한 값을 제외한 모든 값은 JavaScript에서 true입니다.<br>

혹시라도 난 자동으로 전달하는것을 원하지 않는다!<br>
하면 Boolean타입으로 강제로 변환할 수 있습니다.

```javascript
Boolean("hello")
!!"world"
```
1. Boolean 함수를 통해 변환하기(위쪽)
1. 이중 부정을 통해(!!) 변환하기(아래쪽)

위 두가지 방식을 쓰면 Boolean 타입으로 변환이 가능합니다.<br>

위에서 봤던 printAll 코드에 이곳에서 설명한 내용을 조금만 가미하면 오류를 수정할 수 있습니다.
```javascript
function printAll(strs: string | string[] | null) {
  if (strs && typeof strs === "object") {
    for (const s of strs) {
        // 이 부분의 strs에서 오류가 발생함
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    // 아무것도 안해요
  }
}
```
1. 동작은 이전 설명과 같습니다.
1. 바뀐 부분은 조건 분기```if (strs && typeof strs === "object")``` 부분입니다.
1. 위에서 설명했듯이 string[]과 null은 typeof 사용시 "object" 를 반환해서 오류가 발생한다고 했습니다.
1. 여기에 ```strs &&``` 을 추가해 저 인자가 null인지 판단합니다.
1. 만약 strs가 null이면? -> false 이기에 null 체크가 **가능**합니다.


true/false 좁히기 시 안좋은 예시가 하나 있습니다.
```javascript
function printAll(strs: string | string[] | null) {
  if (strs) {
    if (typeof strs === "object") {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
    }
  }
}
```
코드를 위와같이 작성하면 이 코드는 빈 문자열("")에 대해서 올바른 동작을 수행하지 않습니다.<br>

빈 문자열은 null은 아니기에 최초 조건분기에서 true가 되어야 하지만, JavaScript는 빈 문자열을 false로 판단해서 개발자가 원하는 동작을 수행하지 않습니다.<br>

마지막으로
```javascript
function multiplyAll(
  values: number[] | undefined,
  factor: number
): number[] | undefined {
  if (!values) {
    return values;
  } else {
    return values.map((x) => x * factor);
  }
}
```
위 코드를 보면 조건문에 !라는 부정 연산자를 사용하고 있습니다.<br>
values라는 값이 존재하지 않으면 values를 그대로 돌려주고<br>
그게 아니라면 values number 배열에 있는 모든 값에 대해 factor 만큼 곱한 배열을 반환합니다.

<br>

## 4. 등식 좁히기
TypeScript도 JavaScript와 마찬가지로 등식을 통해 타입을 좁힐 수 있습니다.(===, !==, ==, !=)

```javascript
function example(x: string | number, y: string | boolean) {
  if (x === y) {
    x.toUpperCase();
    y.toLowerCase();
  } else {
    console.log(x);
    console.log(y);
  }
}
```
1. 첫번째 조건 분기에서 x와 y의 타입이 서로 동일한지 확인하고 있음
1. 이 함수에서 x는 string과 number를 허용, y는 string과 boolean을 허용하는 유니언 타입이다.
1. 따라서 x와 y의 타입이 동일하다는 것은 x와 y가 공통으로 허용하는 string 타입밖에 없다.
1. 그렇기에 x와 y에 toUpperCase, toLowerCase 메소드를 사용할 수 있다.
1. x와 y가 서로 동일하지 않다면 x는 string 또는 number, y는 string 또는 boolean(string으로 동일하진 않음)이다.

JavaScript에 존재하는 느슨한 동등성 검사 ==, !=를 통해서도 올바르게 좁히기가 가능함
```javascript
interface Container {
  value: number | null | undefined;
}
 
function multiplyValue(container: Container, factor: number) {
  if (container.value != null) {
    console.log(container.value);
    container.value *= factor;
  }
}
```
1. Container인터페이스의 속성으로 number, null, undefined를 허용하는 유니언 타입인 value가 있음
1. multiplyValue 함수는 Container인터페이스와 factor를 인자로 받음
1. 조건문을 통해 true/false를 판단함
1. 여기서 사용된 조건이 ```container.value != null```이다.
1. != 으로 null이 아닐 경우에만 출력과 곱하기 연산을 수행함

<br>

## 5. in 좁히기
TypeScript에서 in을 통해 객체에 속성이 존재하는지 확인할 수 있음
```javascript
type Fish = { swim: () => void };
type Bird = { fly: () => void };
 
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
    // Fish 타입
  }
 
  return animal.fly();
  // Bird 타입
}
```
1. Fish와 Bird의 두가지 타입을 정의하고 있음
1. move 함수에서 인자로 Fish와 Bird 타입을 허용하는 animal이 있음
1. ```if("swim" in animal)``` 조건문을 통해 인자가 Fish인지 확인하고 있음
1. 인자의 타입에 따라 다른 동작을 수행함

다른 케이스를 확인해보면
```javascript
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void };
 
function move(animal: Fish | Bird | Human) {
  if ("swim" in animal) {
    animal;
  } else {
    animal;
  }
}
```
1. 옵셔널 프로퍼티로 선언된 속성을 가지고 있는 Human 타입이 추가됨
1. 똑같이 조건문으로 검사하면
1. true 일 때 animal은 Fish와 Human
1. false 일 때 animal은 Bird와 Human이 된다.

<br>

## 6. instanceof 좁히기
JavaScript에 특정 값이 다른 값의 인스턴스인지 확인하는 연산자가 있음<br>
ex) ```x isntanceof Foo``` - x가 Foo의 인스턴스?<br>
이것을 활용해 좁히기가 가능하다!

```javascript
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
  } else {
    console.log(x.toUpperCase());
  }
}
```
1. 이 함수는 Date, string을 허용하는 유니언 타입 인자 x가 있음
1. 인자인 x가 Date의 인스턴스인지 확인하는 조건문을 통해 분기가 일어남
1. true일 때 x에 toUTCString() 메소드를 사용해 출력
1. false일 때 x에 toUpperCase() 메소드를 사용해 출력함

<br>

## 7. 할당
TypeScript에선 할당을 진행할 때 연산자의 오른쪽을 보고 왼쪽의 내용을 적절하게 좁힘
```javascript
// nuber, string
let x = Math.random() < 0.5 ? 10 : "hello world!";

// number 재할당
x = 1; 
console.log(x);

// string 재할당
x = "goodbye!";
console.log(x);

// boolean 할당?
x = true
console.log(x)
```
1. 최초 x에 할당을 진행 할 때 삼항 연산자를 통해 number와 string을 허용하는 유니언 타입으로 좁혀짐
1. 따라서 새로 할당하는 값이 number와 string이면 오류가 발생하지 않음
1. 하지만 마지막 boolean을 할당하려면 오류가 발생함, x는 number와 string만 허용하는데 boolean을 할당하려고 했기 때문

<br>

## 8. 제어 흐름 분석
도달 가능성을 기반으로 하는 코드 분석을 제어 흐름 분석이라고 함<br>
TypeScript에선 이것을 활용해 유형 보호 및 할당이 발생할 때 타입 좁힘

```javascript
function example() {
  let x: string | number | boolean;
  x = Math.random() < 0.5;
  console.log(x);
  // 여기선 boolean

  if (Math.random() < 0.5) {
    x = "hello";
    console.log(x);
    // 여기선 string
  } else {
    x = 100;
    console.log(x);
    // 여기선 number
  }
  return x;
  // 위 조건문에서 할당된 string과 number중 한가지
}
```
1. 변수 x는 string, number, boolean을 허용하는 유니언 타입
1. 최초에 난수와 0.5를 비교해 boolean값이 할당
1. 조건문을 통해 string 또는 number가 할당
1. 마지막 반환시 변수 x는 string, number 둘중 하나가 됨

이 흐름을 통해 각 지점에서 변수 x가 다른 타입을 가지는 것을 확인할 수 있음

<br>

## 9. 타입 서술어
반환 유형이 타입 서술어
```javascript
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

// Fish, Bird 중 한가지를 할당받는 함수
let pet = getSmallPet();
 
if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```
1. ```pet is Fish``` 이 부분이 타입 서술어(반환 부분)
1. 함수에서 pet이 Fish로 단언하고, swim 속성이 undefined 즉 존재 여부를 판단함
1. 존재하면 pet은 Fish, 아니면 Bird임
1. 아래 조건문을 통해 pet의 타입을 확인하고 동작을 수행함

<br>

## 10. 유니언 구별
복잡한 구조를 구별하여 좁힐 수 있음
```javascript
interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}

function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius! ** 2;
  }
}
```
위 코드를 보면 모양을 정의하는 인터페이스와 그것의 넓이를 구하는 함수가 있다.<br>
1. Shape 인터페이스에 속성 kind는 circle, square 리터럴을 허용하는 유니언 타입
1. PI * r <sup>2</sup> 은 원의 넓이를 구하는 공식이기에 사각형의 넓이를 구할 수 없음
1. 따라서 조건문 ```if (shape.kind === "circle")```을 통해 좁히기를 수행하였음

위 코드는 원의 넓이만을 구할 수 있기에, 구조와 방식을 조금 변경해서 다시쓰면
```javascript
interface Circle {
  kind: "circle";
  radius: number;
}
 
interface Square {
  kind: "square";
  sideLength: number;
}
 
type Shape = Circle | Square;

function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;

    case "square":
      return shape.sideLength ** 2;
  }
}
```
다음과 같이 쓸 수 있다.<br>
Shape 인터페이스를 쓰는것도 괜찮긴 하지만, 명확한 구별 및 안정성을 위해 Circle과 Square 인터페이스로 분리하고, 이를 타입으로 묶어서 사용한다.
1. switch문을 사용해 인자의 kind에 따라 다른 동작을 수행함
1. kind == "circle" 일 경우 원의 넓이를
1. kind == "square" 일 경우 사각형의 넓이를 구하게 함

<br>

## 11. never 타입과 철저한 검사
좁히기를 수행하면서 모든 가능성을 제거하고 아무것도 남지 않은 지점까지 결합 옵션을 줄일 수 있음<br>
TypeScript에선 never 타입을 사용해 존재하지 않는 상태를 나타낼 수 있음
```javascript
type Shape = Circle | Square;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```
이전 사용한 함수에서 default(case와 모두 일치하지 않는 경우)와 never를 추가하였다.

- never를 사용할 시 이점?
    1. 만약 Shape에 새로운 인터페이스를 추가한다고 가정(Triangle)
    1. never가 없으면 switch에서 Triangle을 따로 처리 하지 않아도 오류가 발생하지 않음
    1. 하지만 Triangle에 대한 처리가 없기에 개발자가 원하는 동작이 수행되진 않음
    1. 이때 never가 빛을 발함(새로 처리할 내용이 생겼기에 never가 성립하지 않아 오류가 발생함 - 다른 Triangle이라는 가능성이 생겨버림)

위 내용을 코드로 정리하면 다음과 같다.
```javascript
type Shape = Circle | Square | Triangle;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    case "triangle":
        return (shape.width * shape.height) / 2
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```
never를 쓰지 않으면 triangle 케이스의 존재 여부와 상관없이 오류가 발생하지 않음(이걸 원하진 않겠죠?)<br>
never를 쓰면 다른 가능성이 생겨서 오류가 발생하고, 개발자는 이를 캐치해서 올바른 동작을 수행하게 할 수 있음(삼각형의 넓이 구하기)<br>

이해를 돕기 위해 never를 문장에 비유하면(내가 이해한 내용)<br>

- never는 마지막에 존재해서 이 뒤엔 아무것도 없다는것을 표시하는 마침표 역할임(Circle, Square.)
- 그런데 갑자기 triangle이 생겨서 마침표 뒤에 단어가 추가됨(Circle, Square. Triangle)(??????)
- 그렇게 되면 문장이 이상해짐(오류)
- 따라서 올바르게 문장을 수정해야함(Circle, Square, Triangle.)**(고마워요 never!)**