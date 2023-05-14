# 16장 프로퍼티 어트리뷰트

## 16.1 내부 슬롯과 내부 메서드

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 **의사 프로퍼티**와 **의사 메서드**다.
이들은 이중 대괄호로 감싸져 있다. `[[...]]`

내부 슬롯과 내부 메서드는 자바스크립트 엔진에서 실제로 동작하지만 개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아니다.

모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 갖는다. 이는 내부 로직이므로 직접 접근할 수 없지만 `__proto__`를 통해 간접적으로 접근 할 수 있다.

```javascript
const o = {};
o.[[Prototype]] // synctaxError
o.__proto__ // Object.prototype
```

## 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 어트리뷰트를 기본값으로 자동 정의한다.

프로퍼티 상태란 **프로퍼티의 값**`(value)`, **값의 갱신 가능 여부**`(writable)`, **열거 가능 여부**. `(enumerable)`, **정의 가능 여부**`(configurable)`를 말한다.

`Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 프로퍼티 어트리뷰트에 접근 할 수 있다.

```javascript
const person = {
  name: "Lee",
};
console.log(Object.getOwnPropertyDescriptor(person, "name"));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```

`Object.getOwnPropertyDescriptor` 메서드를 호출할 때 첫 번째 매개변수에는 객체의 참조를 전달하고, 두 번째 매개변수에는 프로퍼티 키를 문자열로 전달한다.

`Object.getOwnPropertyDescriptor` 메서드로 반환되는 객체는 **프로퍼티 디스크립터 객체**이다.

```javascript
const person = {
  name: "Lee",
};
// 프로퍼티 동적 생성
person.age = 20;
// 모든 프로퍼티 반환
console.log(Object.getOwnPropertyDescriptor(person));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
// {value: 20, writable: true, enumerable: true, configurable: true}
```

## 16.3 데이터 프로퍼티와 접근자 프로퍼티

- 데이터 프로퍼티 : 키와 값으로 구성된 일반적인 프로퍼티. 일반적으로 알고있는 모든 프로퍼티이다.
- 접근자 프로퍼티 : 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티.

### 데이터 프로퍼티

해당 프로퍼티 어트리뷰트는 자바스크립트 엔진이 프로퍼티를 생성할 때 기본값으로 자동 정의된다.

| 프로퍼티 어트리뷰트 | 설명                                                                                                                                                                                                                                          |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[[Value]]`         | - 프로퍼티 키를 통해 프로퍼티 값에 반환되는 값이다.                                                                                                                                                                                           |
| `[[Writable]]`      | - 프로퍼티 값의 변경 가능 여부를 나타내며 불리언 값을 갖는다. <br /> - `[[Writable]]`의 값이 false인 경우 해당 프로퍼티의 `[[Value]]`의 값을 변경 할 수 없는 읽기 전용 프로퍼티가 된다.                                                       |
| `[[Enumerable]]`    | - 프로퍼티의 열거 가능 여부를 나타내며 불리언 값을 갖는다. <br /> - `[[Enumerable]]`의 값이 false인 경우 해당 프로퍼티는 for ... in문이나 Object.keys 메서드 등으로 열거할 수 없다.                                                           |
| `[[Configurable]]`  | - 프로퍼티의 재정의 기능 여부를 나타내며 불리언 값을 갖는다. <br /> - `[[Configurable]]`의 값이 false인 경우 해당 프로퍼티의 삭제, 값의 변경이 금지된다. 단 true인 경우 `[[Value]]`의 변경과 `[[Writable]]`을 false로 변경하는 것은 허용한다. |

프로퍼티가 처음 생성될 때 `[[value]]`의 값은 프로퍼티 값으로 초기화되며 `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`의 값은 true로 초기화된다. 동적으로 추가되는 프로퍼티도 마찬가지이다.

### 접근자 프로퍼티

자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

| 프로퍼티 어트리뷰트 | 설명                                                                                                                                                                                             |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `[[Get]]`           | 데이터 프로퍼티의 값을 읽을때 호출되는 접근자 함수. 접근자 프로퍼티 키로 프로퍼티 값에 접근하면 프로퍼티 어트리뷰터 `[[Get]]`의 값, 즉 getter 함수가 호출되고 그 결과 프로퍼티 값이 반환된다.    |
| `[[Set]]`           | 데이터 프로퍼티의 값을 저장할 때 호출되는 접근자 함수. 접근자 프로퍼티 키로 프로퍼티 값을 저장하면 프로퍼티 어트리뷰트 `[[Set]]`의 값, 즉 setter 함수가 호출되고 그 결과 프로퍼티 값이 저장된다. |
| `[[Enumerable]]`    | 데이터 프로퍼티와 동일하다.                                                                                                                                                                      |
| `[[Configurable]]`  | 데이터 프로퍼티와 동일하다.                                                                                                                                                                      |

```javascript
const person = {
  // 데이터 프로퍼티
  firstName: 'Gaeun',
  lastName: 'Lee',

  // getter
  get fullName() {
    return `${firstName} ${lastName}`;
  },
  // setter
  set fullName() {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

// 접근자 프로퍼티로 값을 저장하면 setter함수가 호출된다.
person.fullName = 'Heegun Lee';
console.log(person); // {firstName: 'Heegun', lastName: 'Lee'}; split 된 결과물

// 접근자 프로퍼티로 fullName에 접근하며 getter함수가 호출된다.
console.log(person.fullName); // Heegun Lee

Object.getOwnPropertyDescriptor(person, 'firstName') // 데이터 프로퍼티
// {value: 'Heegeun', weritable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor(person, 'fullName') // 접근자 프로퍼티
// {value: f, weritable: f, enumerable: true, configurable: true}
```

접근자 프로퍼티 `fullName`으로 프로퍼티 값에 접근하면 내부적으로 `[[Get]]` 내부 메서드가 호출되어 다음과 같이 동작된다.

1. 프로퍼티 키가 유효한지 확인한다. 프로퍼티 키는 문자열 또는 심벌이어야 한다. 프로퍼티 키가 'fullName'은 문자열이므로 유효남 프로퍼티 키다.
2. 프로토타입 체인에서 프로퍼티를 검색한다. person 객체에 fullName 프로퍼티가 존재한다.
3. 검색된 fullName 프로퍼티가 어떤 프로퍼티인지 확인한다. fullName은 접근자 프로퍼티이다.
4. 접근자 프로퍼티 fullName의 프로퍼티 어트리뷰트인 getter 함수(`[[Get]]`)를 호출하여 그 결과를 반환한다.

## 16.4 프로퍼티 정의

프로퍼티 정의란 새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의하는 것을 말한다.

```javascript
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, "firstName", {
  value: "Gaeun",
  writable: true,
  enumerable: true,
  configurable: true,
});

// 디스크립터 객체의 프로퍼티를 누락시키면 undefined, false가 기본이다.
Object.defineProperty(person, "lastName", {
  value: "Lee",
});

// writable 이 false인 경우 value 값을 변경하려면 변경되지 않고, 에러가 발생되지 않으며 무시된다.
person.lastName = "kim";
// configurable 값이 false인 경우 해당 프로퍼티 삭제가 불가능하며, 에러가 발생되지 않고 무시된다.
delete person.lastName;
// configurable 값이 false인 경우 값 재정의가 불가능하다.
Object.defineProperty(person, "lastName", {
  enumerable: true,
}); // Uncaught TypeError
```

`Object.defineProperty` 메서드로 프로퍼티를 정의할 때 프로퍼티 디스크럽터 객체의 프로퍼티를 일부 생략 할 수 있는데, 이 때 기본 값이 적용된다.

| 프로퍼티     | 생략했을때 기본 값 |
| ------------ | ------------------ |
| value        | undefined          |
| get          | undefined          |
| set          | undefined          |
| writable     | false              |
| enumerable   | false              |
| configurable | false              |

`Object.defineProperty` 메서드로 여러개의 프로퍼티를 한 번에 정의 할 수 있다. (데이터 프로퍼티 , getter 함수, setter 함수)

## 16.5 객체 변경 방지

객체는 변경 가능한 값이므로 재할당 없이 직접 변경할 수 있다.
객체 변경 방지 메서드들은 객체 변경을 금지하는 강도가 다르다.

**객체 확장 금지 Object.preventExtensions**

객체 확장 금지란 프로퍼티 추가 금지를 의미한다.

```javascript
const person = { name: "Lee" };
// 객체 확장 금지 추가
Object.preventExtensions(person);
// 객체 확장 불가
person.age = 20; // 무시
// 삭제는 가능하다.
delete person.name;
```

**객체 밀봉 Object.seal**

객체 밀봉은 읽기과 쓰기만 가능하다.

```javascript
const person = { name: "Lee" };
// 객체 밀봉, 추가/삭제/재정의 금지
Object.seal(person);
// 프로퍼티 추가 금지
person.age = 20; // 무시
// 프로퍼티 삭제 금지
delete person.name; // 무시
// 프로퍼티 값 갱신 가능
person.name = "kim";
// 어트리뷰트 재정의 금지
Object.defineProperty(person, "name", { configurable: true }); // TypeErroe
```

**객체 동결 Object.freez**

객체를 동결하여 일기만 가능하다.

```javascript
const person = { name: "Lee" };
// 객체 밀봉, 추가/삭제/재정의 금지
Object.freez(person);
// 프로퍼티 추가 금지
person.age = 20; // 무시
// 프로퍼티 삭제 금지
delete person.name; // 무시
// 프로퍼티 값 갱신 금지
person.name = "kim"; // 무시
// 어트리뷰트 재정의 금지
Object.defineProperty(person, "name", { configurable: true }); // TypeErroe
```

여태까지의 객체 변경 방지 메서드들은 모두 직속 프로퍼티만 변경이 가능하고, 중첩된 내부 객체까지는 영향을 주지 못한다.

내부 객체까지 동결하여 변경이 불가능한 읽기 전용의 객체를 구현하려면 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 `Object.freeze` 메서드를 호출해야 한다.
