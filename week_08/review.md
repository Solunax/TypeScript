# Week8 Review
## 1. 제네릭(조금 더 자세히)
시작에 앞서 제네릭이 어떤 형태인지 다시 보고 가도록 하자

```javascript
function identity<Type>(arg : Type) : Type{
    return arg
}
```

위 예시 코드는 타입 매개변수로 지정한 타입을 인자로 받고, 그것을 그대로 반환하는 함수이다.<br>
제네릭은 위 코드의 Type과 같은 타입 매개변수를 사용한다.<br>

만약 제네릭을 안쓰면?
```javascript
function identity(arg : number) : number{
    return arg
}
```
위와 같은 함수를 타입별로(string, array ...)만들어 주어야 한다 :(<br>

그러면 any를 쓰면 되지 않느냐?<br>
- any는 어떻게 보면 제네릭일수 있다.<br>
- 그러나 개발자가 원하지 않는 부분에서 오류를 만들어낼 수 있는 가능성을 높이기에 사용에 주의가 필요함<br>

이 좋은 제네릭을 사용하려면 어떻게 호출해야할까?
```javascript
// 1번 : string
let output = identity<string>("Hello")
// 2번 : string
let output = identity("Hello")
```
위 두 가지 방법을 사용할 수 있다.<br>
- 1번 같은 경우에는 꺽쇠괄호를 사용해 타입을 명시하고, 그 뒤에 인자를 넣는 방법이다.<br>
- 2번은 1번에서 타입을 명시하는 부분만 없는데, 둘의 타입은 동일하다. 왜?<br>
- 우리 친절한 TypeScript는 타입을 자동으로 유추해 주기에, 인자의 타입을 가지고 함수의 타입 매개변수를 지정해 준다!
- 하지만 이도 만능은 아닌게, 코드가 복잡해져서 타입을 유추할 수 없어지면, 개발자가 1번 방법으로 타입을 명시해야 할 수도 있다.

<br>

## 2. 제네릭 타입 변수 작업
위에서 예시로 사용했던 코드를 조금 바꿔보자
```javascript
function loggingIdentity<Type>(arg : Type) : Type{
    console.log(arg.length)
    return arg
}
```
기존 예시 함수에서 인자의 길이를 반환하게끔 수정했다.<br>
그런데 이 코드는 TypeScript에서 에러라고 알려준다. 왜?
- Type에는 number를 비롯한 string, array 등등 다양한 타입이 타입 매개변수가 될 수 있음
- 그런데, 인자로 받은 arg가 length라는 속성이 있는지 없는지 알 수 없다.<br>
ex) string - length 있음, number - length 없음

그러면 이제 오류가 발생하지 않게끔 수정해보자
```javascript
// 1번
function loggingIdentity<Type>(arg : Type[]) : Type[]{
    console.log(arg.length)
    return arg
}

//2번
function loggingIdentity<Type>(arg : Array<Type>) : Array<Type>{
    console.log(arg.length)
    return arg
}
```
위 처럼 인자로 arg의 타입을 타입 매개변수 배열로 바꾸면, 오류가 해결된다.
- 배열은 length속성을 가지고 있다.

1번은 축약어([])를 사용한 버전, 2번은 축약어를 사용하지 않은 버전이다.

<br>

## 3. 제네릭 타입
위에서는 제네릭을 가지고 함수를 만들었지만, 제네릭을 타입으로 만들 수 도 있다

```javascript
function identity<Type>(arg : Type) : Type{
    return arg
}

let myIdentity : <Type>(arg: Type) => Type = identity
```

위 예시에서 ```myIdnetity```의 타입인 ```<Type>(arg: Type) => Type``` 이것과, identity 함수의 타입 매개변수, 인자, 반환타입과 일치하기에
```myIdnetity```에 identity 함수가 될 수 있음

```javascript
function identity<Type>(arg: Type): Type {
  return arg
}
 
let myIdentity: <Input>(arg: Input) => Input = identity
```
먼저 본 예시와 지금 예시의 차이점이 보이는가?<br>
- 타입 매개변수의 이름이 Type과 Input으로 서로 다르다,
하지만 오류가 발생하지 않는다.<br>
- 타입 변수의 수, 타입 변수의 사용 방식에 따라 타입 매개변수의 이름을 다르게 사용할 수 있다.

```javascript
function identity<Type>(arg: Type): Type {
  return arg
}
 
let myIdentity: { <Type>(arg: Type): Type } = identity
```
위 코드처럼 객체 리터럴 타입의 함수 호출 서명으로도 작성할 수 있다.<br>

위 객체 리터럴 방식을 사용해 인터페이스를 생성하고, 이 인터페이스를 활용할 수 있음
```javascript
// 1번
interface GenericIdentityFn {
  <Type>(arg: Type): Type
}
 
function identity<Type>(arg: Type): Type {
  return arg
}
 
let myIdentity: GenericIdentityFn = identity

// 2번
interface GenericIdentityFn<Type> {
  (arg: Type): Type
}
 
function identity<Type>(arg: Type): Type {
  return arg
}
 
let myIdentity: GenericIdentityFn<number> = identity
```
위 두 예시는 객체 리터럴 방식을 사용한 인터페이스를 사용한 예제이다.<br>
- myIdentity 변수가 GenericIdentityFn 타입이다.<br>
- GenericIdentityFn는 ```<Type>(arg: Type): Type``` 객체 리터럴
- GenericIdentityFn와, identity 함수의 타입 매개변수, 인자, 반환타입이 일치해서 할당이 가능하다.

2번은 모양이 1번과 형태가 조금 다른것을 확인할 수 있을것이다.<br>
- 2번의 방식은 제네릭 매개변수를 인터페이스의 매개변수로 사용한 형태이다.
- 이렇게 사용하면 인터페이스의 모든 멤버가 같은 타입 매개변수를 사용한다.
- ```GenericIdentityFn<number>``` 이렇게 되면, 해당 인터페이스의 타입 매개변수는 number가 됨

<br>

## 4. 제네릭 클래스
위 마지막 예시에서 사용한 제네릭 인터페이스와 형태가 유사함
```javascript
class GenericNumber<NumType> {
  zeroValue: NumType
  add: (x: NumType, y: NumType) => NumType
}
```
제네릭 클래스는 다음의 구성요소로 이루어진다.
- 클래스의 이름(GenericNumber)
- 타입 매개변수(<NumType>)

이 제네릭 클래스 객체를 생성해보자
```javascript
let myGenericNumber = new GenericNumber<number>()
myGenericNumber.zeroValue = 0
myGenericNumber.add = function (x, y) {
  return x + y
}
```
- GenericNumber객체인 myGenericNumber를 생성
- myGenericNumber의 zeroValue를 0으로
- myGenericNumber의 add 매소드는 x, y 두 인자를 더한 값을 반환하는 함수를 할당

위 예시는 타입 매개변수를 number로 지정하고 생성한 예시이고

다른 타입으로도 얼마든지 생성할 수 있다.
아래는 string 타입으로 생성한 예시이다.
```javascript
let stringNumeric = new GenericNumber<string>()
stringNumeric.zeroValue = ""
stringNumeric.add = function (x, y) {
  return x + y
}
```
number타입일 때와 다르게 zeroValue가 빈 문자열이고, add 메소드는 두 문자열을 합친 결과를 반환한다.

<br>

## 5. 제네릭 제약조건
예전에 제네릭을 잠깐 다루었을 때 봤었을 것이다. extends를 사용해 제약 조건을 걸 수 있다.

```javascript
function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length)
  return arg
}
```
위에서 다루었던 오류가 발생한 예시이다. 설명하기를 lenght 속성이 없는 타입이 인자로 올 수 있기에 오류가 발생한다고 했었다.<br>
위에선 배열을 사용해서 해결했으나, 제약조건을 사용해도 해결할 수 있다.

```javascript
function loggingIdentity<Type extends {length : number}>(arg: Type): Type {
  console.log(arg.length)
  return arg
}
```
이렇게 작성하면, 배열뿐만 아니라, length 속성을 가지고 있는 모든 타입들을 인자로 받을 수 있다.<br>
이 상태에서 ```loggingIdentity(5)``` 이렇게 함수를 호출하면 오류가 발생한다.(number는 length 속성이 없다!)

<br>

## 6. 제네릭 제약조건에서 타입 매개변수 사용
```javascript
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key]
}
 
let x = { a: 1, b: 2, c: 3, d: 4 }
 
// 동작
getProperty(x, "a")

// 오류
getProperty(x, "m")
```
위 코드를 분석해보자
- getProperty 함수는 객체에서 특정 값을 가져오는 함수임
- Key 타입 매개변수는 Type 타입 매개변수에 있는 키의 값으로 제한함
- x는 a, b, c, d의 키가 있고, 각각 1, 2, 3, 4 값을 가짐
- 첫번째 getProperty 함수는 1을 반환함
- 두번째 getProperty 함수는 x에 m이라는 키 값이 존재하지 않기에 오류가 발생한다.

위 예시처럼, 다른 타입 매개변수로 제한된 타입 매개변수를 선언할 수 있다.

<br>

## 7. 제네릭 클래스 타입 사용
제네릭을 사용하는 TypeScript에서 팩토리를 생성할 때 생성자 함수로 클래스 타입을 참조해야 한다.
```javascript
function create<Type>(c: { new (): Type }): Type {
  return new c()
}
```
위 예시처럼, ```new() : Type``` 생성자 함수를 타입에 넣는다.
위 코드를 조금 확장하면 아래와 같이 작성할 수 있다.
```javascript
class BeeKeeper {
  hasMask: boolean
}
 
class ZooKeeper {
  nametag: string
}
 
class Animal {
  numLegs: number
}
 
class Bee extends Animal {
  keeper: BeeKeeper
}
 
class Lion extends Animal {
  keeper: ZooKeeper
}
 
function createInstance<A extends Animal>(c: new () => A): A {
  return new c()
}
 
createInstance(Lion).keeper.nametag
createInstance(Bee).keeper.hasMask
```
- createInstance 함수는 Animal 클래스의 속성을 포함해야한다.
- Lion 클래스는 keepr의 타입이 ZooKeeper이기에 nametag 속성에 접근할 수 있다.
- Bee 클래스는 keeper의 타입이 BeeKeeper이기에 hasMask 속성에 접근할 수 있다.

위와 같은 패턴은 mixins 디자인 패턴을 만드는데 사용된다.