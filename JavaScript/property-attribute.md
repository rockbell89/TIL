## 내부 슬롯과 내부 메서드

자바스크립트 엔진 구현의 알고리즘을 설명하기위한 **의사 프로퍼티**와 **의사 메서드** 이다

외부로 공개된 객채 프로퍼티가 아니기때문에 개발자가 직접 접근하거나 조작 할 수 없다
예외적으로 객체 내부 슬롯 `[[prototype]]` 을 접근할 수 있는 `.__prpto__` 를 통해 간접적으로 접근 할 수 있다 → **`Object.getPrototypeOf()`** 사용 권장

```jsx
const o = {}
o.[[Prototype]]; // SyntaxError : Unexpected token '['
o.__proto__; ;; Object.prototype
```

## 프로퍼티 어트리뷰트
자바스크립트 엔진은 프로퍼티를 생설할때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의한다
### 데이터 프로퍼티

`[[Value]]` , `[[Writable]]`, `[[Emnumerable]]`, `[[Configurable]]` 등에 직접 접근할수는 없지만,

`**Object.getOwnPropertyDescriptor()**` 를 통해 상태를 조회할 수 있다

```jsx
const person = {
	name : 'Ria',
}
person.age = 20;

// **디스크립터 객체를 반환하며 value는 프로퍼티의 값으로, 나머지는 true로 초기화 됨**
console.log(Object.**getOwnPropertyDescriptor(person, name)**);
/*
  {
	 value : 'Ria',
   writable :  true, // 변경(수정) 가능 여부
   enumerable : true, // 열거(조회) 가능 여부
   configurable : true // 값 재정의 기능 여부 
  }
*/

// Object.**getOwnPropertyDescriptors() 는 객체의 모든 프로퍼티에 대한 디스크립터 객체를 반환**
console.log(Object.**getOwnPropertyDescriptors(person)**)
/*
{
  name : { value : 'Ria', writable : true, enumerable : true, configurable : true },
  age: { value : 20, writable : true, enumerable : true, configurable : true }
}
*/
```

### 접근자 프로퍼티

`[[Get]]` , `[[Set]]` ,`[[Emnumerable]]`, `[[Configurable]]` 의 어트리뷰트를 갖는다

접근자 프로퍼티는 값을 가지지 않으며, 데이터 프로퍼티의 값을 읽거나 저장할때만 관여한다

```jsx
const person = {
	firstName : 'Bora',
  lastName : 'Kim',
  // fullName은 접근자 프로퍼티
  get fullName() {
		retunr `${this.firstName} ${this.lastName}`;
	},
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

person.fullName = 'Jay Park';
console.log(person); // { firstName : 'Jay', lastName : 'Park' }

console.log(Object.getOwnPropertyDescriptor(person, 'fullName'))
// { get: f , set: f, enumerable : true, configurable : true }

```

### 프로퍼티 정의

`Object.defineProperty` 메서드를 통해 프로퍼티의 어트리뷰트를 정의할 수 있다

```jsx
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, 'firstName', {
	 value : 'Ria',
   writable :  true, 
   enumerable : true, 
   configurable : true 
})

// 디스크립터 객체의 프로퍼티를 누락시키면 값은 undefined, 나머지는 false가 할당
Object.defineProperty(person, 'lastName')

console.log(Object.getOwnPropertyDescriptor(person, 'lastName'));
/*
 {
	 value : undefined,
   writable :  false, 
   enumerable : false, 
   configurable : false
}
*/
)

// 접근자 프로퍼티를 정의
Object.defineProperty(person, 'fullName', {
	 get() {...},
   set() {...},
   enumerable : true, 
   configurable : true 
})

//  Object.defineProperties() 메서드를 사용하면 여러개의 프로퍼티를 한번에 정의 가능
Object.defineProperties(person, {
	firstName : {...},
  lastName : {...},
  fullName : {...}
})
```

## 객체 변경 방지

- 객채 확장 금지 - Object.preventExtensions()
- 객체 밀봉 - Object.seal()
- 객체 동결 - Object.freeze()

| 추가 | 삭제 | 재정의 | (어트리뷰트) | 쓰기(수정) | 읽기 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| 객채 확장 금지 | X | O | O | O | O |
| 객체 밀봉 | X | X | X | O | O |
| 객체 동결 | X | X | X | X | O |

### 객체 확장 금지

```jsx
const person = {name : 'Ria'}; 

// 객채 확장 가능 여부 Object.isExtensible()
console.log(Object.isExtensible(person)); // true
Object.preventExtensions(person);
console.log(Object.isExtensible(person)); // false

// 수정 가능
Object.defineProperty(person, 'name', {value : 'Bora'});
console.log(person); // { name : 'Bora'}

// 어트리뷰트 재정의 가능
Object.defineProperty(person, 'name', { enumerable: false });

// 삭제는 가능
delete person.name; 
console.log(person); // {}

// 추가 불가 -> 무시 or 에러(strict mode)
person.age = 20; // 무시
Object.defineProperty(person, 'age', {value : 20}); 
// TypeError : Cannot define property age, object is not extensible
```

### 객체 밀봉

```jsx
const person = {name : 'Ria'};

// 객채 밀봉 여부 Object.isSealed()
console.log(Object.isSealed(person)); // false
Object.seal(person);
console.log(Object.isSealed(person)); // true

// 수정 가능
person.name = 'Bora';
console.log(person) { name : 'Bora'}

// 어트리뷰트 재정의 불가
Object.defineProperty(person, 'name', { configurable: true });
// TypeError: Cannot redefine property: name

// 삭제 불가
delete person.name; // 무시

// 추가 불가 -> 무시 or 에러(strict mode)
person.age = 20; // 무시
Object.defineProperty(person, 'age', {value : 20}); 
// TypeError : Cannot define property age, object is not extensible
```

### 객체 동결

```jsx
const person = {name : 'Ria'};

// 객채 동결 여부 Object.isFrozen()
console.log(Object.isFrozen(person)); // false
Object.freeze(person);
console.log(Object.isFrozen(person)); // true

// 수정 불가
person.name = 'Bora' // 무시

// 어트리뷰트 재정의 불가
Object.defineProperty(person, 'name', { configurable: true });
// TypeError: Cannot redefine property: name

// 삭제 불가
delete person.name; // 무시

// 추가 불가 -> 무시 or 에러(strict mode)
person.age = 20; // 무시
Object.defineProperty(person, 'age', {value : 20}); 
// TypeError : Cannot define property age, object is not extensible
```

### 불변 객체
Object.freeze() 메서드로 객체를 동결하도 중첩 객체까지는 동결 할 수 없으며, 중첩 객체까지 동결하려면 객체를 값으로 갖는 프로퍼티에 대해 재귀적으로 Object.freeze() 메서드를 호출해야한다
