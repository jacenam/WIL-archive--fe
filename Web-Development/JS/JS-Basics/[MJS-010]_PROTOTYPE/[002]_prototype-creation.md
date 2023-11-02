# 프로토타입의 생성 시점

**Table of Contents**

- [프로토타입의 생성 시점](#프로토타입의-생성-시점)
  - [사용자 정의 생성자 함수와 프로토타입 생성 시점](#사용자-정의-생성자-함수와-프로토타입-생성-시점)
  - [빌트인 생성자 함수와 프로토타입 생성 시점](#빌트인-생성자-함수와-프로토타입-생성-시점)
- [객체 생성 방식과 프로토타입의 결정](#객체-생성-방식과-프로토타입의-결정)

<br>

## 프로토타입의 생성 시점

생성자 함수와 `prototype` 객체는 언제나 쌍으로 존재한다. 따라서 `prototype` 객체는 생성자 함수가 생성되는 시점에 함께 생성된다

생성자 함수는 개발자가 직접 정의한 '사용자 정의 생성자 함수'와 JS에서 내장되어 기본 제공하는 '빌트인 생성자 함수'로 구분된다. 그리고 `prototype` 객체는 생성자 함수 종류에 따라 생성 시점이 상이한다

### 사용자 정의 생성자 함수와 프로토타입 생성 시점

[내부 메서드 `[[Construct]]`를 갖는 함수 객체](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/constructor%20function.md#6-1-call%EA%B3%BC-construct), 즉 함수 선언문, 함수표현식과 같이 일반 함수 정의 방식으로 정의한 함수 객체만이 `new` 연산자와 함께 생성자 함수로서 사용될 수 있다

JS 엔진에 의해 생성자 함수는 평가되어 함수 객체를 생성하는데, 바로 이 시점에 프로토타입이 함께 생성된다

```javascript
function User(name) {
  this.name = name;
}

console.log(User.hasOwnProperty("prototype")); // → true
```

[함수 생성 시점과 함수 호이스팅](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#4-2-%ED%95%A8%EC%88%98-%EC%83%9D%EC%84%B1-%EC%8B%9C%EC%A0%90%EA%B3%BC-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)에서 언급했듯이, 함수 선언문은 코드가 실행되는 시점인 런타임 이전에 JS 엔진에 의해 먼저 평가되어 실행된다. 함수 선언문으로 정의된 `User` 생성자 함수는 호이스팅되어 런타임 이전에 실행되어 함수 객체로서 평가된다. 따라서, 바로 위에서 언급했듯이, `prototype` 객체도 이 시점에 생성되어 `User` 생성자 함수의 프로퍼티가 된다

```javascript
// 참조문이 함수 선언문보다 위에 위치해도 호이스팅에 의해 함수 객체 및 프로토타입 객체가 생성된다
console.log(User.hasOwnProperty("prototype")); // → true

function User(name) {
  this.name = name;
}
```

`User` 생성자 함수와 함께 생성된 `prototype` 객체의 내부를 브라우저 환경에서 살펴보면 아래와 같다:

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b16e4fb2-1205-45dc-a71d-a80c6d58e7f1" width="100%">

`User` 생성자 함수의 `prototype` 프로퍼티인 `prototype` 객체는 `constructor` 프로퍼티와 `[[Prototype]]` 내부 슬롯을 갖고 있는 것을 확인할 수 있다. 그리고 `[[Prototype]]` 내부 슬롯의 참조값은 `Object` 생성자 함수를 가리치고 있다

앞서 계속해서 언급했듯이, 모든 객체는 `[[Prototype]]` 내부 슬롯을 갖는다 했다. `prototype`도 객체이기 때문에 `[[Prototype]]` 내부 슬롯을 갖는다. 다시 말해,  `User` 생성자 함수와 함께 생성된 `prototype` 객체는 또 하나의 `prototype` 객체와 연결되어 있다는 의미다

아래 예시와 같이, `__proto__` 프로퍼티 접근자를 통해 `prototype` 객체와 연결된 또 하나의 `prototype` 객체에 접근할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/2a599970-7d16-4f62-9e44-95687adb1093" width="100%">

`User` 생성자 함수에 의해 생성된 인스턴스가 없기 때문에, `User` 생성자 함수와 함께 생성된 `prototype` 객체는 `constructor` 프로퍼티만을 갖는다. 그리고 `prototype` 객체와 연결된 또 하나의 `prototype` 객체는 `Object.prototype`다. `prototype` 객체는 `Object.Prototype`의  `__proto__` 접근자 프로퍼티를 상속받아 `Object.prototype` 객체에 접근할 수 있게 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/4b5d5a43-44d5-4d9f-9c48-622e28f5fc83" width="100%">

```javascript
function User(name) {
  this.name = name;
}

User.prototype.__proto__ === Object.prototype; // → true
```

이렇게 여러 개의 `prototype` 객체가 생성되고 연결되는 것에 혼란스러울 수 있다. 이는 [프로토타입 체인](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype%20chain.md) 파트에서 살펴볼 예정이다

### 빌트인 생성자 함수와 프로토타입 생성 시점

앞서 일반 함수 정의 방식으로 정의한 생성자 함수의 `prototype` 객체는 생성자 함수의 생성 시점과 동일하다 했다. `Object`, `String`, `Number`, `Function`, `Array` 등의 빌트인 생성자 함수의 `prototype` 객체도 빌트인 생성자 함수가 생성되는 시점에 생성된다

다만, 빌트인 생성자 함수의 생성 시점은 전역 객체가 생성되는 시점이다. 따라서 빌트인 생성자 함수와 함께 생성되는 `prototype`은 전역 객체가 생성되는 시점과 같다

> 전역 객체(Global Object)란 소스 코드가 실행되기 이전에 JS 엔진에 의해 생성되는 최상위의 특수 객체다. 전역 객체는 브라우저 환경에서는 `window` 객체, Node.js 환경에서는 `global` 객체를 의미한다.
>
> 전역 객체는 단 한 개로, JS의 모든 객체와 값들은 이 전역 객체의 바인딩된다. 다시 말해, JS의 모든 객체와 값들은 전역 객체의 프로퍼티가 된다 
>
> ```javascript
> function sayHello() {
>   console.log("Hello");
> }
> 
> sayHello(); // → Hello
> window.sayHello(); // → Hello
> ```
>
> 앞서 언급한 빌트인 생성자 함수는 모두 전역 객체의 프로퍼티다
>
> ```javascript
> // Object 빌트인 생성자 함수
> console.log(Object); // → ƒ Object() { [native code] }
> 
> // Object 빌트인 생성자 함수는 전역 객체의 프로퍼티다
> window.Object === Object; // → true
> ```
>
> 전역 객체에 대한 상세 내용은 [여기]()를 참고하자

<br>

## 객체 생성 방식과 프로토타입의 결정

앞서 객체 리터럴 방식에 의해 생성된 객체는 따로 인스턴스를 생성하지 않기 때문에 `prototype` 객체와 연결되려면 [가상의 생성자 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-2-constructor-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0feat-%EA%B0%9D%EC%B2%B4-%EB%A6%AC%ED%84%B0%EB%9F%B4)와 연결된다고 했다. 그리고 객체 리터럴 방식에 의해 생성된 객체는 가상의 생성자 함수의 인스턴스 역할을 한다

```javascript
const user = { name: "Jace" };

// 객체 리터럴 방식의 의해 생성된 객체, 가상의 생성자 함수, 프로토타입 객체는 모두 연결된다
// 프로토타입 객체의 프로퍼티 중 하나인 constructor 프로퍼티를 객체가 사용할 수 있게 된다
console.log(user.constructor); // → ƒ Object() { [native code] }
```

따라서, 객체 리터럴이 JS 엔진에 의해 평가되어 객체 값을 생성할 때 가상의 `Object` 생성자 함수도 생성되기 때문에 객체 리터릴 방식에 의해 객체가 생성될 때 `prototype` 객체도 생성된다

객체를 생성하는 가장 대표적인 방식인 객체 리터럴, `Object` 생성자 함수, 생성자 함수 등 가상의 생성자 함수가 필요한 방식이든, 생성자 함수가 존재하는 방식이든 `prototype` 객체는 생성자 함수가 생성되는 시점에 더불어 생성된다는 것을 알 수 있다

<br>

## 참고

- [모던 자바스크립트 Deep Dive]()