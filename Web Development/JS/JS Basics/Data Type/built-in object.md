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

이는 원시 값을 일시적으로 객체 타입으로서 활용하는 경우가 필요하기 때문이다. 아래 예제를 살펴보자. 문자열은 원시 값으로 객체가 아니기 때문에 프로퍼티나 메서드를 가질 수 없다. 하지만 마치 객체처럼 문자열 뒤에 마침표 프로퍼티 접근 연산자(`.`)를 붙여 사용할 수 있게 된다

```javascript
const str = "string";

// 문자열의 길이
console.log(str.length); // → 6

// 문자열을 모두 대문자화하는 메서드 
console.log(str.toUpperCase()); // → STRING
```



원시 값은 불변 값으로 값을 변경할 수는 없다

여권 번호 입력하는 경우: 입력된 모든 알파벳을 영어 대문자로 변환. 
