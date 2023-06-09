자바스크립트는 **프로토타입 기반의 객체지향 프로그래밍** 언어다

ES6에서 **클래스**가 도입되었는데, 이는 기존 프로토타입 기반 패턴의 **문법적 설탕**이라고 볼 수 있다.

좀 더 차이를 두면 생성자함수에 비해 엄격하며 생성자함수에서 제공하지 않는 기능을 제공하기에 클래스는 새로운 **객체 생성 매커니즘**으로 보는 것이 더욱 합당하다


> 💡 __문법적 설탕__
Syntax Sugar는 한국어로 문법 설탕이라고 번역됩니다. JS뿐만 아니라 프로그래밍 언어 전반적으로 적용되는 개념이며, 달달한 이름에 걸맞게 읽는 사람 또는 작성하는 사람이 편하게 디자인 된 문법이라는 뜻을 갖고 있습니다.


## 객체지향 프로그래밍

객체지향 프로그래밍은 객체의 __상태(프로퍼티)__를 나타내는 데이터와 데이터를 조적할 수 있는 **동작(메서드)**을 하나의 논리적인 단위로 묶는 프로그래밍 패러다임을 말한다

각 객체는 독립적이면서도 다른 객체와 관계성을 가지며, 다른 객체로부터 상태나 동작을 상속받아 사용할 수 있다

다양한 속성중에 프로그램에 필요한 속성만 간추려내어 표현하는 것을 **추상화** 라고 한다


```jsx
const person = {
  // 이름과 주소를 속성으로 갖는 객체
	name : 'Lee',
  address : 'Seoul'
}

console.log(person);
```

### 상속과 프로토타입

자바스크립트는 객체지향 프로그래밍의 핵심개념으로 프로토타입 기반으로 상속을 구현하여 불필요한 중복을 제거한다

```jsx
function Circle(radius) {
	this.radius = radius;
  // 공통적인 기능을 하는 메서드는 하나만 생성하여 
  // 모든 인스턴스가 공유해서 사용하는 것이 바람직하다
  // 인스턴스가 생성될때마다 동일 메서드 중복 소유 -> 불필요한 메모리 낭비
  this.getArea = function() {
    return Math.PI * this.radius ** 2;
	}
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea); // false
```

프로토타입 상속을 통해 해결

```jsx
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getArea = function() {
	return Math.PI * this.radius ** 2;
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는디
console.log(circle1.getArea === circle2.getArea); // true
```

### 프로토타입 객체

객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위해 사용된다

모든 객체는 __[[Prototype]]__ 이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조다 

- 리터럴 객체
    - Object.prototype
- 생성자 함수
    - 생성자 함수의 prototype에 바인딩 되어있는 객체

모든 객체는 하나의 프로토타입을 갖으며, 생성자 함수와 연결되어있다


>⚠️ **[[Prototype]] 내부슬롯 값이 null인 경우 프로토타입이 없음**

![image](https://github.com/rockbell89/TIL/assets/52031484/6c577d7c-3de2-4546-9ed9-2a0e8ebbe51e)


[[Prototype]] 내부 슬롯에는 `__proto__` 접근자 프로퍼티를 통해 간접적으로 접근할 수 있다

### __proto__ 접근자 프로퍼티

__proto__ 접근자 프로퍼티는 객체가 직접 소유하는 프로퍼티가 아니라 Object.prototype의 프로퍼티이며, 모든 객체가 상속을 통해 Object.prototype.__proto__ 접근자 프로퍼티를 사용한다

```jsx
const person = { name : 'Ria' } 
console.log(person.__proto__); 

console.log(person.hasOwnProperty('__proto__')); // false
console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
// { get:  f , set : f, enumerable: false, configurable: true }
 
console.log({}.__proto__ === Object.prototype); // true
```

접근자 프로퍼티는 `[[Get]]` , `[[Set]]` 프로퍼티 어트리뷰트로 구성되어있으며, 내부슬롯 값인 프로토타입을 취득하거다 할당할 수 있다

```jsx
const obj = {};
const parent = { x: 1 };
// getter    
obj.__proto__;
// setter
obj.__proto__ = parent;
console.log(ojb.x); // 1;
```

### getPrototypeOf()

__proto__ 접근자 프로퍼티를 모든 객체가 사용할 수 있는 것은 아니여서 사용을 권장하지 않는다

```jsx
// obj는 프로토타입 체인의 종점이기 때문에
// Object.__proto__를 상속받을 수 없다
const obj = Object.create(null);
```

따라서, 프로토타입의 참조를 취득하고 싶은 경우에는 **Object.getPrototypeOf()** 메서드를 사용하고

교체할때는 **Object.setPrototypeOf()** 메서드를 사용할 것을 권장한다

```jsx
const obj = {};
const parent = { x: 1 };
// getter    
Object.getPrototypeOf(obj);
// setter
Object.setPrototypeOf(obj, parent);
console.log(obj.x); // 1;
```

### non-constructor

생성자함수로 호출할 수 없는 함수는 prototype을 가질수 없다

- 화살표 함수
- 메서드

```jsx
const Person = name => {
	this.name = name;
}

console.log(Person.hasOwnProperty('prototype')); // false
console.log(Person.prototype); // undefined;
const obj = {
	foo(){}
}

console.log(obj.foo.hasOwnProperty('prototype')); // false
console.log(obj.foo.prototype); // undefined;
```

### 리터럴 표기법

| 리터럴 표기법 | 생성자 함수 | 프로토타입 |
| --- | --- | --- |
| 객체 | Object | Object.prototype |
| 함수 | Funtion | Object.prototype |
| 배열 | Array | Array.prototype |
| 정규 표현식 | RegExp | RegExp.prototype |

## 프로토타입 체인

```jsx
function Person(name) {
	this.name = name;
}

Person.prototype.sayHello = function() {
	console.log(this.name);
}

const me = new Person('Ria');
// hasOwnProperty() 메서드는
// Object.prototype 의 메서드를 상속받음
console.log(me.hasOwnProperty('name')); // true
```

자바스크립트는 객체 프로퍼티에 접근하려 할때 해당 프로퍼티가 존재하지 않다면 [[Prototype]] 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색하며 이를 **프로토타입 체인**이라 한다

1. me 객체에서 hasOwnProperty 탐색
2. Person.prototype 에서 hasOwnProperty  탐색 
3. 마지막으로 체인을따라 Object.prototype 으로 이동하여 hasOwnProperty 메서드 탐색
4. hasOwnProperty 메서드 호출시, 자바스크립트 엔진은 해당 메서드의 this에 me 객체 바인딩

```jsx
Object.prototype.hasOwnProperty.call(me, 'name');
```

프로토타입 체인의 최상위에 위치하는 객체는 언제나 Object.prototype (end of prototype chain)이며, Object.prototype의 프로토타입은 **null** 이다

## 오버라이딩과 프로퍼티 섀도잉

```jsx
const Person = (function() {
	function Person(name) {
		this.name = name;
	}![Uploading image.png…]()


  Person.prototype.sayHello = function() {
		console.log(`안녕하세요! ${this.name}입니다.`)
	}

	return Person;
}());

const me - new Person('Ria');

me.sayHello = functon() {
	console.log(`안녕! ${this.name}라고해.`)
}

me.sayHello();// 안녕! Ria라고해.

delete me.sayHello;

me.syaHello(); // 안녕하세요! Ria입니다.

// 하위 객체를 통해 프로토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능
// get엑세스는 허용되나 set액세스는 허용되지 않음
delete me.sayHello;// 안녕하세요! Ria입니다.

// 프로토타입의 직접 접근해서 삭제
delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```



> 💡 **오버라이딩** 
상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식


> 💡 **오버로딩** 
함수의 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현하고 매개변수에 의해 메서드를 구별하여 호출하는 방식으로 javascript에서는 지원하지 않지만 **arguments**객체를 통해 구현할 수 있음


```jsx
const Person = (function() {
	function Person(name) {
		this.name = name;
	}
  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
   sayHello() {
   console.log(`안녕하세요! ${this.name}입니다.`)
  }
 }

	return Person;
}());

const me = new Person('Ria');

// 이처럼 프로토타입을 교체하게 되면 constructor 프로퍼티와
// 생성자 함수간의 연결이 파괴된다
console.log(me.constructor === Person); // false
console.log(me.constructor === Object); // true
```

이는 생성자 함수를 constructor에 연결해줌으로써 해결할 수 있다

```jsx
// constructor 프로퍼티와 생성자 함수 간의 연결을 설정  
Person.prototype = {
   constructor : Person,
   sayHello() {
	   console.log(`안녕하세요! ${this.name}입니다.`)
		}
 }

console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false
```

## 프로토타입의 교체

### 생성자 함수에 의한 프로토타입 교체

```jsx
const Person = (function() {
	function Person(name) {
		this.name = name;
	}
	
	Person.prototype = {
		sayHello() {
			console.log(this.name);
		}
	}
	
	return Person;
});

const me = new Person('RIA');

// Person 생성자 함수의 prototype이 교체되어 contstructor 와의 연결이 파괴되고
// 프로토타입체인을 통해 Object 생성자 함수를 가리킨다
console.log(me.constructor); // Object
```

```jsx
// 이를 해결하기 위해서는 교체된 prototype 에 Person 객체를 연결 해주어야함
{...}
Person.prototype = {
    constructor : Person,
		sayHello() {
			console.log(this.name);
		}
	}
{...}
```

### 인스턴스에 의한 프로토타입 교체

```jsx
function Person(name) {
		this.name = name;
}

const parent = {
		sayHello() {
			console.log(this.name);
	}
}

const me = new Person('RIA');
Object.setPrototypeOf(me, parent);

console.log(me.constructor); // Object
```

```jsx
// 이를 해결하기 위해서는 교체된 prototype 에 Person 객체를 연결 해주어야함
{...}
const parent = {
    constructor : Person,
		sayHello() {
			console.log(this.name);
		}
	}
Person.prototype = parent;
Object.setPrototypeOf(me, parent);
{...}
```

## instanceof 연산자

생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인상에 존재하는지 확인한다

- 존재하면 `true` 를 그렇지 않으면 `false` 를 반환한다

```jsx
객체 instanceof 생성자 함수
```

`contstructor` 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 `instanceof` 는 아무런 영향을 받지 않는다

```jsx
function Person(name) {
	this.name = name;
}
const me = new Person('RIA');
const parent = {}

// 프로토타입 빈 객체로 교체
Object.[setPrototypeOf](https://www.notion.so/19-05e5cac83d674842a41b9af8c0b73e32?pvs=21)(me, parent);

console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

console.log(me instanceof Person);// false
console.log(me instanceof Object);// true
```

단, Person.prototype 이 me 객체의 프로토타입 체인상에 존재하지 않기때문에 `false`로 평가된다

Person 생성자 함수의 prototype으로 parent를 바인딩하면 `true`로 평가된다

```jsx
{...}

Person.prototype = parent;
console.log(me instanceof Person);// false
```

```jsx
function isInstanceOf(instance, constructor) {
	const prototype = Object.getPrototypeOf(instance);
  console.log(prototype);
  console.log(constructor);
  if(prototype === null) return false;
  return prototype === constructor.prototype || isInstanceOf(prototype, constructor)
}

console.log(isInstanceOf(me, Person)); // true
console.log(isInstanceOf(me, Object)); // true
console.log(isInstanceOf(me, Array));; // false
```

## 직접 상속

### Object.create

명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다

- 첫번째 인수에 프로토타입으로 지정할 객체 전달
- 생설할 객체의 프로퍼티 키와 디스크립터 객체로 이루어진 객체 전달

```jsx
Object.create(protptype[, propertiesObject])
```

- Object.create 메서드로 프로토타입을 `null` 로 지정했을 때, Object.prototype 메서드 사용 불가
→ Object.prototype의 빌트인 메서드를 사용하기 위해서는 간접 호출을 통해 사용

```jsx
const obj = Object.create(null)
obj.a = 10;
console.log(Object.getPorototypeOf(obj) === null); // true

obj.hasOwnProperty('a'); // TypeError: obj.hasOwnProperty is not a function
Object.prototype.hasOwnProperty.call(obj, 'a'); // 10
```

### __proto__

__proto__ 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다

```jsx
const myProto = {x : 10};

const obj = {
	y :20,
  __proto__: myProto
}

console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 정적 프로퍼티/메서드

정적 프로퍼티/메서드는 인스턴스를 생성하지 않고 참조 및 호출 할수 있는 프로퍼티/메서드를 말한다 → **생성한 인스턴스로 참조 및 호출이 불가 → 인스턴스 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아님**

```jsx
function Person(name) {
	this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function() {...}

// 정적 프로퍼티
Person.staticProp = 'static prop';

// 정적 메서드
Person.staticMethod = function () {
	console.log('static method')
}

Person.staticMethod(); // 'static method'

const me = new Person('RIA');

me.staticMethod(); // TypeError: me.staticMethod is not a function

```

- Object.create() vs Object.prototype.hasOwnProperty

```jsx
// Object.create 는 정적 메서드
const obj = Object.create({ name : 'RIA'});

obj.create({age : 20}); // TypeError: obj.create is not a function
obj.hasOwnProperty('name'); // false
```

## 프로퍼티 조회

### in연산자

객체 내에 특정 프로퍼티가 존재하는지 여부를 확인 → 상속받은 프로토타입의 프로퍼티까지 확인

```jsx
key in obj
```

```jsx
**const obj = {
	a : 1,
  b : 2,
  __proto__ : { c : 3}
}

console.log('a' in obj); // true
console.log('c' in obj); // true
console.log('toString' in obj); // true**
```

### Reflect.has

in연산자와 동일하게 동작한다

```jsx
{...}
console.log(Reflect.has(obj, 'a'); // true
console.log(Reflect.has(obj, 'c'); // true
console.log(Reflect.has(obj, 'toString'); // true
```

## Object.prototype.hasOwnProperty

객체 내에 특정 프로퍼티가 존재하는지 여부를 확인하는 것은 동일하나, 객체 고유의 프로퍼티만 `true`를 반환하고, 상속받은 프로토타입의 프로퍼티 키의 경우 `false` 를 반환

```jsx
{...}
console.log(obj.hasOwnProperty('a')); // true
console.log(obj.hasOwnProperty('c')); // false
console.log(obj.hasOwnProperty('toString')); // false
```

## 프로퍼티 열거

### for…in

객체의 프로퍼티 개수 만큼 순회하며 선언한 변수에 프로퍼티 키를 할당 

- 상속받은 프로토타입의 프로퍼티까지 순회하여 열거
(단, 해당 프로퍼티의 프로퍼티 어트리뷰트 중 **[[Enumerable]]** 값이 `true` 인 프로퍼티만 !!)
    - **Object.prototype.hasOwnProperty** 체크를 통한 추가 처리 필요
- 열거 시 순서를 보장하지 않음 → 대부분, 모던 브라우저는 순서를 보장

```jsx
for (변수 선언문 in 객체) {...}
```

```jsx
// 객체의 Object.prototype.toString 과 같은 경우  
// [[Enumerable]] 값이 false 이기때문에 열거되지 않음
const obj = {
	a : 1,
  b : 2,
  __proto__ : { c : 3}
}
for (const key in obj) {
	console.log(`${key} : ${obj[key]}`);
}

// a : 1
// b : 2
```

- 키가 symbol 인 프로퍼티는 열거되지 않음

```jsx
const symbol = Symbol();
const obj = {
	a : 1,
  b : 2,
 [symbol] : 10
}

for (const key in obj) {
	console.log(`${key} : ${obj[key]}`);
}

// a : 1
// b : 2
```

- 배열에 프로퍼티가 추가되면 `for..in`문 사용시 해당 프로퍼티도 포함 → 배열도 객체이기때문
→ 배열 순회시에는 `for…of` 혹은 Array.prototype.ForEach 문을 사용하는 것이 좋음

```jsx
const arr = [1,2];
arr.x = 3;

for (const index in arr) {
	console.log(index);
}

// 1
// 2
// 3
```

### Object.keys

객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다

```jsx
const obj = {
	a : 1,
  b : 2,
  __proto__ : { c : 3}
}

console.log(Object.keys(obj)); // ['a', 'b']
```

### Object.values

객체 자신의 열거 가능한 값을 배열로 반환한다

```jsx
const obj = {
	a : 1,
  b : 2,
  __proto__ : { c : 3}
}

console.log(Object.values(obj)); // [1, 2]
```

### Object.entries

객체 자신의 열거 가능한 프로퍼티 키와 값 쌍을  배열로 반환한다
