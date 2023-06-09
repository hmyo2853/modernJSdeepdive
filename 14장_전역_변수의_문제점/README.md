# 14장 전역 변수의 문제점

## 14.1 변수의 생명 주기

변수는 생성되고 소멸되는 생명 주기가 있다. 변수는 자신이 선언된 위치에서 생성되고 소멸한다.

함수 내부의 변수는 함수가 호출되면 생성되고 함수가 종료하면 소멸한다. 따라서 지역 변수의 생명 주기는 함수의 생명 주기와 같다.

```javascript
var x = "global";
function foo() {
  console.log(x); // ???
  var x = "local";
}
foo();
console.log(x); // global
```

## 14.2 전역 변수의 문제점

자바스크립트는 모든 코드가 전역 변수를 참조하고 변경할 수 있는 **암묵적 결합**을 허용한다.
변수의 스코프가 크면 클수록 코드의 가독성을 나빠지고 의도치 않게 상태가 변경될 수 있는 위험성이 높아진다.

전역 변수는 생명 주기가 길다. 전역 변수의 검색 속도가 가장 느리다.

## 14.3 전역 변수의 사용을 억제하는 법

전역 변수의 무분별한 사용은 위험하다. 지역 변수를 사용하여 변수의 스코프를 좁게 만들수록 좋다.
모든 코드를 즉시 실행 함수로 감싸면 모든 변수는 즉시 실행 함수의 지역 변수가 된다. 이러한 특성을 이용하여 전역 변수의 사용을 제한 할 수 있다.

이 방법은 라이브러리 등에 자주 사용된다.

```javascript
(function () {
  var foo = 10; // 지역변수
  ...
}, ());
console.log(foo) // ReferenceError: foo is not defined
```

**모듈 패턴**
클래스를 모방하여 관련이 있는 함수와 변수들을 모아 즉시 실행 함수로 감싸고 하나의 모듈을 만드는 방법이다.
모듈 패턴의 특징은 전역 변수의 억제는 물론 캡슐화까지 구현 가능하다는 점이다.

캡슐화는 객체의 특정 프로퍼티나 매서드를 갑출 목적으로 사용한다. 이를 정보 은닉이라 한다.

```javascript
var Counter = function () {
  var num = 0; // private 정보 은닉
  return {
    // public 외부로 공개할 매서드
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
};
console.log(num); // undefined
console.log(Counter.increase()); // 1
console.log(Counter.decrease()); // 0
```

ES6 모듈을 사용하면 전역 변수를 사용할 수 없다. ES6 모듈은 독자적인 모듈 스코프를 제공한다.

하지만 ES6는 IE를 포함한 구현 브라우저에서는 동작하지 않으며, 사용하더라도 트랜스파일링이나 번들링이 필요하기 때문에 Webpack 등의 모듈 번들러를 사용하는 것이 일반적이다.
