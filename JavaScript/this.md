# this 키워드
`this`는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수이다  
객체는 상태를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합자료이며,  
자신이 속한 객체의 프로퍼티를 참조하려면 해당 객체를 가리키는 식별자를 참조할 수 있어야 한다

##  객체 리터럴
```js
const circle = {
 radius: 5,
 getDiameter() {
   return 2 * this.radius; // 객체 내부에서의 this 는 메서드를 호출한 객체를 가리킴
 }
}

// 메서드가 호출되는 시점에서 이미 객체 리터럴이 평가되어 객체가 생성된 상태
console.log(circle.getDiameter()); // 10
```

## 생성자 함수
```js
// 생성자 함수가 정의만된 시점에서는 생설할 인스턴스를 가리키는 식별자를 알 수 없음
function Circle(radius) {
  this.radius = raduis;
}

Circle.prototype.getDiameter = function() {
  return 2 * this.radius;
}

// 생성자 함수에 의한 객체 생성 방식은 new 연산자와 함께 생성자 함수를 호출하는 단계가 추가로 필요
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

## JavaScript 에서의 this 바인딩
자바스크립트의 `this`는 함수가 호출되는 방식에 따라 this바인딩이 **동적으로 결정**됩니다 

### 전역
```js
console.log(this); // window

// strict mode 시
console.log(this); // undefined
```

### 일반 함수 내부

```js
function sqaure(number) {
  console.log(this); // window -> strict mode 시 undefined
  return number * number;
}
sqaure(2);
```

### 객체 리터럴
```js
const person= {
	name :'RIA',
  getName() {
    // 메서드를 호출한 객체
    console.log(this); // { name : 'RIA', getName : f }
    return this.name;
  }
}
console.log(person.getName()); // 'RIA'
```

### 생성자 함수
```js
function Person(name) {
	this.name = name;
  // 생성될 인스턴스 객체
  console.log(this); // Person { name : 'RIA' }
}

const me = new Person('RIA'); 
```


### 중첩함수, 콜백함수의 this
```js
var value = 1;

const obj = {
	value :  100,
  foo() {
    console.log(this); // { value: 100, foo : f }
    console.log(this.value); // 100
    // 메서드 내 중첩함수도 일반 함수로 호출되면 this에는 전역 객체가 바인딩됩니다
    function bar() {
      console.log(this); // window
      console.log(this.value); // 1
    }
    bar();
  }
}

obj.foo();
```
#### 변수 
변수 할당을 통해서 중첩함수와 콜백함수의 this바인딩을 일치 시킬 수 있습니다
```js
var value = 1;

const obj = {
	value :  100,
  foo() {
    console.log(this); // { value: 100, foo : f }
    // 중첩함수 혹은 콜백 함수의 this바인딩을 일치시키기위해 
    // 다음과 같이 변수에 this를 할당하여 참조한다
    const that = this;
    setTimeout(function() {
      console.log(that); // { value: 100, foo : f }
      console.log(that.value); // 100
    }, 100)
  }
}

obj.foo();
```
#### Function.prototype 메서드 - apply() / call() / bind()
```js
var value = 1;

const obj = {
	value :  100,
  foo() {
    setTimeout(function() {
      console.log(this.value); // 100
    }.bind(this), 100) // bind를 사용하여 인수로 전달하면 함수 내부의 this는 인수로 전달된 객체가 된다 
  }
}

obj.foo();
```

### 화살표 함수
화살표 함수로 호출 된 경우 의의 규칙을 무시하고, 생성된 시점에서 상위 스코프의 this 값을 참조합니다
```js
var value = 1;

const obj = {
	value :  100,
  foo() {
    setTimeout(() => {
      // 
      console.log(this.value); // 100
    }, 100)
  }
}

obj.foo();
```

### 메서드 호출 this
객체 내 메서드 내부의 `this`는 메서드가 정의된 객체가 아닌 메서드가 **호출한 객체**에 바인딩된다
```js
const person = {
	name :'RIA',
  getName() {
    console.log(this); // ㅡ1
    return this.name; // ㅡ2
  }
}
console.log(person.getName()); // 1 - { name : 'RIA', getName : f } , 2 - 'RIA'

const anotherPerson = {
	name : 'BONG',
}
anotherPerson.getName = person.getName;
console.log(anotherPerson.getName());  // 1 - { name : 'BONG', getName : f } , 2 - 'BONG'

const getName = person.getName;
// 여기서 getName() 가 호출된 객체는 전역객체를 참조하고 있다
console.log(getName()); // 1- window, 2 - ''
```

| 함수 호출 방식 | this 바인딩 |
| --- | --- |
| 일반 함수 | 전역 객체 |
| 메서드| 메서드를 호출한 객체 |
| 생성자 함수 | 생성할 인스턴스 |
| Function.prototype 메서드 | apply,call,bind 메서드에 첫번째 인수로 전달된 객체 |
