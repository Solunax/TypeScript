# Week9 Review
## 1. keyof 타입 연산자
해당 키의 문자열 또는 숫자 리터럴 조합을 생성하는 연산자

```javascript
type Point = { x: number, y: number }
type P = keyof Point

const a :P = "x"
const b :P = "z"
```
- 예제 상단의 Point 타입의 속성은 x, y가 있다
- p 타입은 keyof 연산자에 의해 "x" | "y" 와 같다
- a는 오류가 발생하지 않지만, b는 해당 문자열 리터럴이 p에 존재하지 않기에 오류가 발생한다

만약 타입에 number 또는 string 인덱스 서명이 있는 경우 keyof는 해당 타입을 반환한다.
```javascript
type Arrayish = { [n: number]: unknown }
type A = keyof Arrayish

type Mapish = { [k: string]: boolean }
type M = keyof Mapish
```
- A 타입은 Arrayrish 타입의 인덱스 서명의 타입인 number가 된다.
- M 타입은 Mapish 타입의 인덱스 서명의 타입인 string이어야 하지만, Javscript에서 인덱스 서명의 arr["0"]와 arr[0]는 같기에 string | number의 유니언타입이 됨

<br>

## 2. typeof 타입 연산자
Javascript에서 기존에 typeof라는 연산자가 존재했음<br>
TypeScript에선 타입 컨텍스드에서 변수 또는 속성 타입을 참조하기 위해 사용할 수 있음
```javascript
// JavaScript 
console.log(typeof "Hello world")

// TypeScript
let s = "hello"
let n : typeof s
```
- JavaScript의 예시는 "Hello world"의 타입인 string을 출력함
- TypeScript의 예시는 변수 s의 타입을 변수 n의 타입으로 지정함(string)

typeof는 기본 형식에서 유용하지 않으나, 다른 타입 연산자와 결합하여 많은 패턴을 편리하게 표현할 수 있음

```javascript
type Predicate = (x: unknown) => boolean
type K = ReturnType<Predicate>
```
- Predicate 타입은 boolean을 반환하는 함수 타입
- K는 boolean 타입이 됨(반환 타입)

```javascript
function f() {
  return { x: 10, y: 3 }
}

// 오류
type P1 = ReturnType<f>

// 오류 X
type P2 = ReturnType<typeof f>
```
- 함수 f는 x, y값이 있는 객체를 반환함
- P1은 f를 타입으로 지정하였는데, 제네릭 인자 자리에 함수가 아닌타입이 와야한다
- P2는 P1의 문제를 해결한 코드로 f의 타입을 제네릭 인자로 사용함 ```{ x : number ; y : number}```

<br>

## 3. 인덱싱 액세스 타입
인덱싱된 액세스 타입을 사용해 다른 타입의 속성을 조회할 수 있음

```javascript
type Person = { age: number, name: string, alive: boolean }
type Age = Person["age"]
```
- Person 타입의 속성으로 age, name, alive가 있음
- Age 타입은 Person 타입의 age 속성의 타입을 가져옴(number)

인덱싱 타입은 그 자체가 유니언 또는 keyof, 다른 타입을 전체적으로 사용할 수 있음

```javascript
type Person = { age: number, name: string, alive: boolean }
type I1 = Person["age" | "name"]

type I2 = Person[keyof Person]
 
type AliveOrName = "alive" | "name"
type I3 = Person[AliveOrName]
```
- Person 타입은 위 예제와 동일함
- I1 타입은 Person의 속성중 age와 name의 타입을 가져옴(string | number)
- I2 타입은 Person 객체의 모든 속성 값의 타입을 가져옴(string | number | boolean)
- I3 타입은 alive와 name의 리터럴 유니언 타입인 AliveOfName으로 Person의 속성의 타입을 가져옴(number, boolean)

```javascript
type Person = { age: number, name: string, alive: boolean }
type I1 = Person["alve"]
```
만약 위 예시처럼 존재하지 않는 속성을 인덱싱하려고 하면 오류가 발생한다.<br>

number를 사용해 배열의 타입을 가져오는 방법도 있음, 이를 typeof와 연결하여 배열 리터럴 요소의 타입을 쉽게 가져올 수 있음
```javascript
const MyArray = [
  { name: "Alice", age: 15 },
  { name: "Bob", age: 23 },
  { name: "Eve", age: 38 },
]
 
type Person = typeof MyArray[number]
type Age = typeof MyArray[number]["age"]
type Age2 = Person["age"]
```
- Person 타입은 MyArray의 배열 요소의 속성과 타입을 가져옴(name : string, age : number)
- Age는 MyArray의 배열 요소 중 age속성의 타입만 가져옴(number)
- Age2는 Age과 동일한 결과는 같음, Person타입의 age 속성의 타입을 가져옴(number)

인덱싱할 때 타입만 사용할 수 있음, 상수 값을 사용하여 변수를 참조할 수 없다.

```javascript
//오류
const key = "age"
type Age = Person[key]

type key2 = "age"
type Age2 = Person[key]
```
- Age는 상수를 사용해 인덱싱을 시도했기에 오류가 발생함
- Age2처럼 고쳐서 사용하면 오류 없이 인덱싱할 수 있음

<br>

## 4. 조건부 타입
입력과 출력 타입간의 관계를 설명하는데 도움이 된다.

```javascript
interface Animal {
  live(): void
}

interface Dog extends Animal {
  woof(): void
}
 
type Example1 = Dog extends Animal ? number : string
type Example2 = RegExp extends Animal ? number : string

// SomeType extends OtherType ? TrueType : FalseType

```
- Dog 인터페이스는 Animal 인터페이스를 상속받음
- 위 코드의 주석의 형식을 사용해 조건부 타입을 사용할 수 있다.
    - 특정 타입이 다른 타입에 포함되어 있으면 : 좌측을, 그게 아니면 : 우측을 할당한다.
- Example1은 Dog 타입이 Animal 타입을 상속받았으므로 number 타입이 됨
- Example2는 RegExp 타입이 Animal 타입을 상속받지 않았기에 string 타입이 됨

조건부 타입은 제네릭과 함께 사용하면 좋다.

```javascript
interface IdLabel {
  id: number
}
interface NameLabel {
  name: string
}

// 오버로딩
function createLabel(id: number): IdLabel
function createLabel(name: string): NameLabel
function createLabel(nameOrId: string | number): IdLabel | NameLabel
function createLabel(nameOrId: string | number): IdLabel | NameLabel {
  throw "unimplemented"
}

// 제네릭 + 조건부 타입
type NameOrId<T extends number | string> = T extends number ? IdLabel : NameLabel
```
- 들어온 매개변수의 타입에 따라 IdLabel과 NameLabel의 타입을 부여하는 함수이다.
- 오버로딩 함수를 보면 내용이 너무 길고, 읽기도 어렵다.
- 아래의 제네릭 + 조건부 타입으로 작성한 내용을 보면, 오버로딩 함수와 같은 동작을 수행함에도 짧고 간결한 것을 확인할 수 있다.

아래의 예제는 위 제네릭 + 조건부 타입을 사용한 예제이다.
```javascript
function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  throw "unimplemented"
}
 
let a = createLabel("typescript")
let b = createLabel(2.8)
let c = createLabel(Math.random() ? "hello" : 42)
```
- 변수 a는 인자가 string 타입, 따라서 NameLabel 타입
- 변수 b는 인자가 number 타입, 따라서 IdLable 타입
- 변수 c는 인자가 number와 string 둘 다 올 수 있기에 NameLabel | IdLabel 타입이 됨

<br>

### 조건부 타입 제약조건
조건부 타입 체크를 통해 타입 가드로 좁히기를 수행하는 것 처럼 제네릭을 제약할 수 있음

```javascript
type MessageOf<T extends { message: unknown }> = T["message"]
 
interface Email {
  message: string
}
 
type EmailMessageContents = MessageOf<Email>
```
- 타입 매개변수 T는 message 속성이 있다는 제약을 줌
- 따라서 T는 message라는 속성을 가지게 된다.
- EmailMessageContents 타입은 MessageIf\<Email>이라는 타입을 가진다.
- 여기서 Email에 message가 존재하기에 EmailMessageContents의 타입은 message의 타입인 string이 됨

MessageOf가 인자로 아무 타입이나 받고, message 속성의 존재 여부에 따라 해당 타입 또는 never타입을 반환하게 해보자

```javascript
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never
 
interface Email {
  message: string
}
 
interface Dog {
  bark(): void
}
 
type EmailMessageContents = MessageOf<Email>
type DogMessageContents = MessageOf<Dog>
```
- 기존의 제약 조건을 제거하고, 조건부 타입 제약조건을 사용하면 속성 여부에 따라 다른 타입을 지정하게 할 수 있다.
- EmailMessageContents의 경우 Email에 message 속성이 있기에 message 속성의 타입인 string이 지정됨
- DogMessageContents의 경우에는 Dog에 message 속성이 없기에 never타입이 지정됨

### 조건부 타입 내에서 추론
infer 키워드를 사용해 조건이 참일 때 비교하는 타입을 추론할 수 있음
```javascript
type GetReturnType<Type> = Type extends (...args: never[]) => infer Return ? Return : never
 
type Num = GetReturnType<() => number>
type Str = GetReturnType<(x: string) => string>
type Bools = GetReturnType<(a: boolean, b: boolean) => boolean[]>
```
- 반환 타입을 Return으로, Return의 타입으로 지정
- Num의 타입은 number
- Str의 타입은 string
- Bools의 타입은 boolean

<br>

### 분산적인 조건부 타입
제네릭 타입은 유니언 타입을 만나면 분산적으로 동작함
```javascript
type ToArray<Type> = Type extends any ? Type[] : never
 
type StrArrOrNumArr = ToArray<string | number>
```
- StrArrOrNumArr의 타입은 string[] | number[] 유니언으로 지정됨

만약 위처럼 나누지 않고, (타입 | 타입)[]의 반환 값을 가지게 하려면 조금만 코드를 바꾸면 된다.

```javascript
type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never

type StrArrOrNumArr = ToArrayNonDist<string | number>
```
- 위와는 달리 extends 키워드의 양 옆을 대괄호로 감싸면 된다.
- 이때 StrArrOrNumArr의 타입은 (string |
 number)[]

<br>

## 5. 매핑된 타입
중복을 피하기 위해 다른 타입을 바탕으로 새로운 타입을 생성<br>
이전에 선언하지 않았던 속성 타입을 선언할 수 있는 인덱스 시그니처 문법으로 구성

```javascript
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean
}

type FeatureFlags = {
  darkMode: () => void
  newUserProfile: () => void
}
 
type FeatureOptions = OptionsFlags<FeatureFlags>
```
- FeatureOptions의 타입은 OptionFlags 타입에 의해 매핑됨
- 인자로 들어온 타입의 속성 이름은 그대로 사용하고, 해당 속성의 타입을 변경함
- FeatureOptions = {darkMode : boolean, newUserProfile : boolean }이 됨

<br>

### 매핑 수정자
readonly, ?(선택적) 수정자를 추가하거나 제거할 수 있음
- 추가할 때 +를 사용
- 삭제할 떼 -를 사용
- 접두사(+, -)를 붙히지 않으면 +로 간주함

```javascript
type CreateMutable<Type> = {
  -readonly [Property in keyof Type]: Type[Property]
};
 
type LockedAccount = {
  readonly id: string
  readonly name: string
};
 
type UnlockedAccount = CreateMutable<LockedAccount>
```
- 위 예시는 LockedAccount 타입의 readonly 속성을 모두 제거한 타입을 UnlockedAccount에 할당한다

```javascript
type Concrete<Type> = {
  [Property in keyof Type]-?: Type[Property]
};
 
type MaybeUser = {
  id: string
  name?: string
  age?: number
};
 
type User = Concrete<MaybeUser>;
```
- 위 예시는 MaybeUser 타입의 속성에서 선택적 속성을 제거한 타입을 User에 할당한다

<br>

### as를 통한 키 재매핑
TypeScript 4.1 이상에서 as를 사용해 재매핑을 할 수 있음

```javascript
type Getters<Type> = {
    [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
};
 
interface Person {
    name: string;
    age: number;
    location: string
}
 
type LazyPerson = Getters<Person>
```
- $는 템플릿 리터럴 타입
- Type에서 속성의 이름을 가져와 첫 글자를 대문자로 변환함
- get속성이름 으로 속성 이름이 변경됨
- 따라서 LazyPerson은 { getName : () => string; getAge : () => number; getLocation : () => string}으로 매핑됨

<br>

### 추가 탐색
매핑된 타입은 조건부 타입을 비롯한 다른 타입 조작 기능들과 잘 동작한다
```javascript
type ExtractPII<Type> = {
  [Property in keyof Type]: Type[Property] extends { pii: true } ? true : false
};
 
type DBFields = {
  id: { format: "incrementing" }
  name: { type: string; pii: true }
}
 
type ObjectsNeedingGDPRDeletion = ExtractPII<DBFields>
```
- pii 속성의 존재 및 true/false 여부에 따라 다른 결과를 반환함
- ObjectNeedingGDPRDeletion의 속성은 { id : false, name : true}가 됨

<br>

## 6. 템플릿 리터럴
문자열 리터럴을 기반으로 유니언을 통해 확장가능<br>
JavaScript의 템플릿 리터럴 문자열과 통일하나, 타입의 위치에 사용된다<br>
구체적인 리터럴 타입과 함께 사용할 경우, 템플릿 리터럴은 내용을 연결하여 새로운 문자열 리터럴 타입을 생성함
```javascript
type World = "world"
type Greeting = `hello ${World}`
```
- Greeting 타입은 "Hello world"로 지정됨
- 주의할점은 템플릿 문자열을 사용할 때 '따옴표가 아닌 `(탭키 위의 물결표)를 사용해야 함

보간된 위치에서 유니언 타입을 사용할 수 있음
```javascript
type EmailLocaleIDs = "welcome_email" | "email_heading"
type FooterLocaleIDs = "footer_title" | "footer_sendoff"
 
type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`
```
- 템플릿 리터럴의 각 보간 위체에 대해 유니언을 교차곱셈됨
- AllocaeIDs는 "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"의 유니언 타입(곱셈)

<br>

### 타입의 문자열 유니언
템플릿 리터럴의 진가는 타입 내부의 정보를 기반으로 새 문자열을 정의할때 나타남
```javascript
const passedObject = {
  firstName: "Saoirse",
  lastName: "Ronan",
  age: 26,
}

type PropEventSource<Type> = {
  on(eventName: `${string & keyof Type}Changed`, callback: (newValue: any) => void): void
}
```
- passedObject의 값이 변경될 때 사용될 이벤트와 콜백함수를 생성하는 예시임
- 속성의 이름을 들고와서 (속성이름)Changed라는 이벤트 이름을 만들고, 콜백을 생성함

<br>

### 템플릿 리터럴을 사용한 추론
- newValue에 name이면 string, age면 number가 올 것을 추론이 가능함
- 위 예시를 작성할 때 newValue를 any타입으로 지정하였음, 템플릿 리터럴 타입을 사용하면 콜백의 첫번째 인수와 동일한 타입이 되도록 할 수 있음

```javascript
type PropEventSource<Type> = {
    on<Key extends string & keyof Type>
        (eventName: `${Key}Changed`, callback: (newValue: Type[Key]) => void ): void
}

person.on("firstNameChanged", newName => {
    console.log(`new name is ${newName.toUpperCase()}`)
})
```
- on에 제네릭 Key를 사용해 Type의 속성 이름을 Key로 가져옴
- Key를 이용해 ```newValue : Type[Key]```와 같이 사용해 newValue의 타입 속성과 같은 타입으로 지정함
- firstNameChanged 이벤트의 콜백으로 지정된 문자열을 출력하는 함수를 추가했다.(새로운 이름을 대문자로)

추론은 문자열을 분해하고 다른 방식으로 재구성하기 위해 다른 방식으로 결합될 수 있다

<br>

### 고유 문자열 조작 타입
문자열 조작을 돕기 위해 TypeScript에는 문자열 조작에 사용할 수 있는 타입 집합이 포함되어 있음

```javascript
type Greeting = "hello, world"
type ShoutyGreeting = Uppercase<Greeting>
type QuietGreeting = Lowercase<Greeting>
type CapitalizeGreeting = Capitalize<LowercaseGreeting>
type UncomfortableGreeting = Uncapitalize<UppercaseGreeting>
```
- UpperCase : 문자열 전체를 대문자로
- LowerCase : 문자열 전체를 소문자로
- Capitalize : 문자열의 첫 글자만 대문자로, 나머진 그대로
- UncapitalLzie : 문자열의 첫 글자만 소문자로, 나머진 그대로