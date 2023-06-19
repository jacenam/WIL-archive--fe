# 프로퍼티 어트리뷰트

### 목차

- [1 내부 슬롯과 내부 메서드](#1-내부-슬롯과-내부-메서드)
- [2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체](#2-프로퍼티-어트리뷰트와-프로퍼티-디스크립터-객체)
  - [2-1 프로퍼티 어트리뷰트](#2-1-프로퍼티-어트리뷰트)
  - [2-2 프로퍼티 디스크립터 객체](#2-2-프로퍼티-디스크립터-객체)
- [3 데이터 프로퍼티와 접근자 프로퍼티](#3-데이터-프로퍼티와-접근자-프로퍼티)



<br>

## 1 내부 슬롯과 내부 메서드

프로퍼티 어트리뷰트를 이해하기 앞서 먼저 내부 슬롯(Internal Slot)과 내부 메서드(Internal Method)의 개념에 대한 이해가 필요하다

JS에서는 객체가 생성될 때 JS 엔진 내부의 구현 알고리즘이 동작하여 객체의 프로퍼티를 생성하게 된다. 이러한 JS 엔진의 객체 생성 동작에 대한 설명을 인간이 이해할 수 있는 언어인 '의사(Pseudo)'로 [ECMAScript]()에 정의한 내용이 바로 내부 슬롯과 내부 메서드다

> 의사(Pseudo)란 컴퓨터 프로그램이나 알고리즘이 수행해야 할 명령과 내용들을 인간이 사용하는 언어로 간략히 서술하는 것을 의미한다. 의사 코드에 대한 상세 내용은 [여기](https://medium.com/djangogirlsseoul-codecamp/%EC%9D%98%EC%82%AC%EC%BD%94%EB%93%9C-pseudo-code-%EB%9E%80-d892a3479b1d)를 참고하자

ECMAScript에서는 내부 슬롯과 내부 메서드를 가리키기 위해 내부 슬롯과 내부 메서드의 이름들을 이중 대괄호(`[[...]]`)로 감싸는 의사 형태로 표현되어 있다

```javascript
// 예제
[[getPrototypeOf]]
[[SetPrototypeOf]]
[[IsExtensible]]
[[Prototype]]
...
```

내부 슬롯과 내부 메서드는 JS 엔진의 객체 및 프로퍼티 생성에 대한 내부 로직이므로 개발자가 직접 접근하고 제어할 수 없다. 즉, 내부 슬롯과 내부 메서드에 대한 직접적인 접근이나 호출 방법이 없다. 단, 일부 내부 슬롯과 내부 메서드에 한해서 개발자가 간접적으로 접근하여 객체 정보를 살펴볼 수 있는 수단이 있다

> 내부 슬롯과 내부 메서드에 대한 상세 정보(종류, 이름 등)를 알 필요는 없다. JS 엔진의 객체 및 프로퍼티 생성에 대한 내부 로직이 어떻게 이뤄지는지만 숙지하면 된다

대표적인 수단으로 `[[Prototype]]` 내부 슬롯이 있다. 모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 갖으며, 이는 `__proto__`를 통해 간접적으로 접근해 `[[Prototype]]` 내부 슬롯에 대한 정보를 살펴볼 수 있다

```javascript
const obj = {}; 

console.log(obj.__proto__); // → [Object: null prototype] {}
```

<br>

## 2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

### 2-1 프로퍼티 어트리뷰트

프로퍼티 어트리뷰트(Property Attritube)는 단어 의미 그대로 "[객체 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#3-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C)가 가지는 속성"이라는 뜻으로, JS 엔진에 의해 객체의 프로퍼티가 생성될 때 프로퍼티의 상태를 나타내는 속성 값이라 이해하면 된다. 프로퍼티 어트리뷰트에는 아래와 같은 기본값을 갖으며 이는 모두 내부 슬롯의 일종이다

- **value:** 프로퍼티의 값, `[[Value]]`
- **writable:** 값의 갱신 가능 여부, `[[Writable]]`
- **enumerable:** 값의 열거 가능 여부, `[[Enumerable]]`
- **configurable:** 값의 재정의 가능 여부,  `[[Configurable]]`

앞서 언급했듯이 내부 슬롯은 원래 개발자가 직접 접근하고 제어할 수 없지만, 프로퍼티 어트리뷰트는 `[[Prototype]]` 내부 슬롯과 동일하게 간접적인 접근이 가능하다. 프로퍼티 어트리뷰트에는 아래와 같이  `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 접근할 수 있다:

```javascript
const user = {
  name: "Jace", 
  age: 31
};

console.log(Object.getOwnPropertyDescriptor(user, "name")); // → { value: 'Jace', writable: true, enumerable: true, configurable: true }
console.log(Object.getOwnPropertyDescriptor(user, "age")); // → { value: 31, writable: true, enumerable: true, configurable: true }
```

### 2-2 프로퍼티 디스크립터 객체

바로 위 `Object.getOwnPropertyDescriptor` 메서드 이름의 뜻은 '객체의 프로퍼티 디스크립터(Property Descriptor)를 가져온다'라는 의미다. `Object.getOwnPropertyDescriptor` 메서드를 호출하면 프로퍼티 어트리뷰트 정보가 기술되어 있는 프로퍼티 디스크립터 객체를 반환한다

`Object.getOwnPropertyDescriptor` 메서드를 호출할 때 첫 번째 매개변수에 대상 객체를 지정하고, 두 번째 매개변수에는 프로퍼티 키를 문자열 형태로 지정해야 한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/374ece55-7ca9-4275-881f-db5a0dc19e95" width="100%">

`Object.getOwnPropertyDescriptor` 메서드는 하나의 프로퍼티에 대한 프로퍼티 디스크립터 객체를 반환하지만 ES8(2017)에서 도입된 `Object.getOwnPropertyDescriptors` 메서드를 사용하면 모든 프로퍼티의 프로퍼티 어트리뷰트 정보가 기술되어 있는 프로퍼티 디스크립터 객체를 반환한다

```javascript
const user = {
  name: "Jace",
  age: 31,
};

console.log(Object.getOwnPropertyDescriptors(user));
// → { name: { value: 'Jace', writable: true, enumerable: true, configurable: true }, age: { value: 31, writable: true, enumerable: true, configurable: true } }
```

쌍의 키와 값으로 이루어진 프로퍼티 디스크립터 객체의 프로퍼티는 각각 `value: "Jace"`, `writable: true`, `enumerable: true`, `configurable: true`이다

<br>

## 3 데이터 프로퍼티와 접근자 프로퍼티

객체의 프로퍼티는 아래와 같이 두 가지 형태로 구분된다:

- 데이터 프로퍼티: 키와 값으로 구성된 일반적인 프로퍼티다. 객체의 일반적인 프로퍼티는 데이터 프로퍼티다

  ```javascript
  const user = {
    // 키(프로퍼티 키)와 값(프로퍼티 값)으로 구성된 프로퍼티 
    name: "Jace",
    age: 31
  }
  ```

- 접근자 프로퍼티(Accessor Property): 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수(Accessor Function)으로 구성된 프로퍼티다. 즉, 접근자 함수 형태의 키는 존재하지만, 자체적인 값은 갖지 않는 프로퍼티의 형태다

데이터 프로퍼티는 현재까지 살펴본 객체의 일반적인 프로퍼티다. 바로 위에서 언급했듯이, 접근자 프로퍼티는 자체적인 값을 갖고 있지 않기 때문에 데이터 프로퍼티 키의 값을 읽어들이거나 새로운 값을 저장할 때 사용되는 함수 형태의 프로퍼티다

접근자 프로퍼티의 내부 슬롯과 프로퍼티 어트리뷰트는 아래와 같이 표현된다:

| 내부 슬롯          | 프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 키 |
| ------------------ | ------------------- | -------------------------------------- |
| `[[Get]]`          | get                 | `get`                                  |
| `[[Set]]`          | set                 | `set`                                  |
| `[[Enumerable]]`   | enumerable          | `enumerable`                           |
| `[[Configurable]]` | configurable        | `configurable`                         |

접근자 함수에 대한 이해를 위해 실제 예제를 살펴보자

```javascript
const user = {
  firstName: "Jace",
  lastName: "Nam",
  age: 31,

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
	
  // set 접근자 함수는 매개변수가 최소 1개가 필수적으로 필요하다
  set fullName(name) {
    [this.firstName, this.lastName] = name.split("");
  },
};

console.log(`${user.firstName} ${user.lastName}`);
console.log(user.fullName);
console.log(user);
```





