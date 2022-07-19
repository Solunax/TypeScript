# Week5 Review
## 1. 함수 타입 표현식
```javascript
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}
 
function printToConsole(s: string) {
  console.log(s);
}
 
greeter(printToConsole);
```
위 코드를 보면 인자부분에 특이한 점이 한가지 있다.<br>
인자 부분이 함수를 취하고 있다?!<br>
저 부분을 해석하면 다음과 같다.

1. greeter 함수의 인자인 fn은 문자열을 매개변수로 가지는 리턴 타입이 void인 함수
1. 그에 맞게 printToConsole 함수를 보면 string 타입의 s를 매개변수로 가지는 리턴 타입이 void인 함수
1. ```greeter(print)```를 실행하면 Hello, World가 출력됨

매개변수 타입을 지정하지 않으면 **any**로 지정됨(주의!)<br>
위 코드는 타입으로 다음과 같이 표현할 수 도 있음

```javascript
type GreetFunction = (a: string) => void;

function greeter(fn: GreetFunction) {
  // 동작
}
```

<br>

## 2. 호출 서명
JavaScript에선 함수는 호출 외에도 속성을 가질 수 있음<br>
속성을 사용하여 호출 하려면 객체 유형으로 호출 서명을 작성할 수 있음

```javascript
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};

function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}

const fun = function(n:number){
    if(n > 5)
        return true
    else
        return false
}
fun.description = "HI";

doSomething(fun)
```

TypeScript HandBook에서 하단의 fun 부분부터 doSomething까지의 실행 코드는 없으나, 필자가 임의로 추가한 부분임

1. 함수에도 class처럼 속성을 가질수 있음은 ```fun.description = "Hi"```에서 확인 가능하다.
1. 위 코드를 실행시키면, "HI returned true" 라는 결과를 받을 수 있다(fn(6) -> n > 5 -> true).

추가로 함수 형식과는 다르게 매개변수와 반환 타입 사이에 ```=>``` 이 아닌 ```:```을 사용함

<br>

## 4. 구성 서명
JavaScript의 함수는 new 연산자를 사용해 호출할 수 도 있음<br>
TypeScript에선 이를 **생성자**로 칭함<br>
호출 서명 앞에 new 키워드를 사용해 작성할 수 있음

```javascript
class SomeObject{
    name : string
    constructor(name:string){
        this.name = name
    }
}

type SomeConstructor = {
  new (s: string): SomeObject;
};

function fn(ctor: SomeConstructor) {
  return new ctor("hello");
}

console.log(fn(SomeObject).name)
```
TypeScript Handbook은 다 좋은데 실행부분의 코드가 없는게 **큰 단점**이다.<br>
맨 처음 SomeObject와, ```console.log``` 파트는 필자가 실행을 위해 임의로 작성한 코드다.(Handbook 작성자의 의도와 다를 수 도 있으나, 일단 동작은 한다)<br>
이 코드는 다음과같이 동작한다.
1. SomeConstructor 타입은 문자열을 가지고 임의의 객체를 반환하는 함수를 호출함
1. 여기서 필자는 SomeObject 내부에 생성자를 작성하여 타입에 맞는 동작을 수행하도록 함
1. fn 함수에 SomeObject를 담고 실행하면, "hello" 라는 이름을 가진 SomeObject를 반환함
1. ```console.log```로 출력하면 hello가 정상적으로 출력되는 것을 확인할 수 있다.

JavaScript의 Date와 같은 일부 객체는 new 키워드를 사용하지 않아도 호출할 수 있음<br>
호출과 서명을 임의로 동일한 타입으로 결합할 수 있음

```javascript
interface CallOrConstruct {
  new (s: string): Date;
  (n?: number): number;
}
```

<br>

## 5. 제네릭 함수
```javascript
function firstElement(arr: any[]) {
  return arr[0];
}
```

```javascript
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```
두 함수는 같은 동작을 수행하는 함수이지만,<br>
위는 반환 타입이 any이기에 개발자가 원치 않는 동작이 수행될 수 도 있다.<br>
여기서 제네릭이 **필요**하다<br>
아래쪽 함수를 보면 제네릭에 대해 이해가 쉬울것이다.
1. 기존 함수와 다르게 Type으로 선언되 파트들이 있다.
1. 여기선 함수 사용시 입력한 타입에 따라 입력 타입과 반환 타입이 결정됨(아래 코드 참조)

```javascript
// s 는 문자열
const s = firstElement(["a", "b", "c"]);
// n 는 숫자
const n = firstElement([1, 2, 3]);
// u 는 undefined
const u = firstElement([]);
```

TypeScript는 똑똑해서 타입을 자동으로 추론해 주기 때문에 아래처럼 쓸 필요가 없다!
```javascript
const s = firstElement<string>(["A", "B", "C"])
const n = firstElement<number>([1, 2, 3])
const u = firstElement<undefined>([])
```

<br>

### 5-1. 추론
타입은 TypeScript에 의해 자동으로 선택되기에 지정할 필요가 없다!<br>
매개 변수 타입을 여러개로 지정할 수 있다.

```javascript
function map<Input, Output>(arr: Input[], func: (arg: Input) => Output): Output[] {
  return arr.map(func);
}
 
const parsed = map(["1", "2", "3"], (n) => parseInt(n));
```
1. 위의 map 함수는 Input 타입으로 들어온 배열을 Output 타입으로 변환해서 만든 배열을 반환하는 함수임
1. ```parsed``` 쪽의 ```["1", "2", "3"]``` 은 문자열 배열이고, 뒤쪽의 ```(n) => parseInt(n)```에 의해 number 타입으로 변환됨
1. 위 코드의 실행 결과로 number 타입의 배열이 출력됨

<br>

### 5-2. 제약
extends 키워드를 사용하여 타입으로 올 수 있는 매개변수를 제한할 수 있음

```javascript
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
```
위 함수는 매개변수로 들어온 값들의 길이를 비교하여 더 긴 쪽을 출력하는 함수임
1. 인자들의 길이 비교를 위해선 length라는 값이 필요함
1. ```longest<Type extends {length :number}>```을 사용해 인자로 들어온 값들의 길이 비교가 가능 한 값만 사용할 수 있게 제한함

```javascript
// 동작 O
const longerArray = longest([1, 2], [1, 2, 3]);

// 동작 O
const longerString = longest("alice", "bob");

// 동작 X
const notOK = longest(10, 100);
```
위 코드들은 longest 함수를 사용해 비교한 예시임<br>
배열과 문자열 타입은 length라는 메소드를 사용해 문자의 길이나 배열의 길이 값을 통해 비교할 수 있음<br>
하지만 number 타입은 length라는 메소드가 없기에 길이비교가 불가능하고, 따라서 오류가 발생함

<br>

### 5-3. 제한된 값 작업
```javascript
function minimumLength<Type extends { length: number }>(
  obj: Type,
  minimum: number
): Type {
  if (obj.length >= minimum) {
    return obj;
  } else {
    return { length: minimum };
  }
}
```
위 함수는 정상적으로 보인다.<br>
인자가 타입이 length를 가진 것으로 제한되고, 
obj의 길이와 minimum값을 비교하여 더 긴 쪽을 반환함<br>
그런데 이는 잘못된 코드이다.
(제약 조건과 일치하는 객체가 아닌 전달된 것과 동일한 종류의 객체를 반환)<br>
아래의 예시를 같이 보자

```javascript
const arr = minimumLength([1, 2, 3], 6);
console.log(arr.slice(0));
```
위 코드의 동작을 살펴보면
1. ```[1, 2 ,3]```과 6을 비교해 더 큰쪽을 반환함(arr = {length: 6})
1. ```arr.slice(0)```통해 결과를 출력하려 한다. -> 그러나, 오류 발생!
1. arr에 slice 메소드는 있으나 반환되는 개체가 없음!

<br>

### 5-4. 타입 인수 지정
TypeScript는 일반적인 호출에서 타입을 유추할 수 있지만, 항상 그런것은 아님
```javascript
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}

const arr = combine([1, 2, 3], ["hello"]);
```
위 코드는 다음과 같이 동작한다.
1. combine 함수는 인자로 들어온 두 배열을 이어서 반환하는 함수임
1. 그런데 위와 같이 배열의 타입이 서로 다르면 오류가 발생함(Type은 하나인데 넘어온게 string과 number 두개?)
1. 이는 타입을 직접 지정해서 해결할 수 있음
```javascript
// :)
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

### 5-5. 좋은 제네릭 함수를 쓰기 위한 가이드 라인
1. 타입 매개변수 누르기(?)(Push Type Parameters Down)
    - 가능하면 타입 매개변수를 제한하지 않고, 매개변수 자체를 사용할 것
    - 같은 동작을 수행하지만, 조금 다른 코드를 보면서 이야기 해보자
        ```javascript
        function firstElement1<Type>(arr: Type[]) {
        return arr[0];
        }
        
        function firstElement2<Type extends any[]>(arr: Type) {
        return arr[0];
        }
        // Good
        const a = firstElement1([1, 2, 3]);
        // Bad
        const b = firstElement2([1, 2, 3]);
        ```
    - 서로 같아보이나 firstElement1은 반환 타입이 number, firstElement2는 any임
    (any는 만악의 근원이다)
    - TypeScript는 호출 중 요소를 해결하기 위해 기다리기보단 제약 조건을 활용해 해결함 (firstElement2에서 any[]로 제한함 - 따라서 반환 타입이 any)
1. 더 작은 타입 매개변수 사용하기
    - 가능한 항상  적은 타입 매개변수 쓰기
    - 이것도 비슷하지만 다른 코드를 확인해보자
        ```javascript
        function filter1<Type>(arr: Type[], func: (arg: Type) => boolean): Type[] {
        return arr.filter(func);
        }
        
        function filter2<Type, Func extends (arg: Type) => boolean>(
        arr: Type[],
        func: Func
        ): Type[] {
        return arr.filter(func);
        }            
        ```
    - 두 값을 연관시키지 않는 타입 매개변수 Func
    - Func는 기능을 읽기 어렵게 만들고, 추론하는 것 외에는 아무것도 하지 않음(좋지 않다)
1. 타입 매개변수는 두번 나타나야함
    - 만약 타입 매개변수가 한 위치에서만 나타나면, 제네릭이 필요한지 검토할것
    - 다음 함수는 제네릭으로 쓸 필요가 없다
        ```javascript
        function greet<Str extends string>(s: Str) {
        console.log("Hello, " + s);
        }
        
        greet("world");        
        ```
        다음과 같이 간단하게 작성할 수 있다.
        ```javascript
        function greet(s: string) {
        console.log("Hello, " + s);
        }        
        ```
    - 타입 매개변수는 여러 값의 유형을 연결하기 위한 것, 타입 매개변수가 한 번만 사용되면 쓸 필요가 없음

<br>

## 6. 선택적 매개변수
JavaScript의 함수는 때때로 가변적인 인자를 가질 때 가 있음, 아래의 함수가 그 예이다.
```javascript
function f(n: number) {
  console.log(n.toFixed());
  console.log(n.toFixed(3));
}
```
number.toFixed 메소드는 소수점을 처리할 때 사용하는 함수이다.<br>
메소드의 인자로 아무것도 전달하지 않으면 소숫점을 표시하지 않음<br>

TypeScript에선 ? 를 사용해 선택적 매개변수로 만들 수 있음
```javascript
function f(x?: number) {
  // 동작
}
f();
f(10);
```
두 코드 모두 오류 없이 동작한다!<br>
매개변수가 number로 지정되어 있지만, JavaScriptd에서는 undefined 타입으로 값을 가져오기에 x는 number | undefined 타입을 가짐<br>

```javascript
function f(x = 10) {
  // ...
}
```
다음과 같이 작성해 인자의 기본값을 지정할 수 도 있음

```javascript
declare function f(x?: number): void;

//아래 호출 모두 오류 없음
f();
f(10);
f(undefined);
```
위 기본값과 함께 다음과 같이 작성한다고 할 때
1. 함수 f의 본문에서 정의되지 않은 인수가 10으로 대체됨
1. 따라서 x는 number 타입을 가짐
1. x는 선택적 매개변수 ?키워드를 사용하고 있음, 그렇기에 undefined도 허용함

<br>

### 6-1. 콜백에서 선택적 매개변수
선택적 매개변수 및 함수 타입에 대해 알게 되면 콜백을 호출하는 함수를 작성할 때 실수를 범하기 쉬움

```javascript
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i);
  }
}

myForEach([1, 2, 3], (a) => console.log(a));
myForEach([1, 2, 3], (a, i) => console.log(a, i));
```
위 코드는 아무런 이상 없이 동작하는것으로 보일 수 있다.(배열의 값, 배열의 값과 해당 인덱스 출력)<br>
유심히 볼점은 **index**가 **선택적 매개변수**라는 점<br>
아래 처럼 작성하면 오류가 발생한다.
```javascript
myForEach([1, 2, 3], (a, i) => {
  console.log(i.toFixed());
});
```
왜 오류가 발생하느냐?<br>
index가 undefined일 수도 있기 떄문이다.(index는 선택적 매개변수로 선언됨)<br>

JavaScript에선 매개변수보다 인자가 많은 함수를 호출하면 추가 인자는 무시됨<br>
이는 TypeScript에서도 동일한 방식으로 작동함<br>
같은 타입의 매개변수가 적은 함수는 항상 더 많은 매개변수가 있는 함수를 대체할 수 있음<br>

따라서, 콜백함수에서 인수를 전달하지 않고 함수를 호출하려는 의도가 아니면! 선택적 매개변수를 사용하지 말것!

<br>

## 7. 함수 오버로딩
JavaScript는 함수를 다양한 인자 및 타입으로 호출할 수 있음<br>
ex) Date - timestamp(인자 1개), month/day/year(인자 3개)<br>

TypeScript에서도 오버로드를 수행할 수 있음<br>
미리 몇가지 함수 작성 후, 함수 본문을 작성하기

```javascript
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
// 인자가 2개인 경우는 오버로딩하지 않음(오류)
const d3 = makeDate(1, 3);
```
1. 위 코드는 다음과 같이 동작함
1. 인자가 1개일 경우 : timestamp로 Date개체 생성
1. 인자가 3개일 경우 : year, month, day로 Date 개체 생성 또는 mOrTimmestamp로 생성

위 코드에서 본문이 적히지 않은 처음 두 문장을 오버로딩 서명이라고 함<br>
마지막 d3를 보면 매개변수가 2개 주어지는데, 매개변수 두개로 오버로딩한 함수는 없음, 따라서 오류가 발생함

<br>

### 7-1. 오버로딩 서명 및 구현 서명
먼저 아래 코드를 보자
```javascript
function fn(x: string): void;
function fn() {
  // 동작
}

fn();
```
함수 본문을 쓰는데 사용되는 서명은 외부에서 보이지 않는다.<br>
구현 서명이 외부에 있어서 보이지 않고, 오버로드된 함수를 작성할 때는 함수의 구현 위에 두개 이상의 서명이 있어야 함<br>

또한 구현 서명은 오버로드 서명과 호환되어야 함
```javascript
// case 1
function fn(x: boolean): void;
function fn(x: string): void;
function fn(x: boolean) {}
```

```javascript
// case 2
function fn(x: string): string;
function fn(x: number): boolean;
function fn(x: string | number) {
  return "oops";
}
```
1. case 1번 같은 경우는 인자의 타입이 서로 일치하지 않음
1. case 2번 같은 경우는 반환 타입이 일치하지 않음

<br>

### 7-2. 좋은 오버로드 작성하기
제네릭과 마찬가지로 오버로드를 사용할 때 지침이 존재함<br>
원칙을 따르면 호출하기  쉽고, 이해하기 쉬우며, 구현하기 쉬워짐

```javascript
function len(s: string): number;
function len(arr: any[]): number;
function len(x: any) {
  return x.length;
}
```
위 함수는 문자열이나 배열의 길이를 반환하는 함수이다.<br>

아래는 그 사용의 예시다.
```javascript
len("");
len([0]);
len(Math.random() > 0.5 ? "hello" : [0]);
```
TypeScript는 함수를 단일 오버로드로만 해결할 수 있기에, 문자열 또는 배열일 수 있는 값으로 호출할 수 없다.<br>
위 문장이 무슨말이냐 하면 위 코드의 마지막 len 코드를 보면 된다.
1. 위의 len의 인자로 들어간 삼항 연산자는 다음과 같은 동작을 수행한다.
1. random으로 뽑은 수가 0.5보다 크면 "hello"
1. 뽑은 수가 0.5 이하면 [0]
1. 따라서 위 인자는 string | num[] 의 유니언 타입임
1. 위 설명대로 문자열 또는 배열인 값이 len에 할당됨

그렇기에 위 코드를 아래 코드로 바꾸면 좋다.
```javascript
function len(x: any[] | string) {
  return x.length;
}
```
any[] 또는 string 둘 중 한가지를 허용하고, 해당 인자의 길이를 반환함<br>

위 예시들로 설명하는 원칙은 다음과 같다.<br>
**가능한 경우 오버로딩 대신 유니언 타입의 매개변수를 사용하기**

<br>

## 8. 함수에서 this 선언하기
TypeScript는 코드 흐름 분석을 통해 this가 무엇인지 추론함
```javascript
const user = {
  id: 123,
 
  admin: false,
  becomeAdmin: function () {
    this.admin = true;
  },
};

interface DB {
  filterUsers(filter: (this: User) => boolean): User[];
}
 
const db = getDB();
const admins = db.filterUsers(function (this: User) {
  return this.admin;
});
```
JavaScript는 this라는 매개변수를 가질 수 없다고 명시함.<br>
TypeScript는 this를 사용해 함수의 본문에서 매개변수의 유형을 선언할 수 있음(```function(this:User)```)<br>

이 사용 패턴은 다른 개체가 함수를 호출할 때 제어하는 콜백 스타일의 API에서 공통적으로 사용됨<br>
this 방식을 사용하려면 화살표 함수가 아닌 함수를 사용해야 함<br>

```javascript
interface DB {
  filterUsers(filter: (this: User) => boolean): User[];
}
 
const db = getDB();
// 오류 ()=> 함수
const admins = db.filterUsers(() => this.admin);
```

<br>

## 9. 알아야 하는 다른 유형들
1. void
    - 값을 반환하지 않는 함수의 반환값
    - 함수에 반환문이 없거나 반환문으로부터 명시적인 값을 반환하지 않을 때 유추되는 타입
        ```javascript
        function noop() {
            return;
        }
        ```
        위와 같이 반환 값이 명시되지 않은 경우 void임
    - JavaScript에서 값은 반환하지 않는 함수는 undefined를 암묵적으로 반환함, 하지만 **TypeScript에서 void와 undefined는 동일하지 않음**
1. object
    - 특수한 타입
    - 원시타입(primitive type)(string, number, bingint, boolean, syymbol, null, undefined)를 제외한 모든 값
    - {} (빈 객체)와 global 타입 Object와는 다름
    - **object와 Object는 다릅니다!**
    - JavaScript에서 함수는 객체임(프로토타입 체인에 Object.prototype이 있음, Object의 인스턴스임), Object.keys로 호출 가능함
    - 위 이유로 TypeScript에서도 함수는 object로 간주함
1. unknown
    - any값을 나타냄
    - any와 비슷하지만, 안전함(말 그대로 알수 없는 값이기 때문에 메소드에 접근 자체가 불가능함)
        ```javascript
        // 동작
        function f1(a: any) {
            a.b(); 
        }

        // 오류
        function f2(a: unknown) {
            a.b();
        }        
        ```
        unkown이기에 알 수 없는 값을 가지고 동작하지 않음의 예시임
    - any는 함수 타입을 설명할 때 유용함(함수 본문에 값이 없어도 함수를 설명할 수 있음)
    - any와 반대로 unknown을 반환하는 함수일 경우 설명할 수 있음
        ```javascript
        function safeParse(s: string): unknown {
            return JSON.parse(s);
        }
        
        const obj = safeParse(someRandomString);
        ```
    - 단언을 사용하면 메소드에 접근할 수 있다
      ```javascript
        interface Dog{
            bark() : void
        }

        let a:Dog = {
            bark() {
                console.log("BARK")
            }
        }

        let c = (value as Dog)
        c.bark()
      ```
      as로 단언한 하면 오류 없이 메소드에 접근할 수 있다.
1. never
    - never는 관찰되지 않는 값을 나타냄
    - 이는 함수가 예외를 발생시키거나 프로그램을 종료하는 것을 의미함 
    - 또한 유니언에 남아있는 것이 없다고 결정할 때 나타냄
    - 아래는 예외가 발생할때 예시이다
        ```javascript
        function fail(msg: string): never {
            throw new Error(msg);
        }
        ```
    - 유니언에 남아있는 것이 없을때 예시
        ```javascript
        function fn(x: string | number) {
            if (typeof x === "string") {
                // 여긴 x가 string
            } else if (typeof x === "number") {
                // 여긴 x가 number
            } else {
                x; // 여긴 위에 전부 해당 안되고, 남은 유니언도 없으니 never
            }
        }
        ```
1. Function
    - 전역 타입 함수는 JavaScript의 모든 함수 값에 있는 바인딩, 호출, 적용 등의 속성을 설명함
    - 함수 유형의 값을 항상 호출할 수 있는 특수 속성이 있음
    - 위의 호출은 any를 반환함
        ```javascript
        function doSomething(f: Function) {
            return f(1, 2, 3);
        }
        ```
        위 함수는 입력되지 않은 호출이며, 일반적으로 안전하지 않은 반환 타입때문에 피하는게 좋음
    - 임의의 함수를 허용 해야하지만, 호출할 의사가 없는경우 ```() => void``` 타입이 안전함

<br>

## 10. 나머지 매개 변수 및 인수
1. 나머지 매개 변수
    - 선택적 매개변수나 오버로드를 사용하면 다양한 고정 인수 수를 허용할 수 있는 함수를 만들 수 있음
    - 나머지 매개변수를 사용해 무한한 수의 인수를 취하는 함수를 정의할 수 있음
    - 나머지 매개변수는 다른 모든 매개 변수 뒤에 나타내며 ```...``` 구분을 사용함
        ```javascript
        function multiply(n: number, ...m: number[]) {
            return m.map((x) => n * x);
        }
        const a = multiply(10, 1, 2, 3, 4);
        ```
        위 함수는 함수의 첫 매개 변수를 n으로, 나머지 모든 값을 number 배열로 받음<br>
        a는 1 ~ 4의 number 배열에 각각 10을 곱하고, 그 결과로 생성한 배열을 반환함
    - TypeScript에서 매개 변수의 타입 주석은 any 대신 any[]임
    - 그리고 타입 주석은 Array\<T> 또는 T[] 또는 튜플 타입이여야 함
1. 나머지 인수
    - 확장 구문을 사용해 배열에서 다양한 인수를 제공할 수 있음
        ```javascript
        const arr1 = [1, 2, 3];
        const arr2 = [4, 5, 6];
        arr1.push(...arr2);
        ```
        push 메소드는 배열을 이어붙히고, 길이를 반환함
    - 일반적으로 TypeScript는 배열을 변경할 수 없다고 가정하지 않음 **(변경 가능)**
        ```javascript
        const args = [8, 5];
        const angle = Math.atan2(...args);
        ```
        - 그러나 위 코드에선 오류가 발생함(number 배열은 0 이상의 숫자를 가진 배열이며, 딱 두 숫자를 가진 배열이 아닐 수 도 있음)
        - 그래서 코드를 아래처럼 수정하면 오류를 해결할 수 있음
        ```javascript
        const args = [8, 5] as const;
        const angle = Math.atan2(...args);
        ```
        - const로 단언해 튜플의 길이를 2로 고정하는 것으로 해결할 수 있다.
    - 나머지 인수를 오래된 런타임을 대상으로 사용하려면, 하위 수준 Iteration을 설정해야 함

<br>

## 11. 매개변수 분해
- 매개변수 분해를 사용해 인수로 제공된 개체를 함수 본문에서 하나 이상의 지역 변수로 언팩할 수 있음
- JavaScript에선 아래처럼 표현한다.
    ```javascript
    function sum({ a, b, c }) {
        console.log(a + b + c);
    }
    sum({ a: 10, b: 3, c: 9 });
    ```
- 개체에 대한 타입이 분해 구문 뒤에 있음
    ```javascript
    function sum({ a, b, c }: { a: number; b: number; c: number }) {
        console.log(a + b + c);
    }
    ```
    위와 동일하나 형태가 다르게 작성할 수 도 있다.
    ```javascript
    type ABC = { a: number; b: number; c: number };

    function sum({ a, b, c }: ABC) {
        console.log(a + b + c);
    }
    ```

<br>

## 12. 함수 할당 가능성
- void 반환 타입
    - 함수의 void 반환 타입은 비정상적이지만 예상된 동작을 발생시킬 수 있음
    - void 반환 타입이 있는 상황별 입력은 함수가 무언가를 반환하도록 강제하지 않음
    - void 반환 타입(type vf = () => void)을 가진 컨텍스트 함수 유형으로 구현되면, 다른 값을 반환할 수 있지만 무시됨.
    - () => void 타입이 유효함
        ```javascript
        type voidFunc = () => void;
      
        const f1: voidFunc = () => {
            return true;
        };
          
        const f2: voidFunc = () => true;
          
        const f3: voidFunc = function () {
            return true;
        };

        const v1 = f1();
      
        const v2 = f2();
          
        const v3 = f3();
        ```
        위 함수중 하나의 반환 값이 다른 변수에 할당되면 void를 유지함

        ```javascript
        const src = [1, 2, 3];
        const dst = [0];
    
        src.forEach((el) => dst.push(el));
        ```
        위 코드는 dst에 src 배열의 인자를 추가함 push 메소드는 number와 Array.prototype을 반환함

    - 리터럴 함수의 반환 유형이 void일 때, 그 함수는 아무것도 반환하지 않아야 함
        ```javascript
        function f2(): void {
            return true;
        }

        const f3 = function (): void {
            return true;
        };
        ```
