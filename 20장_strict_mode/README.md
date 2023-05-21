# 20장 strict mode

## 20.1 strict mode란?

ES5부터 추가된 strict mode(엄격 모드)는 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

- ESLint : strict mode와 유사한 효과를 얻을 수 있다. 정적 분석 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재거 오류까지 찾아내고 오류의 원인을 리포팅해주는 유용한 도구이다. (vsc에서 우리가 자주 사용하고 있는 오류 캐치 도구)

## 20.2 strict mode의 적용

strict mode를 적용하려면 전역의 선두 도는 함수 몸체의 선두에 `'use strict';`를 추가한다.

```javascript
// 전역 선두에 사용
"use strict";

function foo() {
  x = 10; // ReferenceError
}
foo();

// 함수 내부 선두에 사용
function foo() {
  "use strict";
  x = 10; // ReferenceError
}
foo();

// 선두에 두지 않으면 적용x
function foo() {
  x = 10; // 에러가 발생되지 않는다.
  ("use strict");
}
foo();
```

## 20.3 전역에 strict mode를 적용하는 것은 피하자

전역에 적용된 strict mode는 스크립트 단위로 적용되기 때문에, 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분해서 사용하는 것을 추천한다.

```javascript
// 스크립트 단위로 적용
...
<script>
  x = 1; //에러가 발생되지 않는다.
</script>
<script>
  'use strict';
  y = 1; // ReferenceError
</script>
...

// 즉시 실행 함수로 사용
(function () {
  'use strict';
  ...
}());
```

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

함수 단위로 strict mode를 적용할 수 있기때문에, 어떤 함수는 non-strict mode, 어떤 함수는 strict mode로 각각 적용할 수 있지만 번거롭고, 바람직하지 않다. 따라서 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

```javascript
(function () {
  // non-strict mode
  var let = 10; // 에러 x

  function foo() {
    "use strict";
    let = 20; // SynctaxError
  }
  foo();
})();
```

## 20.5 strict mode가 발생시키는 에러

- 암묵적 선언 : 선언하지 않은 변수를 참조하면 ReferenceError가 발생된다.

```javascript
(function () {
  "use strict";
  x = 1; // ReferenceError
})();
```

- 변수, 함수, 매개변수의 삭제 : delete 연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생한다.

```javascript
"use strict";
var x = 1;
delete x; // SyntaxError
function foo(a) {
  delete a; // SyntaxError
}
delete foo; // SyntaxError
```

- 매개변수 이름의 중복 : 중복된 매개변수 이름을 사용하면 SyntaxError가 발생된다.

```javascript
"use strict";
function foo(x, x) {
  return x + x; // SyntaxError
}
```

- with 문의 사용 : with문을 사용하면 SyntaxError가 발생된다. with문은 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있으 코드가 간단해지지만 성능과 가독성이 나빠진다. 사용을 지양한다.

```javascript
"use strict";
// SyntaxError
with ({ x: 1 }) {
  console.log(x);
}
```

## 20.4 strict mode 적용에 의한 변화

- 일반 함수의 this : strict mode에서 함수를 일반 함수로 호출하면 this에 undefined가 바인딩 된다. 이때 에러가 발생되진 않는다.

```javascript
"use strict";
function foo() {
  console.log(this); // undefined
}
```

- arguments 객체 : strict mode에서 매개변수에 전달된 인수를 재할당하여 변경해도 arguments객체에 반영되지 않는다.

```javascript
(function (a) {
  "use strict";
  a = 2;
  console.log(arguments); // {a:1, length:1} // 변경되지 않는다.
})(1);
```
