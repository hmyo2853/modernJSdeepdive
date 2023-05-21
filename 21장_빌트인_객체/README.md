# 21장 빌트인 객체

## 21.1 자바스크립트 객체의 분류

1. **표준 빌트인 객체** : ECMAScript 사양에 정의된 객체, 애플리케이션 전역의 공통 기능을 제공한다. 자바스크립트 실행 환경에 관계 없이 언제나 사용할 수 있다. 전역 객체의 프로퍼티로서 제공되며, 별도의 선언 없이 언제나 사용, 참조할 수 있다.

2. **호스트 객체** : ECMAScript 사양에 정의되어 있지 않지만 자바스크립트 실행환경에서 추가로 제공되는 객체. 브라우저에서는 DOM, BOM, Canvas, XMLHttpRequest, fetch, requestAnimationFrame, SVG, Web Storage, Web Component, Web Worker와 같은 클라이언트 사이드 Web API를 호스트 객체로 제공하고, Node.js 환경에서는 Node.js 고유의 API 호스트 객체로 제공한다.

3. **사용자 정의 객체** : 기본 제공되는 객체가 아닌 사용자가 직접 정의한 객체.

## 21.2 표준 빌트인 객체

자바스크립트는 Object, String, Number, ..., Function, Promise, JSON, Error 등 40여개의 표준 빌트인 객체를 제공한다. Math, Reflect, JSON을 제외한 표준 빑트인 객체는 인스턴스를 생성할 수 있다.(생성자 함수)

```javascript
const StrObj = new String("Lee");
const numObj = new Number(123);
const boolObj = new Boolean(true);
const func = new Function("x", "return x * x");
const arr = new Array(1, 2, 3);
const regExp = new RegExp(/ab+c/i);
const date = new Date();
```

표준 빌트인 객체는 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공한다.

```javascript
const numObj = new Number(1.5);

// toFixed는 Number.prototype의 프로토타입 메서드다
numObj.toFixed(); // 2
// isInteger는 Number의 정적 매서드다.
Number.isInsteger(0.5); // false
```

## 21.3 원시값과 래퍼 객체

원시값은 객체가 아니므로 프로퍼티는 메서드를 가질 수 없는데도 원시값이 마치 객체인 것처럼 동작한다.
원시값에 대해 마치 객체처럼 마침표 표기법(또는 대괄호 표기법)으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해준다. 암묵적으로 연관된 객체를 생성하여 생성된 객체로 프로퍼티에 접근하거나 매서드를 호출하고 다시 원시값으로 되돌린다.

```javascript
const str = "hello";
// 원시타입이 프로퍼티와 매서드를 갖고 있는 객체처럼 동작한다.
str.length; // 5
str.toUpperCase(); // HELLO
```

이처럼 원시값에 대해 객체처럼 접근하여 생성되는 임시 객체를 **래퍼 객체**라 한다.
어떠한 원시값에 마침표 표기법으로 접근하면 그 순간 래퍼 객체를 찾아 인스턴스가 생성되고, 생성된 인스턴스는 래퍼 객체의 prototype 매서드를 상속받아 사용한다.
그 후 래퍼 객체의 처리가 종료되면 래퍼 객체 내부 슬롯에 할당되어있던 원시값을 식별자로 돌려주고, 래퍼 객체는 가비지 컬랙션 대상이 된다.

래퍼 객체로 인해 표준 빌트인 객체 String, Number, Boolean, Symbol의 프로토타입 매서드 또는 프로퍼티를 참조할 수 있다. 따라서 new 연산자와 함께 호출하여 인스턴스를 생성할 필요가 없으며 권장하지 않는다.

null과 undefined는 래퍼 객체를 생성하지 않아, 객체처럼 사용하려고 하면 에러가 발생된다.

## 21.4 전역 객체

전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어던 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체이다.

- 브라우저 환경 : window, self, this, frames
- Node.js 환경 : global

1. 전역 객체는 개발자가 의도적으로 생성할 수 없다. 전역 객체를 생성할 생성자 함수가 제공되지 않는다.
2. 전역 객체의 프로퍼티를 참조할 때 window(global)를 생략할 수 있다. `window.parseInt('F', 16); => parseInt('F', 16);`
3. 전역 객체는 표준 빌트인 객체를 프로퍼티로 가지고 있다.
4. 자바스크립트 실행 환경(브라우저 혹은 Node.js)에 따라 추가적인 프로퍼티와 메서드를 갖는다.
5. var 키워드로 선언한 전역 변수와 선언하지 않는 전역 변수에 값을 할당하면 암묵적 전역, 그리고 전역 함수는 전역 객체의 프로퍼티가 된다.

```javascript
var foo = 1;
window.foo; // 1
ber = 2; // window.bar = 2
function baz() {
  return 3;
} // window.baz(); 3
```

6. let 이나 const 로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다. `let foo = 123; window.foo => undefined`
7. 브라우저 환경의 모든 자바스크립트 코드는 window를 공유한다. 코드를 분리해도 모든 자바스크립트 코드는 하나의 전역을 공유한다.

빌트인 전역 프로퍼티는 전역 객체의 프로퍼티를 말한다. 주로 애플리케이션 전역에서 사용하는 값을 제공한다.

**Infinity, NaN, undefined**

```javascript
// Infinity
window.Infinity === Infinity;
3 / 0; // Infinity
-3 / 0; // -Infinity
typeof Infinity; // number

// NaN
window.NaN === NaN;
Number("xyz"); // NaN
typeof NaN; // number

// undefined
window.undefined === undefined;
var foo; // undefined
typeof undefined; // undefined
```

빌트인 전역 함수는 전역 객체의 매서드다.

**eval** 함수는 자바스크립트 코드를 나타내는 문자열을 인수로 전달받는다. 전달받은 문자열이 표현시기이면 문자열 코드를 런타임에 평가하여 값을 생성하고, 전달받은 인수가 표현식이 아닌 문이라면 문자열 코드를 런타임에 실행한다. 문자열 코드가 여러개의 문으로 이루어져 있다면 모든 문을 실행한다.

```javascript
// 표현식의 문
eval("1+2;"); // 3
// 표현식이 아닌 문
eval("var x = 5;"); // undefined
// 객체 리터럴과 함수 리터럴은 괄호로 둘러싼다
eval("({ a: 1 })");
eval("(function() { return 1; })");
// 여러개의 문이면 마지막 결과값을 반환한다.
eval("1+2; 3+4;"); // 7
```

eval 함수는 기존의 스코프를 런타임에 동적으로 수정한다. 따라서 eval 함수로 통해 사용자로부터 입력받은 콘텐츠를 실행하는 것은 보안에 매우 취약하다. 또한 eval 함수를 통해 실행되는 코드는 자바스크립트 엔진에 의해 최적화가 수행되지 않으므로 처리 속도가 느리다. **eval 함수의 사용은 금지해야 한다.**

**inFinite** 함수는 인수가 유한수이면 true, 무한수이면 false를 반환한다. 숫자가 아닌 경우 숫자로 반환하여 검사를 수행한다.

```javascript
isFinite(0); // true
inFinite("10"); // true
isFinite(NaN); // false
inFinite(null); // true null => 0
```

**isNaN** 함수는 인수가 NaN인지 검사한다.

```javascript
isNaN(NaN); // true
isNaN(10); // false
isNaN("10"); // false
isNaN("blabla"); // true
isNaN(""); // false '' => 0
isNaN(" "); // false ' ' => 0
```

**parseFloat** 함수는 전달받은 문자열 인수를 실수(부동 소수점 숫자)로 해석하여 반환한다.

```javascript
parseFloat("3.14"); // 3.14
parseFloat("10.0"); // 10
parseFloat("  60  "); // 공백무시 60
parseFloat("34  45 66"); // 34 첫번째 문자열 반환
// 첫번째 문자열을 숫자로 반환할수 없다면 NaN으로 반환
parseFloat("He was 40"); // NaN
```

**parseInt** 함수는 전달받은 인수를 정수로 반환한다.

```javascript
parseInt("10.123"); // 10
parseInt(10); // 숫자를 문자열로 전환후 정수로 해석하여 반환
// 두번째 인자를 00진수로 해석하고 10잔수로 반환
parseInt(10, 2); // 2
parseInt("10", 16); // 16
parseInt("0xf"); // 16진수로 해석하고 10진수로 반환, 15
parseInt("0b10"); // 0이후 무시 0
parseInt("20", 2); // 2는 2진수로 해석 불가, NaN
parseInt("1A0"); // 10진수로는 A해석 불가, 1
parseInt("FG", 16); // 16진수로는 F 해석 불가, 15
// 첫번째 문자열을 반환
parseInt("34 40 45"); // 34
parseInt("He was 40"); // NaN
parseInt("   60   "); // 60 공백 무시

// 반대로 00진수 문자열 반환
const x = 15;
x.toString(2); // 2잔수 '1111'
x.toString(16); // 16진수 'f'
x.toString(); // 문자열로 반환, '15'
```

**encodeURI / decodeURI** 함수는 문자열을 인코딩(encodeURI)하고, 디코딩(decodeURI)한다.

```javascript
const uri = "http://example.com?name=이가은&job=programmer&woman";
encodeURI(uri); // 'http://example.com?name=%EC%9D%B4%EA%B0%80%EC%9D%80&job=programmer&woman'
decodeURI(encodeURI(uri)); // 'http://example.com?name=이가은&job=programmer&woman'
```

**encodeURIComponent / decodeURIComponent** 함수는 문자열을 인코딩하고, 디코딩한다. 대신 스트링 구분자로 사용하는 `=, ?, &`까지 인코딩한다.

```javascript
const uri = "http://example.com?name=이가은&job=programmer&woman";
encodeURIComponent(uri); // 'http%3A%2F%2Fexample.com%3Fname%3D%EC%9D%B4%EA%B0%80%EC%9D%80%26job%3Dprogrammer%26woman'
decodeURIComponent(encodeURIComponent(uri)); // 'http://example.com?name=이가은&job=programmer&woman'
```

전역 변수는 프로퍼티이지만 delete 연산자로 삭제할 수 없다.

```javascript
var x = 10; // 전역 변수
function foo() {
  y = 20; // 선언하지 않는 식별자에 값 할당
  console.log(x + y);
}
foo(); // 30, y에 암묵적 할당
window.x; // 10
window.y; // 20
delete x; // 삭제되지 않는다
delete y; // 프로퍼티 삭제 undefined
```
