# 19장 프로토타입

자바스크립트는 명령형, 함수형, 프로토타입 기반 객체지향 프로그래밍을 지원하는 멀티 패러다임 프로그래밍 언어이다.

클래스 기반 객체지향 프로그래밍 언어의 특징인 클래스와 상속, 캡슐화를 위한 키워드 public, private, protected 등이 없어서 자바스크립트는 객체지향 언어가 아니라고 오해하는 경우도 있다. 하지만 자바스크립트는 클래스 기반 객체지향 프로그래밍 언어보다 효율적이며 더 강력한 객체지향 프로그래밍 능력을 지니고 있는 프로토타입 기반의 객체지향 프로그래밍 언어다.

자바스크립트를 이루고 있는 거의 **모든 것**이 객체다. 원시 타입의 값을 제외한 나머지 값들(함수, 배열, 정규 표현식 등)은 모두 객체다.

## 19.1 객체지향 프로그래밍

객체지향 프로그래밍은 프로그램을 명령어 또는 함수의 목록으로 보는 전통적인 명령형 프로그래밍의 절차지향적 관점에서 벗어나 여러개의 독립적 단위, 즉 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 말한다.

객체지향 프로그래밍은 실세계의 실체(사물이나 개념)를 인식하는 철학적 사고를 프로그래밍에 접목하려는 시도에서 시작한다. 실체의 특징이나 성질을 나타내는 **속성**, 이 다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 **추상화**라고 한다.

속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 **객체**라고 하며, 객체지향 프로그래밍은 독립적인 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임이다.

객체의 상태를 나타내는 데이터와 상태 데이터를 조작할 수 있는 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조를 객체라 한다. 객체의 상태 데이터를 프로퍼티, 동작을 메서드라 부른다.

## 19.2 상속과 프로토타입

상속은 객체지향 프로그래밍의 핵심 개념으로, 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.

자바스크립트에서는 프로토타입을 기반으로 상속을 구현한다. 기존 코드를 적극적으로 재사용하고, 불필요한 중복을 재거하여 개발 비용을 현저히 줄일 수 있으므로 매우 중요하다.

동일한 생성자 함수에 의해 생성된 모든 인스턴스가 동일한 메서드를 중복 소유하는 것은 메모리를 불필요하게 낭비한다. 또한 인스턴스를 생성할 때마다 메서드를 생성하므로 퍼포먼스에도 악영향을 준다.

중복되는 프로퍼티와 메서드를 상위(부모) 객체 역할을 하는 생성자 함수의 prototype에 추가하여 하위(자식) 객체가 상속받아 사용할 수 있도록 한다.

```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}
// 불필요한 중복을 제거하여 모든 Circle 생성자 함수가
// 해당 메서드를 공유하여 사용할 수 있도록 프로토타입에 추가한다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};
const circle1 = new Circle(1);
const circle2 = new Circle(2);
circle1.getArea === circle2.getArea; // true
circle1.getArea(); // 3.14....
circle2.getArea(); // 12.56...
```

## 19.3 프로토타입 객체

프로토타입 객체(또는 줄여서 프로토타입)란 객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위해 사용된다.

어떤 객체의 상위(부모) 객체의 역할을 하는 프로토타입은 다른 객체에 공유 프로퍼티(메서드 포함)을 제공한다.

모든 객체는 하나의 프로토타입을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 연결되어 있다.

\***\*proto** 접근자 프로퍼티\*\*

모든 객체는 **proto** 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 `[[Prototype]]` 내부 슬롯에 간접적으로 접근 할 수 있다.

Object.prototype의 접근자 프로퍼티인 **proto** 는 getter/setter 함수라고 부르는 접근자 함수를 통해 `[[Prototype]]` 내부 슬롯의 값, 즉 프로토타입을 취득하거나 할당한다.

```javascript
const obj = {};
const parent = { x: 1 };
obj.__proto__; // 프로토타입 할당
obj.__proto__ = parant; // setter 함수가 호출되어 obj 객체 프로토타입을 교체
obj.x; // 1
```

`[[Prototype]]` 프로토타입에 접근하기 위해 접근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서이다. 비정상적인 프로토타입 체인은 프로토타입 체인 종점이 존제하지 않기 때문에 프로퍼티를 검색할 때 무한 루프에 빠진다.
그렇기 때문에 프로토타입 체인은 단방향 링크드 리스트로 구현되어야 한다.

```javascript
const parant = {};
const child = {};

// 서로가 자신의 프로토타입이 되는 비정상 프로토타입 체인
child.__proto__ = parent;
parent.__proto__ = child; // TypeError 무한 루프
```

모든 객체가 가지고 있는 **proto** 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 결국 동일한 프로토타입을 가리킨다.

| 구분                            | 소유        | 값                | 사용 주체   | 사용 목적                                                                    |
| ------------------------------- | ----------- | ----------------- | ----------- | ---------------------------------------------------------------------------- |
| **proto** <br />접근자 프로퍼티 | 모든 객체   | 프로토타입의 참조 | 모든 객체   | 객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용                      |
| prototype<br />프로퍼티         | constructor | 프로토타입의 참조 | 생성자 함수 | 생성자 함수가 자신이 생성할 객체(인스턴스)의 프로토타입을 할당하기 위해 사용 |

모든 프로토타입은 constructor 프로퍼티를 갖는다.
constructor 프로퍼티는 prototype 프로퍼티로 생성자 함수를 가리킨다. 이 연결은 생성자 함수가 생성될 때, 함수 객체가 생성될 때 이뤄진다.

```javascript
function Person(name) {
  this.name = name;
}
const me = new Person("Lee");
Person.prototype === me.__proto__; // true
me.constructor === Person; // true
```

생성자 함수인 부보 객체는 자식 객체를 생성하고, 이 때 자식 객체는 프로토타입의 constructor 프로퍼티를 통해 생성자 함수와 연결된다. 자식 객체에는 constructor 프로퍼티가 없지만 생성자 함수와 연결되어 있기 때문에 부모 객체의 prototype에서 constructor를 상속받아 사용할 수 있다. ????

## 19.4 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

생성자 함수에 의해 생성된 인스턴스는 프로토타입의 constructor 프로퍼티에 의해 생성자 함수와 연결된다. 이 때 constructor 프로퍼티가 가리키는 생성자 함수는 인스턴스를 생성한 생성자 함수(부모 함수 객체)다.

```javascript
const obj = new Object();
obj.constructor === Object; // true
const add = new Function("a", "b", "return a + b");
add.constructor === Function; // true
function Person(name) {
  this.name = name;
}
const me = new Person("Lee");
me.constructor === Person; // true
```

리터럴 표기법에 의해 생성된 객체의 경우 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 생성자 함수라고 단정할 수 없다.

```javascript
const obj = {}; // 생성자 함수가 아닌 객체 리터럴
obj.constructor === Object; // true
```

```javascript
// 생성자 함수에 의한 객체 생성, 인수가 전달되지 않아도 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성한다.
let obj = new Object(); // {}
// 인스턴스 -> Foo.prototype -> Object.prototype 순으로 프로토타입 체인 생성
class Foo extends Object {}
new Foo(); // Foo {}
// 인수가 전달되면 인수가 객체로 변환
obj = new Object(123); // Number {123}
```

리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요하다. 따라서 리터럴 표기법에 의해 생성된 객체도 가상적인 생성자 함수를 갖는다. **프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다.**

| 리터럴 표기법      | 생성자 함수 | 프로토타입         |
| ------------------ | ----------- | ------------------ |
| 객체 리터럴        | Object      | Object.prototype   |
| 함수 리터럴        | Function    | Function.prototype |
| 배열 리터럴        | Array       | Array.prototype    |
| 정규 표현식 리터럴 | RegExp      | RegExp.prototype   |

## 19.5 프로토타입의 생성 시점

프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다. 프로토타입과 생성자 함수는 언제나 쌍으로 존재하기 때문이다.

```javascript
// 함수 정의(constructor)가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
console.log(Person1.prototype); // {constructor: f}
// 생성자 함수
function Person1(name) {
  this.name = name;
}
// 화살표 함수
const Person2 = (name) => {
  this.name = name;
};
// non-constructor는 평가되지 않는다.
console.log(Person2.prototype);
```

빌트인 생성자 Object, String, Number, Boolean, Function, Array, Date, RegExp, Promise 도 힐반 함수와 마찬가지로 생성자 함수가 생성되는 시점에 프로토타입이 생성된다.

## 19.6 객체 생성 방식과 프로토타입의 결정

**객체 생성 방법**

- 객체 리터럴
- Object 생성자 함수
- 생성자 함수
- Object.create 메서드
- 클래스(ES6)

객체가 생성될 때 방법에 상관 없이 추상 연산 OrdinaryObjectCreate에 의해 생성된다.

추상 연산 OrdinaryObjectCreate은 필수적으로 생성할 객체의 프로토타입을 인수로 전달받는다.
추상 연산 OrdinaryObjectCreate은 빈 객체를 무선 생성한 후, 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 프로퍼티를 객체에 추가한다. 그리고 인수로 전달받은 프로토타입을 자신이 생성한 객체의 `[[Prototype]]` 내부 슬롯에 할당하고 생성한 객체를 반환한다.

객체 리터럴이 평가되면 추상 연산 OrdinaryObjectCreate에 의해 Object 생성자 함수와 Object.prototype과 생성된 객체 사이에 연결이 만들어진다.

생성된 객체는 이 연결로 Object.prototype을 프로토타입으로 갖게 되며, Object.prototype을 상속받아 Object.prototype의 메서드를 자유롭게 사용할 수 있다.

생성자 함수로 생성된 객체도 동일하게 Object.prototype을 상속받는다.

```javascript
const obj1 = { x: 1 }; // 바로 프로퍼티 추가
obj1.constructor === Object; // true
obj1.hasOwnProperty("x"); // true Object.prototype의 메서드 사용 가능

const obj2 = new Object(); // 일단 빈 객체를 생성
obj2.x = 1; // 이후에 프로퍼티 추가
obj2.constructor === Object; // true
obj2.hasOwnProperty("x"); // true
```

프로토타입에 프로퍼티를 추가하여 하위(자식) 객체가 상속받을 수 있으며, 자신의 메서드처럼 사용할 수 있다.

```javascript
function Person(name) {
  this.name = name;
}
// 추가된 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Lee");
const you = new Person("Kim");
me.sayHello(); // Hi! My name is Lee
you.sayHello(); // Hi! My name is Kim
```

## 19.7 프로토타입 체인

자바스크립트는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 `[[Prototype]]` 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다. 이를 프로토타입 체인이라하며, 이는 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 메커니즘이다.

```javascript
const obj = { x: 1 };
obj.hasOwnProperty("x");
// hasOwnProperty를 호출한 obj 객체에서 hasOwnProperty를 검색하였지만 없다. 그래서 프로토타입 체인을 따라 [[Prototype]] 내부 슬롯에 바인딩 되어있는 프로토타입(Object.prototpye)으로 이동하여 hasOwnProperty를 검색한다. 해당 메서드가 존재하니 자바스크립트 엔진은 Object.prototype.hasOwnProperty를 호출한다.
// Object.prototype은 프로토타입 체인의 종점이다.

obj.foo; // undefined 에러가 발생하지않는다.
// 프로토타입 체인의 종점에서도 프로퍼티를 검색할 수 없는 경우 undefined를 반환한다.
```

프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘이다.
스코프 체인은 식별자 검색을 위한 메커니즘이다.
스코프 체인과 프로토타입 체인은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는데 사용된다.

# 19.8 오버라이딩과 프로퍼티 섀도잉

- 오버라이딩 : 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하려 사용하는 방식이다.
- 오버로딩 : 함수의 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현하고 매개변수에 의해 메서드를 구별하여 호출하는 방식이다.

```javascript
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }
  // 프로토타이 메서드 추가
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };
  // 함수 반환
  return Person;
})();
const me = new Person("Lee");
//인스턴스 메서드
me.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 인스턴스 메서드 호출
me.sayHello();
```

인스턴스 메서드 sayHello는 프로토타입 메서드 sayHello를 오버라이딩했고, 프로토타입 메서드 sayHello는 가려진다. 이처럼 상속 관계에 의해 프로퍼티가 가려지는 현상을 **프로퍼티 섀도잉**이라 한다.

하위 객체를 통해 프로토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능하다. 하위 객체를 통해 프로토타입에 get 액세스는 허용되나 set 액세스는 허용되지 않는다.

## 19.9 프로토타입의 교체

```javascript
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }
  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체 ???
  Person.prototype = {
    // constructor: Person, 으로 추가해주면 기존 Person과의 연결을 유지
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };
  return Person;
})();
const me = new Person("Lee");
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
me.constructor === Object; // true
me.constructor === Person; // false
```

Object.setPrototypeOf메서드는 **proto** 접근자 프로퍼티와 동일하게 동작한다. 이 둘로 프로토타입을 교체할 수 있다.

```javascript
Object.setPrototypeOf(me, parent);
// 위와 동일하다
me.__proto__ = parent;
```

생성자 함수에 의한 프로토타입 교체는 기존 부모 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리킨다.
인스턴스에 의한 프로토타입 교체는 기존 부모 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리키지 않는다. ????

프로토타입은 직접 교체하지 않는 것이 좋다.

## 19.10 instanceof 연산자

instanceof 연산자는 이항 연산자로서 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다. 만약 우변의 피연산자가 함수가 아닌 경우 TypeError가 발생한다.

`객체 instanceof 생성자 함수`

우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true로 평가되고, 그렇지 않은 경우 false로 평가된다.
instanceof 연산자는 생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인한다.

```javascript
function Person(name) {
  this.name = name;
}
const me = new Person("Lee");
me instanceof Person; // true
me instanceof Object; // true
```

## 19.11 직접 상속

```javascript
let obj = Object.create(null); // null
// Object의 prototype을 상속받지 못한다.
obj.toString(); // typeError
obj = Object.create(Object.prototype); // obj.prototype이 null, obj = {} 와 동일
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true },
}); // obj = {x:1}
```

**Object.create의 장점**

1. new 연산자가 없이도 객체를 생성할 수 있다.
2. 프로토타입을 지정하면서 객체를 생성할 수 있다.
3. 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.

Object.prototype을 통해 빌트인 메서드를 직접 호출하는건 권장하지 않는다. Object.create 메서드로 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다.

`Object.create(null)`

ES6에서는 객체 리터럴 내부에서 **proto** 접금자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.

```javascript
const myProto = { x: 10 };
const obj = {
  y: 20,
  __protp__: myProto, // 직접 상속
};
obj.x; // 10
obj.y; // 20
```

## 19.12 정적 프로퍼티/메서드

정적 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출 할 수 있는 프로퍼티/메서드를 말한다.

정적 프로퍼티/메서드는 인스턴스의 프로토타입 체인에 속해있지 않다.

```javascript
function Person(name) {
  this.name = name;
}
// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티
Person.staticProp = "static prop";
Person.staticMethod = function () {
  console.log("staticMethod");
};

const me = new Person("Lee");

// 부모 객체에서는 정적 프로퍼티 호출가능
Person.staticMethod(); // staticMethod
// 생성자로 상속받은 자식 객체에서는 정적 프로퍼티 호출 불가능
me.staticMethod(); // TypeError
```

## 19.13 프로퍼티 존재 확인

in 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.
`key in object`

```javascript
const Person = {
  name: "Lee",
  address: "Seoul",
};
"name" in Person; // true
"age" in Person; // false
"toString" in Person; // true, Object의 prototype을 상속받았기 때문

Reflect.has(Person, "name"); // true, in과 동일하게 동작한다.

Person.hasOwnProperty("name"); // true, Object의 prototype을 상속받았을 경우만 가능
Person.hasOwnProperty("toString"); // false, 고유 키만 체크한다.
```

## 19.14 프로퍼티 열거

모든 프로퍼티를 순회하려면 for ...in을 사용한다.
Object.prototype의 프로퍼티는 열거되지 않는다.

```javascript
const Person = {
  name: "Lee",
  address: "Seoul",
};
for (const key in Person) {
  console.log(key + ":" + Person[key]);
}
// name : Lee, address : Seoul
// Object prototype에서 상속받았을 toString은 없다.
```

for ...in 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에 프로퍼티 어트리뷰트 `[[Enumerable]]` 값이 true인 프로퍼티만 순회하며 열거한다.

for ...in 문은 프로퍼티를 열거할 때 순서를 보장하지 않는다. 하지만 대부분의 모던 브라우저는 순서를 보장해주고, 숫자인 프로퍼티 키에 대해서는 자동으로 정렬해준다.

배열에서는 for ...in문을 사용하지 않고 일반적으로 for 반복문이나 for ...of문 또는 Array.prototype.forEach 메서드를 사용하기를 권장한다. 상속받은 프로퍼티가 포함될 수 있기 때문이다.

```javascript
const arr = [1, 2, 3];
arr.x = 10; // 배열도 객체이므로 프로퍼티를 가질 수 있다.
// for... in은 프로퍼티를 포함한 key를 순회한다.
for (const i in key) {
  console.log(arr[i]); // 1 2 3 10
}
arr.length; // 3
// forEach 메서드는 요소가 아닌 프로퍼티는 제외한다.
arr.forEach((v) => console.log(v)); // 1 2 3
// for ...of는 for in과 다르게 키가 아닌 값을 순회한다.
for (const value of arr) {
  console.log(value); // 1 2 3 (10은 값이 없으므로 출력하지 않는다)
}
```

Object.key 메서드는 객체 자신이 결거 가능한 프로퍼티 키를 배열로 반환한다.
ES8에서 도입된 Object.value 메서드는 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환한다.
ES8에 도입된 Object.entries 메서드는 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환한다.

```javascript
const person = {
  name: "Lee",
  address: "Seoul",
  __proto__: { age: 20 },
};
console.log(Object.key(person)); // ['name', 'address']
console.log(Object.value(person)); // ['Lee', 'Seoul']
console.log(Object.entries(person)); // [['name', 'address'], ['Lee', 'Seoul']]
```
