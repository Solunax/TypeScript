# Week6 Review
## 1. 객체 타입
JavaScript에서 데이터를 그룹화하고 전달하는 방법 중 한가지<br>
TypeScript는 이를 객체 타입(Object Type)으로 표현함

```javascript
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name;
}
```
1. 함수는 name, age 속성을 가진 객체 타입 person을 매개변수로 받음
1. 그 중 name 속성을 Hello 문자열과 더해서 반환함

여기서 person이 객체 타입이다.<br>

위 예시는 익명 객체로 표현한 방식이고, 인터페이스를 사용해 객체 타임의 이름을 지정할 수 있다.
```javascript
interface Person {
  name: string;
  age: number;
}

type Person = {
  name: string;
  age: number;
};

function greet(person: Person) {
  return "Hello " + person.name;
}
```
위에서 사용한 예시를 인터페이스를 활용해 조금만 손 본 것이다.<br>
그리고 위 코드처럼 타입으로 객체 타입을 생성할 수 도 있다.

<br>

## 2. 속성 지정자
객체 타입의 속성은 타입(string, number, etc), 선택적 속성 여부(?), 속성의 쓰기  가능 여부(writable) 등 몇가지를 지정할 수 있다.<br>

타입 지정같은 경우는 다 알고있을것이다. 따라서 타입 지정을 제외한 나머지 두 가지에 대해서 알아보자.<br>

1. 선택적 속성
    - 이전까지 스터디를 진행하면서 봐왔던 (?) 를 사용한 속성이다.
    - 이건 선택적 속성을 지정할 때 사용한 타입과 undefined의 유니언을 반환한다.

        ```javascript
        interface PaintOptions {
            shape: Shape;
            xPos?: number;
            yPos?: number;
        }
        
        function paintShape(opts: PaintOptions) {
            // 동작
        }
        
        const shape = getShape();
        paintShape({ shape });
        paintShape({ shape, xPos: 100 });
        paintShape({ shape, yPos: 100 });
        paintShape({ shape, xPos: 100, yPos: 100 });    
        ```
        
        위 예시 코드를 보면 조금 이해가 쉬울 것 이다.<br>
        1. PaintOption 인터페이스의 xPos와 yPos 속성은 타입이 number인 선택적 속성이다.
        1. 선택적 속성은 값이 있으면 number 없으면 undefined를 갖기에 위 예시에서 xPos와 yPos는 **number와 undefined**의 유니언 타입이다.
    - 선택적 속성은 undefined 값도 가질 수 있기에 좁히기를 사용해 나중에 발생할 수 도 있는 오류를 방지할 수 있다.<br>
    아래는 좁히기를 사용한 예시이다.
    
        ```javascript
        function paintShape(opts: PaintOptions) {
            let xPos = opts.xPos === undefined ? 0 : opts.xPos;
            let yPos = opts.yPos === undefined ? 0 : opts.yPos;
        }

        
        function paintShape({ shape, xPos = 0, yPos = 0 }: PaintOptions) {
        }         
        ```

        예시에서 위 쪽 함수는 삼항 연산자를 사용해 undefiend일 경우 0 아니면 매개변수의 속성값을 그대로 사용한다. <br>
        아래쪽 함수는 JavaScript에서 지원하는 기능을 사용해 초기값을 부여하여 따로 undefined로 좁히기 할 수고를 덜 수 도 있다.
    - 주의할 것이 한가지 있다.<br>
    만약 객체 구조화(분해) 패턴을 사용할 경우 아래 처럼 사용하지 않도록 주의해야 한다.

        ```javascript
        function draw({ shape: Shape, xPos: number = 100 }) {
            render(shape);
            render(xPos);
        }    
        ```
        매개변수 파트를 위 처럼 작성할 경우 이는 타입을 설명하는 것이 아니라, 해당 타입의 변수들을 생성하는 것으로 인식하기에 주의가 필요하다.

1. 읽기 전용 속성
    - TypeScript에서 속성을 읽기 전용으로 표시할 수 있다.
    - 실행 시 동작은 변경되지 않지만, 타입 검사중 작성(write)이 불가능하다.<br>
    예를 들어

        ```javascript
        interface SomeType {
            readonly prop: string;
        }
        
        function doSomething(obj: SomeType) {
            // 출력은 가능(읽기)
            console.log(`prop has the value '${obj.prop}'.`);
            // 할당은 불가능(쓰기) - 오류
            obj.prop = "hello";
        }    
        ```
        SomeType 인터페이스의 prop이라는 속성을 읽어서 출력은 할 수 있다.<br>
        허나, prop 속성에 값을 다시 할당하려고 하면 이는 읽기 전용 속성의 동작에 반하는 행위이기에 오류가 발생한다.
    - 그런데 위와는 다르게 예외적인 케이스가 존재한다.
        ```javascript
        interface Home {
            readonly resident: { name: string; age: number };
        }
        
        function visitForBirthday(home: Home) {
            console.log(`Happy birthday ${home.resident.name}!`);
            // 오류가 발생하지 않는다. 일부만 수정했음
            home.resident.age++;
        }
        
        function evict(home: Home) {
            // 오류가 발생함. 전체를 수정하려고 했음
            home.resident = {
                name: "Victor the Evictor",
                age: 42,
            };
        }    
        ```
        위 예시에선 읽기전용 속성으로 resident라는 객체를 생성했다.<br>
        첫번째 함수의 ```home.resident.age++``` 부분에선 오류가 발생하지 않는데, 이는 resident 객체 전체가 아닌, 특정 속성을 변경하는 것이기에 오류가 발생하지 않음<br>
        그러나 두번째 함수는 resident 객체 전체를 수정(write)하려고 하기에 오류가 발생한것.<br>
        **한 가지만 기억하자! 전체를 바꾸는건 X, 일부만 수정은 O**
    - 다음 설명할 것은 예시와 함께 보는것이 이해가 빠르다.
        ```javascript
        interface Person {
            name: string;
            age: number;
        }
        
        interface ReadonlyPerson {
            readonly name: string;
            readonly age: number;
        }
        
        let writablePerson: Person = {
            name: "Person McPersonface",
            age: 42,
        };
        
        let readonlyPerson: ReadonlyPerson = writablePerson;
        
        // 42
        console.log(readonlyPerson.age);
        writablePerson.age++;
        // 43
        console.log(readonlyPerson.age); 
        ```
        쓰기 가능한 인터페이스와 읽기전용 인터페이스 각각 생성한 뒤, 읽기 전용 인터페이스에 쓰기 가능한 인터페이스를 할당했다.<br>
        잘 보면, 읽기 전용임에도 불구하고 readonlyPerson.age의 값이 변경된다.<br>
        이는 단순히 readonlyPerson 객체가 Person 객체의 주소를 할당 받았기 때문이다.<br>
        아무리 읽기 전용 객체라 할 지라도, 할당 받은 원본의 값이 바뀐다면 값이 변한다!
1. 인덱스 서명
    - 타입의 속성 이름을 알 수 없지만, 값의 형태는 알 수 있을때 사용한다.
        ```javascript
        interface StringArray {
            [index: number]: string;
        }
        
        const myArray: StringArray = getStringArray();
        const secondItem = myArray[1];
        ```
        위 코드의 ```[index: number] : string``` 이 파트가 인덱스 서명이다.<br>
        위 인덱스 서명을 해석하면<br>
        인덱스 번호로 number가 올 수 있고 이 때 반환 값의 타입은 string이다. 라고 해석 할 수 있다.<br>
        따라서 secondItem의 타입은 string
    - 인덱스 서명은 딕셔너리 패턴을 가지고 있다.(Python의 그 딕셔너리가 맞다. Key : Value로 이루어진 쌍)
    - 딕셔너리 패터이기에 객체의 모든 속성이 반환 타입과 일치해야 한다.<br>
    아래처럼 작성하면 오류가 발생한다.(반환 타입과 불일치)

        ```javascript
        interface NumberDictionary {
            [index: string]: number;
            
            length: number;
            // 에러
            name: string;
        }        
        ```
        이 상황에서 오류를 해결할 방법이 있다.<br>
        반환 타입을 유니언 타입으로 지정하면 된다!
    
        ```javascript
        interface NumberOrStringDictionary {
            [index: string]: number | string;
            length: number;
            name: string;
        }
        ```
    - 추가로 인덱스 서명도 읽기 전용 속성을 사용할 수 있다!
        ```javascript
        interface ReadonlyStringArray {
            readonly [index: number]: string;
        }
        
        let myArray: ReadonlyStringArray = getReadOnlyStringArray();
        // 오류
        myArray[2] = "Mallory";
        ```
        간단히 속성 앞에 readonly 만 붙히면 된다.<br>
        아래 ```mArray[2] = "Mallory```는 읽기 전용 속성에 새로운 값을 할당하려 했기 때문에 오류가 발생한다!

<br>

## 3. 타입 확장
예시를 먼저 보도록 하자.
```javascript
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}
```
위 예시는 우편 배송에 필요한 요소를 담은 인터페이스이다.<br>
여기에 새로운 값을 추가하려면 다음과 같이 작성할 것 이다.
```javascript
interface AddressWithUnit {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
  unit: string;
}
```
그런데 이렇게 쓰면 코드도 길어지고, BasicAddress 인터페이스의 속성과 동일한게 너무 많다.<br>

**이 경우! 사용하면 좋은것이 바로 타입 확장이다.**<br>
**extends** 키워드를 사용해 타입 확장을 사용할 수 있다.
```javascript
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}

interface AddressWithUnit extends BasicAddress {
  unit: string;
}
```
위 예시처럼 객체 이름 뒤에 extends 키워드를 사용하고, 상속 받아올 객체의 이름을 적으면 된다.(자바의 상속처럼)<br>

그리고 한번에 여러개의 객체를 상속 받아올 수 도 있다.
```javascript
interface Colorful {
  color: string;
}
 
interface Circle {
  radius: number;
}
 
interface ColorfulCircle extends Colorful, Circle {}
 
const cc: ColorfulCircle = {
  color: "red",
  radius: 42,
};
```
위 예시의 ColorfulCircle 뒤에 쓰인 것 처럼, 콤마로 구분하여 객체 여러개를 상속 받을 수 있다.

<br>

## 4. 타입 교차
인터페이스를 확장하여 다른 타입으로 부터 새로운 타입을 구축함, **& 연산자**를 사용하여 정의함<br>
TypeScript에서 기존 객체 타입을 결합하는데, 교차 타입이라는 다른 구조를 제공<br>
```javascript
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
 
type ColorfulCircle = Colorful & Circle;
```
위 예시에 ```Colorful & Circle``` 이 부분이 타입 교차를 수행한 부분이다.<br>
타입 교차는 타입 확장과 동일한 동작을 수행하지만, **조금 다르다.**<br>
- 인터페이스
    - 서로 다른 두 객체에 같은 함수가 존재하고, 반환 타입이 다를 경우 타입 확장시 오류가 발생함
- 타입 교차
    -   인터페이스와 다르게 같은 함수가 존재하고, 반환 타입이 달라도, 아무런 문제 없이 결함함

간단히 말해, 충돌을 처리하는 방식에 차이가 존재한다! 라고 알고 있으면 된다.

<br>

## 5. 제네릭 객체 타입
객체를 하나 만든다고 가정하자.<br>
그런데 객체 안에 담을 속성의 타입을 원하는대로 지정하고 싶다(number, string, boolean, etc). 어떻게 하면 좋을까?
```javascript
interface NumberBox {
  contents: number;
}
 
interface StringBox {
  contents: string;
}
 
interface BooleanBox {
  contents: boolean;
}
```
이렇게 만들까? 그러면 여기에 새 값을 넣는 기능을 수행하는 함수를 만들어 볼까?
```javascript
function setContents(box: StringBox, newContents: string): void;
function setContents(box: NumberBox, newContents: number): void;
function setContents(box: BooleanBox, newContents: boolean): void;
function setContents(box: { contents: any }, newContents: any) {
  box.contents = newContents;
}
```
와! 너무 난잡하다.<br>
타입별로 객체를 만들고, 그거에 맞춰 전부 오버로딩해야한다.<br>
너무 번거롭고 귀찮다. 그렇기에 필요한게 바로 **제네릭 객체 타입!**<br>
위에서 쓴 난잡한 코드를 줄이자!
```javascript
interface Box<Type> {
  contents: Type;
}

function setContents<Type>(box: Box<Type>, newContents: Type){
    box.contents = newContents
}
```
보기도 편하고, 귀찮게 오버로딩 안해도 되고, 너무 좋다!<br>
애용하도록 하자!<br>
```javascript
type Box<Type> = {
  contents: Type;
};
```
위 예시처럼, 타입 별칭도 제네릭으로 쓸 수 있다.

<br>

## 6. 배열 타입
우리가 자주 써왔던 배열도 제네릭 객체 타입이였다!
```javascript
number[] == Array<number>
string[] == Array<string>
```
우리가 익히 써왔던 왼쪽의 배열 선언 방식은, 오른쪽을 축약어였다.

```javascript
interface Array<Type> {
  length: number;
  pop(): Type | undefined;
  push(...items: Type[]): number;
}
```
위 코드는 배열 객체의 일부분이다.
1. 길이를 가지고 있는 속성 length
1. 배열의 마지막 값을 제거하고, 그 값을 반환하는 pop메소드
1. 배열에 새 값을 추가하고, 해당 배열의 길이를 반환하는 push 메소드
이외에도 많은 속성과 메소드를 가지고 있다.<br>

배열 객체도 읽기 전용으로 만들수 있다!
```javascript
function doStuff(values: ReadonlyArray<string>) {
  const copy = values.slice();
  console.log(`The first value is ${values[0]}`);

  // 오류
  values.push("hello!");
}
```
읽기 전용 배열은 말 그대로 값을 읽을 수는 있지만, 쓰기가 불가능 하다.<br>
그렇기에 아래 push 메소드에서 오류가 발생한다.<br>

읽기 전용 배열을 만들 때 주의해야 할 점이 있다.<br>
읽기 전용 배열은 생성자가 없기에 new 키워드를 쓸 수 없다.
```javascript
// 오류!
new ReadonlyArray("red", "green", "blue");

// 굿!
const roArray: ReadonlyArray<string> = ["red", "green", "blue"];
Try
```
일반 배열을 ReadOnlyArray 타입으로 지정하고 사용하자.<br>
위에서 봤던 축약어처럼, 읽기 전용 배열도 축약어가 있다.<br>
```let x = readonly string[] = []``` \<-처럼 사용하면 된다.

그리고 읽기 전용 배열을, 일반 배열(읽고 쓰기가 가능한)에 할당하려고 하면 오류가 발생하니 주의하자.
```javascript
let x: readonly string[] = [];
let y: string[] = [];
 
x = y;
// 오 류
y = x;
```
그 이유는 읽기 전용 배열을 쓰기가 가능한 배열에 할당해버리면, 값이 변경될 수 있기에 TypeScript에서 할당할 수 없게함

<br>

## 7. 튜플 타입
포함된 요소의 수와, 특정 위치에 포함된 타입을 정확히 알고 있는 배열 타입의 한 종류
```javascript
type StringNumberPair = [string, number];

function doSomething(pair: [string, number]) {
  const a = pair[0];
  const b = pair[1];

  // 오 ~ 류
  const c = parin[2];
}
 
```
위 코드의 첫번째 줄 같이 선언하여 사용할 수 있다.<br>
1. 위 함수 내의 변수 a의 타입은 string
1. b의 타입은 number이다.
1. c처럼 요소의 수를 넘어선 값에 접근하려고 하면 오류가 발생한다.

튜플은 숫자를 리터럴 타입으로, 길이 속성을 가지는 객체 형태로도 만들 수 있다.
```javascript
interface StringNumberPair {
  length: 2;
  0: string;
  1: number;
 
  slice(start?: number, end?: number): Array<string | number>;
}
```

튜플은 선택적 속성도 가질 수 있다.<br>
```javascript
type Either2dOr3d = [number, number, number?];
 
function setCoordinate(coord: Either2dOr3d) {
  const [x, y, z] = coord;
  console.log(`Provided coordinates had ${coord.length} dimensions`);
}
```
1. 튜플의 선택적 속성은 요소의 끝에만 존재 할 수 있다.
1. 선택적 요소이기에, 튜플의 길이에 영향을 미침(위 예시에선 길이가 2 ~ 3이 될 수 있음)
1. 선택적 속성이기에 Type | undefined의 유니언 타입

선택적 속성과 더불어 나머지 매개변수도 사용할 수 있다.
```javascript
type StringNumberBooleans = [string, number, ...boolean[]];
type StringBooleansNumber = [string, ...boolean[], number];
type BooleansStringNumber = [...boolean[], string, number];
```
위 예시를 설명하자면 다음과 같다.
1. StringNumberBooleans는 처음 두 요소가 string, number, 그 뒤로 길이 제한 없이 boolean값이 있을 수 있음
1. StringBooleansNumber는 첫 요소가 string, 그 다음 임의의 수 만큼 boolean값이 오고, number로 끝남
1. 마지막으로 BooleansStringNumber는 첫 요소가 임의의 boolean값이고 string, number로 끝나는 튜플임

튜플에서 나머지 매개변수를 사용하게 되면 설정된 길이가 존재하지 않게 되고, 요소의 집합만이 존재한다.

마지막으로 튜플도 읽기 전용 튜플로 만들 수 있다.<br>
읽기 전용 배열 처럼 축약어가 있다.
```javascript
function doSomething(pair: readonly [string, number]) {
  // 동작
}
```
간단히 **readonly** 만 붙히면 된다.<br>

위에서 계속 설명 했지만, 읽기만 가능하고 쓰기가 불가능하다!<br>
이는 튜플도 마찬가지다.
```javascript
function doSomething(pair: readonly [string, number]) {
  // 오류
  pair[0] = "hello!";
}
```
위 예시는 읽기 전용 튜플에 값을 할당하려고 한다.<br>
읽기 전용으로 지정하면 쓰기가 불가능하다~