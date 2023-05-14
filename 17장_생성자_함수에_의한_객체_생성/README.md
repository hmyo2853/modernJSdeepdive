# 17장 생성자 함수에 의한 객체 생성

## 17.1 Object 생성자 함수

new 연산자와 함께 Object 생성자 함수를 호출하면 빈 객체를 생성하여 반환한다.

```javascript
const person = new Object(); // 빈 객체 {}
```

생성자 함수 - new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수
인스턴스 - 생성자 함수에 의해 생성된 객체

**빌트인 생성자 함수**

new Object, new String, new Number, new Boolean, new Function, new Array, new Date, new RegExp, new Promise

## 17.2 생성자 함수

객체는 프로퍼티를 통해 객체 고유의 상태를 표현한다.
프로퍼티는 객체마다 프로퍼티 값이 다를 수 있지만 메서드는 내용이 동일한 경우가 일반적이다.
객체 리터럴에 의해 객체를 생성하는 경우 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메서드를 기술해야 한다.

생성자 함수에 의한 객체 생성 방식은 마치 객체(인스턴스)를 생성하기 위한 템플릿(클래스)처럼 생성자 함수를 사용하여 프로퍼티 구조가 동일한 여러 객체를 간편하게 생성할 수 있다.

생성자 함수는 꼭 new와 함께 호출하지 않으면 일반 함수 호출로 동작한다.

```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
// 인스턴스 여러개 생성
const circle1 = new Circle(5); // 10
const circle2 = new Circle(10); // 20

//일반 함수
const circle3 = Circle(10); // undefined return 반환값이 없다
```

**this**

this는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이다.
this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.

| 함수 호출 방식     | this 바인딩(this의 값)                 |
| ------------------ | -------------------------------------- |
| 일반 함수로서 호출 | 전역 객체                              |
| 메서드로서 호출    | 메서드를 호출한 객체(마침표 앞의 객체) |
| 생성자 함수로 호출 | 생성자 함수가 (미래에) 생성할 인스턴스 |

```javascript
function foo() {
  console.log(this);
}

//일반적 함수로 호출
foo(); // window 전역

const obj = { foo };
// 메서드로 호출
obj.foo(); // obj

// 생성자 함수로 호출
const inst = new foo(); // inst
```

생성자 함수는 인스턴스를 생성하는 것과 동시에 인스턴스를 초기화한다. 하지만 생성하는 것은 필수이고 초기화는 선택이다.
자바스크립트 엔진은 암묵적인 처리를 통해 인스턴스를 생성하고 반환한다.
인스턴스는 암묵적으로 this에 바인딩 된다. 이 처리는 함수 몸체의 코드가 한 줄씩 실행되는 런타임 이전에 실행된다.

**바인딩**

식별자와 값을 연결하는 광정. 변수 선언은 변수 이름(식별자)와 확보된 메모리 공간의 주소를 바인딩하는 것이다.

```javascript
function Circle(radius) {
  console.log(this); // Circle {} this 바인딩

  // this에 바인딩 되어있는 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
  // 완성된 인스턴스가 바인딩된 this로 암묵적으로 반환된다.
}
const circle = new Circle(1); // 생성자 함수로 암묵적으로 반환된 this
```

this를 반환하지 않고 다른 객체를 반환하게 된다면 this 반환이 무시되고 생성자 함수에서 반환한 객체를 반환한다.
따라서 생성자 함수 내부에서는 return문을 반드시 생략해야 한다.

일반 함수도 생성자로서 호출할 수 있다.

```javascript
function foo() {}
foo(); // 일반 함수로 호출 [[Call]] 호출
new foo(); // 생성자 함수로 호출 [[Construct]] 호출
```

callable은 호출할 수 있는 객체, 즉 함수를 ㅁ라하며, constructor는 생성자 함수로 호출할 수 있는 함수, non-constructor는 객체를 생성자 함수로서 호출할 수 없는 함수를 의미한다.
모든 함수 객체는 `[[Call]]`을 갖고 있어 호출 할 수 있지만, `[[Construct]]`를 모두 갖고 있지는 않다.

자바스크립트 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 constructor와 non-constructor로 구분한다.

- constructor: 함수 선언문, 함수 표현식, 클래스
- non-constructor: 매메서드(ES6 메서드 축약 표현), 화살표 함수

??????

new 연산자와 함께 호출하는 생성자 함수는 non-constructor가 아닌 constructor이어야 한다.

```javascript
// 일반함수
function add(x, y) {
  return x + y;
}
let inst = new add(); // {} 반환문이 무시된다. 빈객체가 생성되어 반환

//객체를 반환하는 일반함수
function createUser(name, role) {
  return { name, role };
}
inst = new createUser("Lee", "admin"); // {name: 'Lee', role: 'admin'}
```

생성자 함수는 일반 함수와 구분을 위해 첫 문자를 대문자로 기술하는 파스칼 케이스로 명명하여 사용한다.

**new.target**

new 연산자와 함께 생성된 생성자 함수에서 new.target은 함수 내부에서 함수 자신을 가리킨다.
new 연산자 없이 생성자 함수를 호출하면 new.target을 통해 생성자 함수로서 호출한다.

```javascript
function Circle(radius) {
  if (!new.target) {
    // new가 없을때 조건문을 읽는다
    return new Circle(radius);
  }
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
const circle = Circle(1); // new가 없어도 new 생성자 함수로 호출
```
