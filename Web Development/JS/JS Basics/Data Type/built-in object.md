# 빌트인 객체

### 목차

- [1 표준 빌트인 객체](#1-표준-빌트인-객체)
- [2 원시 값과 래퍼 객체](#2-원시-값과-래퍼-객체)
- [3 전역 객체](#3-전역-객체)

<br>

## 1 표준 빌트인 객체

JS의 객체는 크게 3개의 객체로 분류된다:

- 표준 빌트인 객체
- 호스트 객체: ECMAScript 사양에 정의되어 있지 않지만 JS 실행 환경인 브라우저 또는 Node.js 환경에서 추가로 기본 제공되는 객체를 의미한다
- 사용자 정의 객체: 기본 제공되는 객체가 아닌 개발자가 객체 리터럴 등의 방식으로 직접 정의한 객체를 의미한다

여기서 자세히 살펴볼 객체는 표준 빌트인 객체(Standard Built-in Object or Native Objects)로서, ECMAScript 사양에 정의된 객체다. JS 실행 환경인 브라우저 또는 Node.js 환경과 관계 없이 언제나 사용할 수 있는 객체다. 전역 객체의 프로퍼티로서 별도의 선언 없이 언제나 참조할 수 있다. 곰곰히 돌이켜보면 빌트인 객체는 따로 선언 없이 언제나 참조하여 사용할 수 있었다

```javascript
// 별도의 선언 없이 언제나 참조 가능하다
const obj = new Object();
const arr = new Array(1, 2, 3);
```

JS는 `Object`, `String`, `Number` 등 40여 개의 표준 빌트인 객체가 제공되며 `Math`, `Reflect`, `JSON`을 제외하면 모두 인스턴스를 생성할 수 있는 생성자 함수 객체이기도 하다. 예를 들어 `String`, `Number`, `Boolean`, `Function`, `Array`, `Date` 생성자 함수를 호출하여 생성자 함수에 의한 인스턴스를 생성할 수 있다

```javascript
const strObj = new String("Lee");
const numObj = new Number(123); 
const boolObj = new Boolean(true);
const func = new Function("x", "return x+1");
const arr = new Array(1, 2, 3);
const regExp = new RegExp(/ab+c/i);
const date = new Date(); 
```

앞서 [프로토타입]() 파트에서 언급했듯이, 표준 빌트인 객체를 통해 생성된 인스턴스의 `prototype` 객체는 표준 빌트인 객체의 `prototype` 프로퍼티이기도 하다

```javascript
const strObj = new String("Lee");
console.log(strObj); // → String { "Lee" }

// 표준 빌트인 객체인 String 생성자 함수에 의해 생성된 인스턴스의 프로토타입은 String 생성자 함수의 프로토타입 프로퍼티이기도 하다
console.log(strObj.__proto__ === String.prototype); // → true
```

그리고 `prototype` 객체가 자동 소유하게 되는 다양한 빌트인 메서드들을 인스턴스가 상속받아 사용할 수 있게 되기도 한다

```javascript
const numObj = new Number(123);
console.log(numObj); // → Number { 123 }

// 표준 빌트인 객체인 Number 생성자 함수에 의해 생성된 인스턴스는 Number.prototype의 빌트인 메서드를 사용할 수 있다
console.log(numObj.hasOwnProperty("Jace")); // → false
```

또한 표준 빌트인 객체는 인스턴스 없이 [정적 메서드](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#2-3-%EC%A0%95%EC%A0%81-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80-%EB%A9%94%EC%84%9C%EB%93%9C)를 사용할 수도 있다

```javascript
const boolObj = new Boolean(true);
console.log(boolObj); // → Boolean { true }

Boolean.staticMethod = function() {
  console.log("this is a static method");
}

Boolean.staticMethod(); // → this is a static method
boolObj.staticMethod(); // → Uncaught TypeError: boolObj.staticMethod is not a function
```

<br>

## 2 원시 값과 래퍼 객체

JS에서 문자열, 숫자, 불리언 등의 원시 값이 존재하는데도 원시 값 형태의 문자열, 숫자, 불리언을 객체로 생성하는 `String`, `Number`, `Boolean` 등의 표준 빌트인 객체(생성자 함수)가 필요한 이유가 무엇일까?

이는 원시 값을 일시적으로 객체 타입으로서 활용하는 경우가 필요하기 때문이다. 아래 예제를 살펴보자. 문자열은 원시 값으로 객체가 아니기 때문에 프로퍼티나 메서드를 가질 수 없다. 하지만 마치 객체처럼 문자열 뒤에 마침표 프로퍼티 접근 연산자(`.`)를 붙여 사용할 수 있게 된다. 그리고 메서드(e.g. `toUpperCase`, `toFixed`)에 의해 원시 값이 변한 것처럼 보이기도 한다

```javascript
const str = "string";

// 문자열의 길이
console.log(str.length); // → 6
// 문자열을 모두 대문자화하는 메서드 
console.log(str.toUpperCase()); // → STRING

const num = 1.5; 

// 숫자 값을 반올림하여 정수로 변환하는 메서드
console.log(num.toFixed()); // → 2
```

이는 일부 원시 값(문자열, 숫자, 불리언)의 경우, 마침표 프로퍼티 접근 연산자를 붙여 빌트인 메서드를 사용하게 되면 JS 엔진은 원시 값을 일시적으로 객체로 변환해주기 때문이다. 그리고 일시적으로 변환된 객체 값은 쓰임을 다하면 가비지 콜렉터에 의해 메모리에서 삭제되며, 원시 값은 변하지 않고 유지된다

> 원시 값은 불변 값이므로 값이 변경될 수 없다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/cbd1948f-3b5d-4240-b661-9078b9c36d10" width="100%">

```javascript
const str = "string";

// 문자열을 모두 대문자화하는 메서드 
console.log(str.toUpperCase()); // → STRING

// 원시 값은 불변 값이기 때문에 변경되지 않는다
console.log(str); // → string
```

이처럼 문자열, 숫자, 불리언 값을 객체처럼 접근하여 일시적으로 생성되는 임시 객체를 래퍼 객체(Wrapper Object)라 부른다. JS 엔진은 이러한 임시 객체인 래퍼 객체를 생성자 함수를 통해 생성하기 때문에, 래퍼 객체는 인스턴스이며 `생성자 함수.prototype` 객체의 프로퍼티를 상속받아 사용할 수 있게 된다. 위 문자열의 래퍼 객체 예제의 경우, 생성된 문자열은 래퍼 객체의 `[[StringData]]` 내부 슬롯에 저장되며 프로퍼티는 배열과 같이 인덱스의 형태를 가진다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/61911142-9e91-46e5-8cf7-a7633e693c9e" width="100%">

앞서 일시적으로 생성된 래퍼 객체(인스턴스)의 값은 쓰임을 다하면 가비지 콜렉터의 의해 메모리에서 바로 삭제된다고 했다. 그러나 래퍼 객체의 값을 변수에 할당하면 메모리에 유지되어 재사용이 가능하다

```javascript
const str = "string"; 

const wrapperObj = str.toUpperCase(); 
console.log(wrapperObj); // → STRING

console.log(str); // → string
```

<br>

## 3 전역 객체

전역 객체(Global Object)란 JS 엔진에 의해 그 어떤 객체보다도 먼저 생성되는 특수한 객체다. 그리고 전역 객체는 어떤 객체에도 속하지 않는 최상위 객체다. 브라우저 환경에서는 `window`, `self`, `this`, `frames`, Node.js 환경에서는 `global`이 전역 객체를 가리킨다

```javascript
// 브라우저 환경
console.log(window); // → Window {window: Window, self: Window, document: document, name: '', location: Location, …}

console.log(self)l // → Window {window: Window, self: Window, document: document, name: '', location: Location, …}

// Node.js 환경
console.log(global); 
/* 
	→ Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
	...
	}
*/
```

ECMAScript 11(ES11, 2020) 사양에서 도입된 `globalThis`는 브라우저 환경과 Node.js 환경에서 전역 객체를 가리키는 통일된 식별자다

```javascript
// 브라우저 환경
console.log(globalThis); // → Window {window: Window, self: Window, document: document, name: '', location: Location, …}

// Node.js 환경
console.log(globalThis); 
/* 
	→ Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
	...
	}
*/
```

