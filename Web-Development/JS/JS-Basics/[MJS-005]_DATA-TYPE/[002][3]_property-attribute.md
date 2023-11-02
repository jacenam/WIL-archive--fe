# 프로퍼티 어트리뷰트

**Table of Contents**

- [내부 슬롯과 내부 메서드](#내부-슬롯과-내부-메서드)
- [프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체](#프로퍼티-어트리뷰트와-프로퍼티-디스크립터-객체)
  - [프로퍼티 어트리뷰트](#프로퍼티-어트리뷰트)
  - [프로퍼티 디스크립터 객체](#프로퍼티-디스크립터-객체)
- [데이터 프로퍼티와 접근자 프로퍼티](#데이터-프로퍼티와-접근자-프로퍼티)
  - [접근자 프로퍼티](#접근자-프로퍼티)
  - [getter 함수](#getter-함수)
  - [setter 함수](#setter-함수)
- [프로퍼티 정의](#프로퍼티-정의)
- [객체 변경 방지](#객체-변경-방지)

<br>

## 내부 슬롯과 내부 메서드

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

## 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

### 프로퍼티 어트리뷰트

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

### 프로퍼티 디스크립터 객체

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

## 데이터 프로퍼티와 접근자 프로퍼티

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

### 접근자 프로퍼티

데이터 프로퍼티는 현재까지 살펴본 객체의 일반적인 프로퍼티다. 바로 위에서 언급했듯이, 접근자 프로퍼티는 자체적인 값을 갖고 있지 않기 때문에 데이터 프로퍼티 키의 값을 읽어들이거나 새로운 값을 저장할 때 사용되는 함수 형태의 프로퍼티다

접근자 프로퍼티의 내부 슬롯과 프로퍼티 어트리뷰트는 아래와 같이 표현된다:

| 내부 슬롯          | 프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 키 |
| ------------------ | ------------------- | -------------------------------------- |
| `[[Get]]`          | get                 | `get`                                  |
| `[[Set]]`          | set                 | `set`                                  |
| `[[Enumerable]]`   | enumerable          | `enumerable`                           |
| `[[Configurable]]` | configurable        | `configurable`                         |

접근자 함수에 대한 이해를 위해 실제 예제를 살펴보자. 아래와 같이 하나의 객체에 데이터 프로퍼티와 접근자 프로퍼티가 존재할 수 있다. 이때, 접근자 프로퍼티(`get`, `set`)는 함수의 형태로 값과 직접적으로 맵핑되어 있지 않고, 데이터 프로퍼티의 값을 읽어들여 새로운 값을 반환한다. 이러한 접근자 프로퍼티의 함수는 `getter` 혹은 `setter` 함수라고도 부른다

```javascript
const user = {
  firstName: "Jace",
  lastName: "Nam",

  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
	
  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 객체 user의 참조
console.log(user); // → { firstName: 'Jace', lastName: 'Nam', fullName: [Getter/Setter] }
// 데이터 프로퍼티를 통한 프로퍼티 값의 참조
console.log(`${user.firstName} ${user.lastName}`); → // Jace Nam
```

객체 `user`의 프로퍼티 `firstName`과 `lastName`은 데이터 프로퍼티다. 따라서, 위 프로퍼티 어트리뷰트 파트에서 언급했듯이 `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`를 프로퍼티 어트리뷰트로서 갖는다

```javascript
const user = {
  firstName: "Jace",
  lastName: "Nam",

  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
	
  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 데이터 프로퍼티의 프로퍼티 디스크립터 객체의 참조
console.log(Object.getOwnPropertyDescriptor(user, "firstName")); // → { value: 'Jace', writable: true, enumerable: true, configurable: true } 
console.log(Object.getOwnPropertyDescriptor(user, "lastName"));
```

그러나 객체 `user`의 프로퍼티 `fullName`은 값이 맵핑되어 있지 않은 접근자 프로퍼티다. 따라서 `[[Get]]`, `[[Set]]`, `[[Enumerable]]`, `[[Configurable]]`를 프로퍼티 어트리뷰트로서 갖는다

```javascript
const user = {
  firstName: "Jace",
  lastName: "Nam",

  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
	
  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 접근자 프로퍼티의 프로퍼티 디스크립터 객체의 참조
console.log(Object.getOwnPropertyDescriptor(user, "fullName")) // → { get: f, set: f, enumerable: true, configurable: true } 
```

### getter 함수

접근자 프로퍼티는 `getter`와 `setter` 함수를 모두 정의할 수도 있고 아래와 같이 하나만 정의할 수도 있다.  `getter` 함수는 이름 그대로 '(값을) 가져오는 함수'라고 생각하면 된다. 접근자 프로퍼티의 이름을 사용하여 **마침표 프로퍼티 접근 연산자를 통해 객체에 접근하면 데이터 프로퍼티의 값을 반환하는 `getter` 함수가 호출**된다

```javascript
const user = {
  firstName: "Jace",
  lastName: "Nam",

  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
};

// 접근자 프로퍼티 키인 fullName을 통해 객체 user에 접근하면 getter 함수가 호출된다
console.log(person.fullName); // → Jace Nam
```

### setter 함수

`setter` 함수는 이름 그대로 '(값을) 설정하는 함수'라고 생각하면 된다. `setter` 함수는 `getter` 함수와는 다르게, `setter` 함수 호출 시 값을 `setter` 함수에 전달해야하기 때문에 최소 1개의 매개변수가 필수적으로 필요하다. **`setter` 함수에 값을 전달하면 `getter` 함수가 아닌 `setter` 함수가 호출**된다

```javascript
const user = {
  firstName: "Jace",
  lastName: "Nam",

  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  
  // setter 함수
  set fullName(name) {
		[this.firstName, this.lastName] = name.split(" ");
  },
};

// 접근자 프로퍼티 fullName에 값을 할당한다
// 접근자 프로퍼티 fullName에 값을 저장하면 getter 함수가 아닌 setter 함수가 호출된다
user.fullName = "Jace Nam";
console.log(user) // → { firstName: "Jace", lastName: "Nam" }
```

`setter` 함수를 호출하면 `user` 객체를 참조했을 때 프로퍼티 `firstName`, `lastName`에 대한 객체 `user`의 정보만을 확인할 수 있다. 그러나 브라우저 콘솔에서 위 코드 예제를 실행하면 아래와 같이 `setter` 함수에 의해 설정된 프로퍼티 `fullName`의 값을 확인할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ad486a2f-a87b-41bb-8706-638c53582e3f" width="100%">

<br>

## 프로퍼티 정의

프로퍼티 정의란 특정 객체에 새로운 프로퍼티를 추가할 때 프로퍼티 어트리뷰트를 개발자가 명시적으로 정의하거나 기존 프로퍼티 어트리뷰트를 재정의(수정)하는 것을 의미한다

> [앞서](#2-프로퍼티-어트리뷰트와-프로퍼티-디스크립터-객체) 살펴본 프로퍼티 어트리뷰트에 대한 내용은 객체 리터럴에 의한 생성 방식으로 객체를 생성했을 때 JS 엔진에 의해 디폴트로 실행되는 프로퍼티 정의 방식이다

`Object.defineProperty` 메서드를 사용하면 프로퍼티 어트리뷰트를 개발자가 명시적으로 정의할 수 있다. `Object.defineProperty`의 인수에는 객체 이름, 데이터 프로퍼티의 키 그리고 프로퍼티 디스크립터 객체를 전달해야한다

```javascript 
const user = {};

Object.defineProperty(user, "firstName", {
  value: "Jace",
  writable: true,
  enumerable: true,
  configurable: true,
});

console.log(Object.getOwnPropertyDescriptor(user, "firstName")); → { value: 'Jace', writable: true, enumerable: true, configurable: true }
```

이때 프로퍼티 디스크립터 객체의 프로퍼티를 누락시키면 `undefined` 혹은 `false`가 디폴트 값으로 정의된다

```javascript
const user = {};

Object.defineProperty(user, "firstName", {
  value: "Jace",
  writable: true,
  enumerable: true,
  configurable: true,
});



Object.defineProperty(user, "lastName", {});

Object.defineProperty(user, "fullName", {
  get () {},
});

console.log(Object.getOwnPropertyDescriptor(user, "lastName")); // → { value: undefined, writable: false, enumerable: false, configurable: false }

console.log(Object.getOwnPropertyDescriptor(user, "fullName")); // → { get: f, set: undefined, enumerable: false, configurable: false }
```

여기서 살펴볼 수 있듯이, `Object.defineProperty` 메서드로 프로퍼티를 정의할 때 누락된 프로퍼티 디스크립터 객체의 프로퍼티는 다음과 같이 디폴트 값으로 정의된다

| 디스크립터 객체의 프로퍼티 | 디스크립터 프로퍼티 생략 시 디폴트 값 |
| -------------------------- | ------------------------------------- |
| `value`                    | `undefined`                           |
| `get`                      | `undefined`                           |
| `set`                      | `undefined`                           |
| `writable`                 | `false`                               |
| `enumerable`               | `false`                               |
| `configurable`             | `false`                               |

특정 프로퍼티 어트리뷰트가 `false`인 경우 아래와 같은 부수효과가 있다: 

- `writable`: `false`인 경우

  ```javascript
  user.lastName = "Nam";
  
  // 에러와 프로퍼티 디스크립터 객체의 변경사항이 없다
  console.log(Object.getOwnPropertyDescriptor(user, "lastName")); // → { value: undefined, writable: false, enumerable: false, configurable: false }
  ```
  
- `enumerable`: `false`인 경우

  ```javascript
  // 프로퍼티 lastName, fullName에 대한 프로퍼티 열거가 불가능하다
  console.log(Object.keys(user)); // → ["firstName"]
  
  for (const property in user) {
    console.log(property); // → firstName
  }
  ```

- `configurable`: `false`인 경우

  ```javascript
  // 프로퍼티 삭제가 불가능하다
  delete user.lastName;
  delete user.fullName;
  
  console.log(Object.getOwnPropertyDescriptor(user, "lastName")); // → { value: undefined, writable: false, enumerable: false, configurable: false }
  console.log(Object.getOwnPropertyDescriptor(user, "fullName")); // → { get: f, set: undefined, enumerable: false, configurable: false }
  
  // 프로퍼티의 재정의가 불가능하다
  Object.defineProperty(user, "lastName", {
    value: "Nam",
    writable: true,
    enumerable: true,
    configurable: true,
  }); // → TypeError: Cannot redefine property: lastName
  ```

<br>

## 객체 변경 방지

[객체는 변경 가능한 값](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/primitive%20%26%20object%20type.md)이다. 프로퍼티를 추가, 삭제 및 갱신할 수 있으며 `Object.defineProperty`를 통해 프로퍼티의 프로퍼티 어트리뷰트도 재정의할 수도 있다. 이때 JS는 객체 값의 변경을 방지하는 다양한 메서드를 아래와 같이 제공하기도 한다:

> 아래 메서드들은 객체 변경을 금지하는 강도에 따라 구분된다

> 위 세 가지 객체 변경 방지 방법들은 모두 '얕은 변경 방지'로서 직속 프로퍼티의 변경만 방지되고 중첩 객체까지는 영향을 주지 못한다. 따라서 중첩 객체까지 모두 영향을 주는 깊은 객체 변경 방지 방법은 재귀적으로 사용되어야 한다. 이에 대해서는 [얕은 복사, 깊은 복사]()를 참고하자

| 메서드                     | 의미           | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 어트리뷰트 재정의 |
| -------------------------- | -------------- | ------------- | ------------- | ---------------- | ---------------- | ----------------- |
| `Object.preventExtensions` | 객체 확장 금지 | X             | O             | O                | O                | O                 |
| `Object.seal`              | 객체 밀봉      | X             | X             | O                | O                | X                 |
| `Object.freeze`            | 객체 동결      | X             | X             | O                | X                | X                 |

### 객체 확장 금지

먼저, `Object.isExtensible` 메서드를 통해 확장이 가능한 객체인지 확인해볼 수 있다

```javascript
const user = { 
  name: "Jace"
}

console.log(Object.isExtensible(user)); // → true
```

`Object.preventExtensions` 메서드를 통해 객체의 확장(프로퍼티 추가)을 금지시킬 수 있다

```javascript
const user = { 
  name: "Jace"
}

Object.preventExtensions(user);
console.log(Object.isExtensible(user)); // → false

// 동적 프로퍼티 추가 및 프로퍼티 정의에 의한 프로퍼티 추가 모두 금지된다
user.age = 31;
// 에러 없이 프로퍼티 추가가 이뤄지지 않는다
console.log(user); // → { name: "Jace" }
```

다만, `Object.preventExtensions` 메서드는 객체의 확장만 금지하기 때문에 프로퍼티 삭제는 가능하다

```javascript
const user = { 
  name: "Jace"
}

Object.preventExtensions(user);
console.log(Object.isExtensible(user)); // → false

user.age = 31;
delete user.name;
console.log(user); // → {}
```

### 객체 밀봉

먼저, `Object.isSealed` 메서드를 통해 밀봉된 객체인지 확인해볼 수 있다

```javascript
const user = { 
  name: "Jace"
}

console.log(Object.isSealed(user)); // → false
```

객체 밀봉이란 프로퍼티 추가, 삭제와 프로퍼티 어트리뷰트의 재정의를 금지한다. 밀봉된 객체는 읽기와 쓰기만 가능해진다

```javascript
const user = { 
  name: "Jace"
}

Object.seal(user);

user.age = 31; 
delete user.name; 

console.log(user); // → { name: "Jace" }
```

다만, 객체가 밀봉되더라도 객체의 기존 프로퍼티 값을 갱신하는 것은 가능하다

```javascript
const user = { 
  name: "Jace"
}

user.name = "Ju Hyung"
console.log(user); // → { name: "Ju Hyung" }
```

### 객체 동결

먼저, `Object.isFrozen` 메서드를 통해 동결된 객체인지 확인해 볼 수 있다

```javascript
const user = {
  name: "Jace"
}

console.log(Object.isFrozen(user)); // → false
```

객체 동결은 객체 값에 가장 높은 변경 금지 강도를 부여한다. 프로퍼티 추가, 삭제, 갱신과 프로퍼티 어트리뷰트의 재정의가 불가능하며 읽기만 가능하다

```javascript
const user = {
  name: "Jace"
}

Object.freeze(user);

user.age = 31; 
user.name = "Ju Hyung"
delete user.name; 

console.log(user); // → { name: "Jace" }
```

<br>

## 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)

