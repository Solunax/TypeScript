# Week11 Review
## Classes
- ES2015에 도입된 키워드를 완벽 지원함
- JavaScript의 기능과 마찬가지로, TypeScript는 타입 주석 및 기타 구문을 사용해 클래스와 다른 타입 간의 관계를 표현 가능함

<br>

## 1. 클래스 멤버
클래스에 쓰기 가능한 공용 속성

```javascript
class Point {
  x: number
  y: number
}

const pt = new Point()
pt.x = 0
pt.y = 0
```
- 위 예시처럼 클래스의 필드에 타입 지정 가능
- 필드에 타입을 지정하지 않으면 any로 간주함

```javascript
class Point {
  x = 0
  y = 0
}
 
const pt = new Point()
console.log(`${pt.x}, ${pt.y}`)

// 오류
const ps = new Point()
ps.x = "0"
```
- 필드에 초기 값이 있을 수 있음(x = 0, y = 0)
- 클래스가 인스턴스화 될 때 자동으로 초기 값으로 초기화 진행
- ps.x의 타입은 number인데 string을 할당하려고 하기에 오류가 발생함

<br>

### 1-1. strictPropertyInitialization
생성자에서 클래스의 필드를 초기화해야 하는지의 여부를 제어함

```javascript
class BadGreeter {
  name: string
}
```
- strictPropertyInitialization 속성을 사용하고 위 예시처럼 클래스를 생성하면 오류가 발생함
    - 생성자에서 필드를 초기화하고 있지 않음!

```javascript
class GoodGreeter {
  name: string
 
  constructor() {
    this.name = "hello"
  }
}
```
- 위 예시처럼 construcor() 생성자를 사용해 필드를 초기화 해야 함
    - 파생 클래스에서 메소드를 재정의하고 멤버를 초기화할 수 없을 수 도 있으므로, 생성자에서 필드를 초기화 해야함

생성자가 아닌 다른 방법을 통해 필드를 초기화할 경우, 확정 할당 어선셜 연산자(!) 를 사용하면 된다.
```javascript
class OKGreeter {
  name!: string
}
```

<br>

### 1-2. readonly
- 필드에 접두사로서 붙일 수 있음
- 생성자 외부의 필드에 할당되지 않음(읽기 전용)

```javascript
class Greeter {
  readonly name: string = "world"
 
  constructor(otherName?: string) {
    if (otherName !== undefined) {
      this.name = otherName
    }
  }
 
  err() {
    // Error 1
    this.name = "not ok"
  }
}

const g = new Greeter()
// Error 2
g.name = "also not ok"
```
- Error 1, 2 모두 읽기 전용 필드인 name에 값을 할당하려고 함
- 읽기 전용 필드에 값을 할당할 수 없기에 오류가 발생함

<br>

### 1-3. 생성자
- 함수와 유사함
- 타입 주석, 기본값 및 오버로드가 있는 매개변수 추가 가능

```javascript
class Point {
  x: number
  y: number
 
  constructor(x = 0, y = 0) {
    this.x = x
    this.y = y
  }
}
```
- 위 예시의 constructor()가 생성자임
- 생성지 인자 값을 입력하면 해당 인자값으로 초기화
- 인자 값을 넘기지 않으면 생성자의 기본값인 0으로 초기화함

```javascript
class Point {
  constructor(x: number, y: string)
  constructor(s: string)
  constructor(xs: any, y?: any) {
  }
}
```
- 위 예시는 생성자를 오버로딩한 예시임
- 각각 다른 타입의 매개변수를 받음

<br>

생성자와 함수의 차이점
1. 생성자는 타입 매개변수를 가질 수 없음, 타입 매개변수는 외부 클래스 선언에 속함
1. 생성자는 반환 타입을 가질 수 없음, 클래스의 인스턴스 타입은 항상 반환되는 타입임

<br>

### 1-4. super()
- JavaScript와 마찬가지로 기본 클래스가 있는 경우, 이를 사용하기 전 생성자에서 super()를 호출해야 함

```javascript
class Base {
  k = 4
}
 
class Derived extends Base {
  constructor() {
    console.log(this.k)
    super()
  }
}
```
- super()를 통해 부모 클래스의 생성자 호출을 진행해야 함
- 만약 super()를 작성하지 않거나, 위 예시처럼 생성자의 맨 앞에 배치하지 않으면 오류가 발생함
- TypeScript에서는 super()가 필요할때 알려준다!

<br>

### 1-5. 메소드
- 클래스의 함수 속성
- 메소드는 함수 및 생성자로 동일한 타입의 주석을 사용할 수 있음

```javascript
class Point {
  x = 10
  y = 10
 
  scale(n: number): void {
    this.x *= n
    this.y *= n
  }
}
```
- scale 메소드는 Point x, y필드의 값을 n만큼 곱한 값을 할당함

표준 타입 주석 이외에 TypeScript는 메소드에 새로운 내용을 추가하지 않는다.<br>
메소드 본문에서 this 키워드를 통해 필드와 다른 메소드에 접근할 수 있음

```javascript
let x: number = 0
 
class C {
  x: string = "hello"
 
  m() {
    x = "world"
  }
}
```
- 위 예시처럼 this를 사용하지 않고 x에 접근하면?
    - 맨 위에 선언한 변수 x에 접근하게 된다.
    - string 타입인 C.x에 number 타입의 0을 할당하려고 하니 오류가 발생한다.

<br>

### 1-6. Getter / Setter
- 클래스의 접근자

```javascript
class C {
  _length = 0
  get length() {
    return this._length
  }
  set length(value) {
    this._length = value
  }
}
```
- 위 예시처럼 get, set을 사용해 접근자를 생성할 수 있음

<br>

TypeScript의 접근자 규칙
1. get이 존재는 하지만 설정하지 않은 경우, 속성은 읽기전용이 됨
1. setter 매개변수의 타입이 지정되지 않을 경우, getter의 반환 타입에서 유추함
1. getter와 setter는 멤버 가시성이 동일해야 함

<br>

TypeScript 4.3이후, get/set을 위해 서로 다른 타입의 접근자를 가질 수 있음
```javascript
class Thing {
  _size = 0
 
  get size(): number {
    return this._size
  }
 
  set size(value: string | number | boolean) {
    let num = Number(value)
 
    if (!Number.isFinite(num)) {
      this._size = 0
      return
    }
 
    this._size = num
  }
}
```
- size의 setter로 string, number, boolean을 받을 수 있음
- Number.isFinite 메소드로 값을 판단함
    - 유한수가 아니면 size에 0을 할당
    - 유한수면 num을 size에 할당

<br>

### 1-7. 인덱스 서명
- 클래스는 인덱스 서명을 선언할 수 있음
- 다른 개체 타입에 대한 인덱스 서명과 동일하게 작동함

```javascript
class MyClass {
  [s: string]: boolean | ((s: string) => boolean)
 
  check(s: string) {
    return this[s] as boolean
  }
}
```
- 인덱스 서명은 메소드 타입도 캡처해야 하므로 다른 타입을 유용하게 사용하는 것은 쉽지 않음
- 일반적으로 인덱스 데이터는 클래스 인스턴스 보다는 다른 위치에 저장하는 것이 좋다!

<br>

## 2. 클래스 상속
- 오브젝트 지향인 다른 언어들 처럼 JavaScript의 클래스는 기본 클래스에서 상속받을 수 있음

<br>

### 2-1. implements
- implements를 사용하여 클래스가 특정 인터페이스를 충족하는지 확인 가능
- 클래스가 제대로 구현되지 않았다면 오류가 발생함

```javascript
interface Pingable {
  ping(): void
}
 
class Sonar implements Pingable {
  ping() {
    console.log("ping!")
  }
}
 
class Ball implements Pingable {
  // Error
  pong() {
    console.log("pong!")
  }
}
```
- Sonar는 Pingalbe 인터페이스를 제대로 구현함
- Ball은 ping이 아닌 pong 을 구현했기에 오류가 발생함
- ```Class C implements A, B{}``` 처럼 작성하여 여러개의 인터페이스를 구현할 수 있음

<br>

### 2-2. implements 주의사항
- implements 절은 클래스를 인터페이스 타입으로 취급할 수 있는 검사일 뿐임
- 클래스의 타입이나 메소드는 변경되지 않음

```javascript
interface Checkable {
  check(name: string): boolean
}
 
class NameChecker implements Checkable {
  check(s) {
    return s.toLowercse() === "ok"         
  }
}
```
- s의 타입은 ```name:string```의 영향을 받음, 그러나 매개 변수의 타입을 명시해야 함
- implements는 클래스 본문을 확인할 뿐, 해당 타입을 추론하는 방법은 변경하지 않음

<br>

마찬가지로 선택적 속성을 가진 인터페이스를 구현한다고 해서, 해당 속성이 생성되지 않음

```javascript
interface A {
  x: number
  y?: number
}

class C implements A {
  x = 0
}

const c = new C()
c.y = 10
```
- 클래스 c는 y 값을 초기화하지 않음(존재하지 않는 필드)
- 따라서 필드 y에 값을 할당할 수 없음

<br>

### 2-3. extends
- 클래스는 확장이 가능함
- 파생 클래스의 기본 클래스의 모든 속성과 메소드를 가짐
- 추가적인 멤버도 정의할 수 있음
```javascript
class Animal {
  move() {
    console.log("Moving along!")
  }
}
 
class Dog extends Animal {
  woof(times: number) {
    for (let i = 0; i < times; i++) {
      console.log("woof!")
    }
  }
}
 
const d = new Dog()
d.move()
d.woof(3)
```
- 클래스 d의 타입은 Animal 클래스를 상속받은 Dog클래스
- Dog클래스는 Animal클래스의 move 메소드를 사용할 수 있음

<br>

### 2-4. 메소드 오버라이딩
- 파생 클래스는 기본 클래스의 필드 및 속성을 재정의 할 수 있음
- super() 키워드를 사용해 기본 클래스 메소드에 접근할 수 있음
- JavaScript의 클래스는 단순한 룩업 개체이기에 슈퍼필드라는 개념은 없음
- TypeScript는 파생 클래스가 기본 클래스의 하위 타입임

```javascript
class Base {
  greet() {
    console.log("Hello, world!")
  }
}
 
class Derived extends Base {
  greet(name?: string) {
    if (name === undefined) {
      super.greet()
    } else {
      console.log(`Hello, ${name.toUpperCase()}`)
    }
  }
}
 
const d = new Derived()
d.greet()
d.greet("reader")
```
- greet 메소드를 Derived 클래스에서 재정의함
- name에 아무 값도 전달하지 않으면 기본 클래스의 greet 메소드를 호출
- name에 값을 전달하면 재정의한 결과를 보여줌

<br>

기본 클래스 참조를 통해 파생 클래스의 인스턴스를 참조하는 것은 일반적임

```javascript
const b: Base = d
b.greet()
```
- Derived 클래스인 d를 Base 클래스인 b에 할당할 수 있음

<br>

### 2-5. 타입 전용 필드 선언
- target >= ES2022 또는 useDefineForClassFields가 참일 때
- 부모 클래스 생성자가 완료 된 후 클래스 필드가 초기화되어 부모클래스에서 설정한 값을 덮어씀
- 상속된 필드에 대해 정확한 타입만 선언하려는 경우 문제가 발생함
    - 필드 선언에 런타임 효과가 없어야 함을 나타내는 선언을 작성해야 함

```javascript
interface Animal {
  dateOfBirth: any
}
 
interface Dog extends Animal {
  breed: any
}
 
class AnimalHouse {
  resident: Animal
  constructor(animal: Animal) {
    this.resident = animal
  }
}
 
class DogHouse extends AnimalHouse {
  declare resident: Dog
  constructor(dog: Dog) {
    super(dog)
  }
}
```
- DogHouse 클래스의 declare는 JavaScript의 코드로 변환하지 않고 타입이 올바른지만 확인함

<br>

### 2-6. 초기화 순서
```javascript
class Base {
  name = "base"
  constructor() {
    console.log("My name is " + this.name)
  }
}
 
class Derived extends Base {
  name = "derived"
}

const d = new Derived()
```
- 위 예시의 초기화 순서는 다음과 같다
    1. 기본 클래스의 필드 초기화
    1. 기본 클래스 생성자 실행
    1. 파생 클래스 필드 초기화
    1. 파생 클래스 생성자 실행
- 파생 클래스 생성시 기본 클래스의 생성자가 실행됨(super)

<br>

### 2-7. 기본 제공 타입 상속
- ES2015에서 객체를 반환하는 생성자는 암시적으로 super를 대체함
- 생성된 생성자 코드가 super의 잠재적 반환 값을 캡처하여 대체
- 따라서 subclassing 에러, 배열 등의 하위 분류가 예상대로 작동하지 않을 수 있음

```javascript
class MsgError extends Error {
  constructor(m: string) {
    super(m)
  }
  sayHello() {
    return "hello " + this.message
  }
}
```
- 메소드가 위 예시와 같은 하위 클래스를 구성하여 반환된 객체에 대해 undefined가 될 수 있으므로 sayHello를 호출하면 오류가 발생
- 하위 클래스의 인스턴스와 해당 인스턴스 간에 instanceof가 중단되므로 new MsgError() instanceof가 false를 반환함

<br>

super() 호출 후 수동으로 조정하는 것이 좋음

```javascript
class MsgError extends Error {
  constructor(m: string) {
    super(m)
    Object.setPrototypeOf(this, MsgError.prototype)
  }
 
  sayHello() {
    return "hello " + this.message
  }
}
```
- Object.setPrototypeOf를 지원하지 않는 런타임일 경우 __proto__를 사용할 수 있음

<br>

## 3. 멤버 가시성
- 특정 메소드 또는 속성을 클래스 외부의 코드에 표시할 것인지의 여부를 제어할 수 있음

<br>

### 3-1. public
- 클래스 멤버의 기본 가시성, 어디에서나 접근 가능
```javascript
class Greeter {
  public greet() {
    console.log("hi!")
  }
}
const g = new Greeter()
g.greet()
```
- public은 기본값이기에 따로 적지 않아도 되나, 스타일과 가독성을 위해 적어도 됨

<br>

## 3-2. protected
- 멤버가 선언된 하위 클래스에만 표시됨

```javascript
class Greeter {
  public greet() {
    console.log("Hello, " + this.getName())
  }
  protected getName() {
    return "hi"
  }
}
 
class SpecialGreeter extends Greeter {
  public howdy() {
    console.log("Howdy, " + this.getName())
  }
}

const g = new SpecialGreeter()
g.greet()
// Error
g.getName()
```
- ```g.greet()```는 public이기에 외부에서 접근이 가능함
- ```g.getName()```은 protected이기에 외부에서 접근이 불가능

<br>

### 3-3. protected 멤버 노출
- 파생 클래스는 기본 클래스 계약을 따라야하지만, 더 많은 기능을 가진 하위 타입의 기본 클래스를 노출할 수 있음
- protected 구성원을 공개하는 것도 포함됨

```javascript
class Base {
  protected m = 10
}
class Derived extends Base {
  m = 15
}
const d = new Derived()
console.log(d.m)
```
- Derived에서 m는 public이기에 접근 가능(만약 이를 원치 않는다면 protected를 표기해야 함)

<br>

### 3-4. 교차 계층 protected 접근
- 기본 클래스 참조를 통해 보호된 멤버에 접근하는 것이 합법적인지에 대해 OOP 언어마다 의견이 다름

```javascript
class Base {
  protected x: number = 1
}

class Derived1 extends Base {
  protected x: number = 5
}

class Derived2 extends Base {
  f1(other: Derived2) {
    other.x = 10
  }
  f2(other: Base) {
    // Error
    other.x = 10
  }
}
```
- Base는 Derived2의 하위클래스가 아니기에 오류가 발생함
- Java는 위 코드가 합법적 여김, C#과 C++ 이 코드에 불법이라 여김
- Derived2의 x에 접근하는 것은 Derived2의 하위 클래스에서만 합법적이어야 함
- Derived1 참조를 통해 x에 접근하는 것이 불법이라면
    - 기본 클래스 참조를 통해 x에 접근하는 것은 상황을 개선하지 않음

<br>

### 3-5. private
- protected와 비슷하지만, 파생 클래스에서의 접근도 허용하지 않음

```javascript
class Base {
  private x = 0
}
const b = new Base()
console.log(b.x)
```
- 클래스 외부에서 접근하려고 하면 오류가 발생

```javascript
class Derived extends Base {
  showX() {
    console.log(this.x)
  }
}
```
- 파생클래스여도 x는 private이기에 접근이 불가능함

```javascript
class Base {
  private x = 0
}
class Derived extends Base {
  x = 1
}

```
- 파생 클래스에서 접근은 물론, 할당도 불가능 함

<br>

### 3-6. 교차 인스턴스 private 접근
- TypeScript는 인스턴스간 private 접근을 허용함
```javascript
class A {
  private x = 10
 
  public sameAs(other: A) {
    return other.x === this.x
  }
}
```

<br>

### 3-7. 주의사항
- private 및 protected는 타입 검사 중에만 적용됨
- in과 같은 단순 속성 룩업과 같은 JavaScript 런타임 구조는 private, protected 멤버에 액세스할 수 있음

```javascript
class MySafe {
  private secretKey = 12345
}
// JS
const s = new MySafe()
console.log(s.secretKey)
```
- 위 코드는 JavaScript에서 12345를 출력함

<br>

```javascript
class MySafe {
  private secretKey = 12345
}
 
const s = new MySafe()
 
// 허용하지 않음
console.log(s.secretKey)
 
// OK
console.log(s["secretKey"])
```
- private는 타입 검사중 대괄호 표기법을 사용해 접근할 수 있음
- JavaScript의 private 필드는 컴파일 후에도 private하게 유지됨, 괄호 표기법 접근과 같은 방식을 제공하지 않음

<br>

## 4. 정적 멤버
- 정적 멤버는 클래스의 특정 인스턴스와 연결되어 있지 않음
- 클래스 생성자 개체를 통해 접근 가능함

```javascript
class MyClass {
  static x = 0
  static printX() {
    console.log(MyClass.x)
  }
}
console.log(MyClass.x)
MyClass.printX()
```
- 위 예제는 MyClass 개체를 생성(let, const, var)하지 않고, 클래스의 속성과 메소드에 접근함
- 정적 멤버는 public, protected, private 가시성 수정자를 사용할 수 있음
- 클래스의 멤버와 마찬가지로 정적 멤버도 상속됨

<br>

### 4-1. 특수 정적 이름
- Function 프로토타입의 속성을 덮어쓰는것은 안전하지 않음
- name, lenght, call과 같은 속성은 정적 멤버로 정의할 수 없음

```javascript
class S {
  static name = "S!"
}
```
- 정적 멤버의 이름으로 name 을 사용할 수 없기에 오류가 발생함

<br>

### 4-2. 정적 클래스가 없는 이유
- TypeScript에는 C#과 같은 정적 클래스 구조체가 없음
- 해당 구조는 언어가 모든 데이터와 함수를 클래스 내에 포함하도록 강제하기 때문에 존재함
    - TypeScript는 이러한 제한이 없기에 필요하지 않음

<br>

### 4-3. 정적 클래스 블록
- 정적 블록을 사용하면 포함된 클래스 내의 개인 필드에 접근할 수 있는 자체 범위를 사용하여 일련의 서술 작성 가능

```javascript
class Foo {
    static #count = 0
 
    get count() {
        return Foo.#count
    }
 
    static {
        try {
            const lastInstances = loadLastInstances()
            Foo.#count += lastInstances.length
        }
        catch {}
    }
}

```
- 위 예시는 이전에 생성된 인스턴스의 lenght를 가져와 현재 인스턴스의 count에 합산함

<br>

## 5. 제네릭 클래스
- 클래스는 인터페이스와 마찬가지로 제네릭일 수 있음
- 제네릭 클래스가 new로 인스턴스화될 때 해당 타입 매개변수는 함수 호출과 같은 방식으로 유추됨

```javascript
class Box<Type> {
  contents: Type
  constructor(value: Type) {
    this.contents = value
  }
}
 
const b = new Box("hello!")
```
- 위 예시에서 Box의 타입은 ```Box<string>```임
- 클래스는 인터페이스와 동일한 방식으로 일반 제약조건 및 기본값을 사용할 수 있음

<br>

### 5-1. 정적 멤버에 매개변수 입력
```javascript
class Box<Type> {
  static defaultValue: Type
}
```
- 위 예시는 합법적이지 않음
    - 타입은 항상 지워짐
    - 런타임에는 Box.defaultValue 속성이 하나만 존재함
    - ```Box<string>.defaultValue```를 설정하면, ```Box<number>.defaultValue```도 변경됨
    - 일반 클래스의 정적 멤버는 타입 매개변수를 참조할 수 없음

<br>

## 6. 클래스의 런타임에 this
- TypeScript는 JavaScript의 런타임 동작을 변경하지 않으며, JavaScript는 특이한 런타임 동작을 가짐
- JavaScript가 this를 다루는 것은 이례적임

```javascript
class MyClass {
  name = "MyClass"
  getName() {
    return this.name
  }
}

const c = new MyClass()
const obj = {
  name: "obj",
  getName: c.getName,
}

console.log(obj.getName())
```
- 위 예제는 "obj"를 출력함
- 기본적으로 함수 내부의 this는 함수의 호출 방식에 따라 달라짐
- 예제에서 함수는 obj 참조를 통해 호출되었기에 this는 instance가 아닌 obj임

<br>

### 7-1. 화살표 함수
- 컨텍스트가 손실되는 방식으로 호출되는 함수가 있는 경우
- 메소드 정의 대신 화살표 함수 속성을 사용하는 것이 좋음

```javascript
class MyClass {
  name = "MyClass"
  getName = () => {
    return this.name
  }
}
const c = new MyClass()
const g = c.getName
console.log(g())
```
- 위 예제는 "MyClass"를 출력함
- 화살표 함수에는 몇가지 트레이드 오프가 있음
    1. this 값은 TypeScript로 확인되지 않은 경우에도 런타임에 정확함을 보장함
    1. 각 클래스의 인스턴스는 이러한 방식으로 정의된 각 함수의 자체 복사본을 가지기에 더 많은 메모리를 사용
    1. 프로토타입 체인에 기본 클래스 메소드를 가져올 항목이 없으므로 파생클래스에서 super.getName을 사용할 수 없음

<br>

### 7-2. this 매개변수
- 메소드 또는 함수 정의에서 this라는 이름의 초기 매개변수는 TypeScript에서 특별한 의미를 가짐
- this 매개변수는 컴파일 중 지워짐

```javascript
function fn(this: SomeType, x: number) {
}

// JS 컴파일
function fn(x) {
}
```
- TypeScript는 this 매개변수를 사용하여 함수를 호출하는 것이 올바른 컨텍스트에서 수행되는지 확인함
- 화살표 함수를 사용하는 대신 메소드 정의에 이 매개변수를 추가하여 메소드가 올바르게 호출되었는지 정적으로 적용할 수 있음

<br>

```javascript
class MyClass {
  name = "MyClass"
  getName(this: MyClass) {
    return this.name
  }
}
const c = new MyClass()
c.getName()


const g = c.getName
// Error
console.log(g())
```
- 이 방법은 화살표 함수 접근 방식의 반대 트레이드 오프를 만듬
    1. JavaScript 호출자가 아직 모르고 클래스 메소드를 잘못 사용할 수 있음
    1. 클래스 인스턴스당 하나의 함수가 아닌 클래스 정의당 하나의 함수만 할당됨
    1. 기본 메소드 정의는 super를 통해 호출할 수 있음

<br>

## 8. this 타입
- 클래스에서 this라는 특수 타입은 현재 클래스의 타입을 동적으로 참조함

```javascript
class Box {
  contents: string = ""
  set(value: string) {
    this.contents = value
    return this
  }
}
```
- 위 예제에서 TypeScript는 반환되는 set의 타입을 Box가 아닌 this로 추론함

<br>

```javascript
class ClearableBox extends Box {
  clear() {
    this.contents = ""
  }
}
 
const a = new ClearableBox()
const b = a.set("hello")
```
- 위 예시는 Box의 하위클래스이다.
- b의 타입은 ClearableBox가 됨

<br>

```javascript
class Box {
  content: string = ""
  sameAs(other: this) {
    return other.content === this.content
  }
}
```
- 위 예시처럼 매개 변수 타입 주석에 this를 사용할 수 있음

<br>

```javascript
class Box {
  content: string = ""
  sameAs(other: this) {
    return other.content === this.content
  }
}
 
class DerivedBox extends Box {
  otherContent: string = "?"
}
 
const base = new Box()
const derived = new DerivedBox()
// Error
derived.sameAs(base)
```
- 위 방식은 other : Box라고 작성하는 것과 다르게 동작함
- 파생클래스가 있는 경우 sameAs 메소드는 동일한 파생클래스의 다른 인스턴스만 허용함
- derived.sameAs의 매개 변수로는 DerivedBox만 허용함 따라서 오류가 발생

<br>

### 8-1. this 기반 타입 가드
- 클래스 및 인터페이스 메소드에 대한 반환 위치에 this is Type을 사용할 수 있음
- 좁혀진 타입과 혼합될 경우, 대상 객체의 타입은 지정된 타입으로 좁혀짐

```javascript
class FileSystemObject {
  isFile(): this is FileRep {
    return this instanceof FileRep
  }
  isDirectory(): this is Directory {
    return this instanceof Directory
  }
  isNetworked(): this is Networked & this {
    return this.networked
  }
  constructor(public path: string, private networked: boolean) {}
}
 
class FileRep extends FileSystemObject {
  constructor(path: string, public content: string) {
    super(path, false)
  }
}
 
class Directory extends FileSystemObject {
  children: FileSystemObject[]
}
 
interface Networked {
  host: string
}
 
const fso: FileSystemObject = new FileRep("foo/bar.txt", "foo")
 
if (fso.isFile()) {
  // fso의 타입은 FileRep
  fso.content
} else if (fso.isDirectory()) {
  // fso의 타입은 Directory
  fso.children
} else if (fso.isNetworked()) {
  // fso의 타입은 Networked & FileSystemObject
  fso.host
}
```
- this 기반의 타입 가드의 사용 사례는 특정 필드의 느린 유효성 검사를 허용하는 것임

<br>

```javascript
class Box<T> {
  value?: T
 
  hasValue(): this is { value: T } {
    return this.value !== undefined
  }
}
 
const box = new Box()
box.value = "Gameboy"

box.value
 
if (box.hasValue()) {
  box.value
}
```
- 위 예시에서 hasValue가 true일 경우 Box 안에 있는 값에서 undefined를 제거함

<br>

## 9. 클래스 표현
- 클래스 표현은 클래스 선언과 유사함
- 차이점은 클래스 표현은 이름이 필요하지 않음

```javascript
const someClass = class<Type> {
  content: Type
  constructor(value: Type) {
    this.content = value
  }
}
 
const m = new someClass("Hello, world")
```

<br>

## 10. 추상 클래스와 멤버
- TypeScript의 클래스, 메소드, 필드는 추상적일 수 있음
- 추상 메소드, 필드는 추상 클래스 내부에 존재해야 함
- 추상 클래스의 역할은 추상 멤버를 구현하는 하위 클래스의 기본 클래스의 역할을 수행하는 것임

```javascript
abstract class Base {
  abstract getName(): string
 
  printName() {
    console.log("Hello, " + this.getName())
  }
}
// Error
const b = new Base()
```
- 추상 클래스는 인스턴스화 할 수 없다
    - 그렇기에 위 예제는 오류가 발생한다.
- 추상 클래스를 인스턴스화 하는 것이 아니라, 아래 예제처럼 파생 클래스를 만들고 추상 멤버를 구현해야 함

```javascript
class Derived extends Base {
  getName() {
    return "world"
  }
}
 
const d = new Derived()
d.printName()
```
기본 클래스의 추상 멤버를 구현하지 않으면 오류가 발생하기에 주의해야 함

<br>

### 10-1. 추상 구성 서명

```javascript
// ctor는 추상클래스임
function greet(ctor: typeof Base) {
  const instance = new ctor()
  instance.printName()
}
```
- 일부 추상 클래스에서 파생된 클래스의 인스턴스를 생성하는 일부 클래스 생성자 함수를 수락하려 함
- TypeScript가 추상 클래스를 인스턴스화 하려고할 경우 알려줌

<br>

```javascript
// Base는 추상클래스, Derived는 Base를 구현한 클래스
function greet(ctor: new () => Base) {
  const instance = new ctor()
  instance.printName()
}
// OK
greet(Derived)
// Error
greet(Base)
```
- 위 예제에서 Derived는 구현되었기에 호출 가능함
- 그러나 Base는 호출할 수 없음(추상 클래스)

<br>

## 11. 클래스 사이의 관계
- 대부분의 경우 TypeScript의 클래스는 다른 타입과 동일하게 구조적으로 비교됨

```javascript
class Point1 {
  x = 0
  y = 0
}
 
class Point2 {
  x = 0
  y = 0
}

const p: Point1 = new Point2()
```
- 위 예제의 두 클래스는 동일하기에, 서로 대신하여 사용할 수 있음
- 마찬가지로, 명시적 상속이 없는 경우에도 클래스간의 하위 타입 관계가 존재함
```javascript
class Person {
  name: string
  age: number
}
 
class Employee {
  name: string
  age: number
  salary: number
}
 
const p: Person = new Employee()
```
- Employee 클래스에 Person의 모든 필드가 존재하기에 문제가 없음

<br>

```javascript
class Empty {}
 
function fn(x: Empty) {

}

fn(window)
fn({})
```
- 위 예시처럼 빈 클래스를 작성하면 모든 클래스를 사용할 수 있음