# 22장 this

## 22.1 this 키워드

생성자 함수를 정의하는 시점에는 아직 인스턴스를 생성하기 이전이므로 생성자 함수가 인스턴스를 가리키는 식별자를 알 수 없다. 따라서 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요하다. 이를 위해 자바스크립트는 this라는 특별한 식별자를 제공한다.

**this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가라키는 자기 참조 변수다. this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.**

함수를 호출하면 this와 arguments 객체가 암묵적으로 함수 내부에 전달된다. this가 가리키는 값, 즉 this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.

```javascript
// 객체 리터럴
const circle1 = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체를 가리킨다.
    return 2 * this.radius;
  },
};
circle1.getDiameter(); // 10

function Circle(radius) {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  return 2 * this.radius;
};
// 인스턴스 생성
const circle2 = new Circle(5);
circle2.getDiameter(); // 10
```

전역에서의 this와 일반 함수에서의 this는 전역 객체 window를 가리킨다. 매서드 내부에서의 this는 매서드를 호출한 객체를 가리킨다. 생성자 함수 내부에서의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
하지만 strict mode가 적용된 일반 함수 내부의 this에는 undefined가 바인딩 된다. 일반 함수 내부에서 this를 사용할 필요가 없기 때문이다 ??????

## 22.2 함수 호출 방식과 this 바인딩

1. 일반 함수 호출
2. 매서드 호출
3. 생성자 함수 호출
4. Function.prototype.apply/call/bind 매서드에 의한 간접 호출

```javascript
const foo = function () {
  console.dir(this);
};
foo(); // window 1. 일반 함수 호출
const obj = { foo };
obj.foo(); // obj 2. 매서드 호출
new foo(); // foo {} 3. 생성자 함수 호출

const bar = { name: "bar" }; // 4. apply/call/bind 매서드에 의한 간접 호출
foo.call(bar); // bar
foo.apply(bar); // bar
foo.bind(bar)(); // bar
```
