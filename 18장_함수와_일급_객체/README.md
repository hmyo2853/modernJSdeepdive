# 18장 함수와 일급 객체

## 18.1 일급 객체

**일급 객체란?**

1. 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
2. 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다.
4. 함수의 반환값으로 사용할 수 있다.

자바스크립트의 함수는 위의 조건을 모두 만족하므로 일급 객체이다.
이는 함수형 프로그래밍을 가능케 하는 자바스크림트의 장점 중 하나이다.

```javascript
// 무명 리터럴로 생성, 변수 increase에 저장
const increase = function (num) {
  return ++num;
};
const decrease = function (num) {
  return --num;
};
const auxs = { increase, decrease };
// 변수 auxs에 저장된 함수를 매개변수에 전달
function makeCounter(aux) {
  let num = 0;
  // 함수의 반환값으로 사용
  return function () {
    num = aux(num);
    return num;
  };
}
// 함수를 매개변수로 전달
const increaser = makeCounter(auxs.increase);
const decreaser = makeCounter(auxs.decrease);
```

## 18.2 함수 객체의 프로퍼티

함수도 객체이므로 프로퍼티를 가질 수 있다. 해당 함수의 모든 프로퍼티의 프로퍼티 어트리뷰트를 Object.getOwnPropertyDescriptors 메서드로 확인할 수 있다.

함수 객체의 arguments 프로퍼티 값은 arguments 객체이다. 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체이다. 함수 외부에서는 참조 불가능하다.
하지만 ES3부터 폐지되었다.

함수에 선언된 매개변수의 개수보다 인수를 적게 전달하면 전달되지 않은 매개변수는 undefined로 초기화된다. 매개변수의 개수보다 인수를 더 많이 전달하면 초과된 인수는 무시된다.
그렇다고 초과된 인수가 그냥 버려지는 것이 아니다. 암묵적으로 arguments 객체의 프로퍼티로 저장 된다.

- arguments 객체의 Sumbol프로퍼티 : arguments 객체를 순회 가능한 자료구조인 이터러블로 만들기위한 프로퍼티이다. `arguments[Sumbole.literator]().next()` next 메서드로 객체 arguments를 순회한다.

arguments 객체는 매개변수 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다.
arguments 객체는 length 프로퍼티가 있는 유사 배열 객체이므로 for문으로 순회할 수 있다. 하지만 인자 정보를 담고 있지만 실제 배열은 아니다.
따라서 배열 메서드를 사용하면 에러가 발생된다. (slice, map 등등)

배열 메서드를 사용하려면 Function.prototype.call, Function.prototype.apply를 사용해 간접호출해야하는 번거로움이 있다.

ES6에서는 이와 같은 번거로움을 해결하기 위해 Rest 파라미터를 도입했다.

```javascript
// Rest parameter 로 reduce 사용
function sum(...args) {
  return args.reduce((pre, curr) => pre + curr, 0);
}
sum(1, 2); //3
```

| 프로퍼티명       | 설명                                                                                                                                                                                |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| caller           | 비표준 프로퍼티다, 사용하지 말자. 함수 객체 내부에서 자신을 호출한 함수를 가리킨다.                                                                                                 |
| length           | 함수를 정의할 때 선언한 매개변수의 개수를 가리킨다. arguments(인자)의 개수와는 다르다.                                                                                              |
| name             | - 함수의 이름을 반환한다. ES5와 ES6 값이 다르다. <br /> - ES5 : 익명 함수 표식에서 빈 문자열을 값으로 갖는다. <br /> - ES6 : 익명 함수 표식에서 . 앞에 있는 식별자를 값으로 갖는다. |
| **proto** 접근자 | `[[Prototype]]` 내부 슬롯 객체에 접근하기 위한 접근자                                                                                                                               |
| prototype        | constructor만이 소유하는 프로퍼티, 생성자 함수로 호출할 수 있는 함수 객체이다.                                                                                                      |

```javascript
const obj = { a: 1 };
obj.__proto__ === Object.prototype; // true
obj.hasOwnProperty("a"); // true
obj.hasOwnProperty("__proto__"); // flase ??????
```
