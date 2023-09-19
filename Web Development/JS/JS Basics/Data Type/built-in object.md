# 빌트인 객체

### 목차

- [1 표준 빌트인 객체](#1-표준-빌트인-객체)
- [2 원시 값과 래퍼 객체](#2-원시-값과-래퍼-객체)
- [3 전역 객체](#3-전역-객체)
  - [3-1 빌트인 전역 프로퍼티](#3-1-빌트인-전역-프로퍼티)
  - [3-2 빌트인 전역 함수(메서드)](#3-2-빌트인-전역-함수(메서드))
    - [3-2-1 eval 함수](#3-2-1-eval-함수)
    - [3-2-2 isInfinite 함수](#3-2-2-isInfinite-함수)
    - [3-2-3 isNaN 함수](#3-2-3-isNaN-함수)
    - [3-2-4 parseFloat 함수](#3-2-4-parseFloat-함수)
    - [3-2-5 parseInt 함수](#3-2-5-parseInt-함수)
    - [3-2-6 encodeURI와 decodeURI 함수](#3-2-6-encodeURI와-decodeURI-함수)

- [4 암묵적 전역](#4-암묵적-전역)

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

전역 객체(Global Object)란 JS 엔진에 의해 그 어떤 객체보다도 먼저 생성되는 특수한 객체다. 그리고 전역 객체는 어떤 객체에도 속하지 않는 최상위 객체다. 브라우저 환경에서는 `window`, `self`, `this`, `frames`, `Node.js` 환경에서는 `global`이 전역 객체를 가리킨다

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

ECMAScript 11(ES11, 2020) 사양에서 도입된 `globalThis`는 브라우저 환경과 `Node.js` 환경에서 전역 객체를 가리키는 통일된 식별자다

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

전역 객체는 앞서 언급한 JS의 3가지 객체 표준 빌트인 객체, 호스트 객체, `var` 키워드로 선언한 사용자 정의 변수와 전역 함수를 프로퍼티로 갖는다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/1e25a083-c5ba-4ad6-ad4d-8f89215a541d" width="100%">

아래 예제와 같이 `var`  키워드로 전역 변수 혹은 전역 함수를 선언하면 전역 객체의 프로퍼티로서 등록된다

```javascript
var globalVariable = "property of global object";
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/af52ec83-2e6a-40d8-8ea6-a04d8a3b9ad5" width="100%">

전역 객체는 최상위 객체다. 그러나 프로토타입 체인의 상속 관계상에서 최상위 객체라는 것을 의미하는 것은 아니다. 이는 전역 객체가 어떤 객체의 프로퍼티가 아니고 JS의 객체를 프로퍼티로 소유하는 최상위 객체라는 의미다

전역 객체의 특징은 다음과 같다: 

- 전역 객체는 개발자가 의도적으로 생성할 수 없다

- 전역 객체의 프로퍼티(표준 빌트인 객체, 호스트 객체 등) 참조할 때 `window`, `global`, `globalThis` 등의 식별자를 생략할 수 있다

  ```javascript
  // 지정한 문자열 혹은 객체를 경고창을 띄우는 alert 빌트인 메서드
  window.alert("Hello World"); 
  
  // window 식별자 없이 참조 가능하다
  alert("Hello World")
  ```

- 전역 객체는 표준 빌트인 객체(`Object`, `String`, `Number`, `Boolean` 등)를 프로퍼티로 가진다

- 전역 객체는 브라우저 환경과 `Node.js` 환경에 따라 추가적인 프로퍼티와 메서드인 호스트 객체를 프로퍼티로 가진다

- 전역 객체는 `var` 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 가진다

- 전역 객체는 `let` 혹은 `const` 키워드로 선언한 전역 변수는 프로퍼티로 가지지 않는다. 이는 `let` 혹은 `const` 키워드로 선언한 변수가 [전역 렉시컬 환경]() 내에 존재하기 때문이다

  ```javascript
  let variable1 = "variable 1";
  const variable2 = "variable 2";
  
  console.log(window.variable1); // → undefined
  console.log(window.variable2); // → undefined
  ```

- 브라우저 환경에서 모든 JS 코드는 하나의 전역 객체를 공유한다. 즉, 여러 개의 `script` 태그를 통해 JS 코드를 분리해도 하나의 전역 객체 `window`를 공유한다는 의미다. 이를 통해 JS 코드 파일을 여러 개로 분리해도 하나의 전체 코드로서 활용이 가능해진다

### 3-1 빌트인 전역 프로퍼티

빌트인 전역 프로퍼티(Built-in Global Property)란 전역 객체의 프로퍼티를 뜻한다. 

- `Infinity` 전역 프로퍼티: 무한대를 나타내는 숫자값 `Infinity`를 프로퍼티 값으로 갖는다. `Infinity`는 양의 무한대 값을, `-Infinity`는 음의 무한대 값을 나타낸다. 

  ```javascript
  // 양의 무한대
  console.log(1/0); // → Infinity
  console.log(-1/0); // → -Infinity
  
  // 앞서 언급했듯이 Infinity는 숫자값이다
  console.log(typeof Infinity); // → number
  ```

- `NaN` 전역 프로퍼티: 숫자가 아님(Not-a-Number)을 나타내는 숫자값 `NaN`을 프로퍼티 값으로 갖는다. `NaN` 프로퍼티는 전역 객체의 `Number.NaN` 프로퍼티와 같다

  ```javascript
  console.log(Number("abc")); // → NaN
  console.log("string" * 123); // → NaN
  
  // 앞서 언급했듯이 NaN은 숫자값이다
  console.log(typeof NaN); // → number
  ```

- `undefined` 프로퍼티: 정의되지 않은 값을 나타내는 `undefined`를 프로퍼티 값으로 갖는다

  ```javascript
  let a; 
  console.log(a); // → undefined
  
  // undefined는 undefined 원시값이다
  console.log(typeof undefined); // → undefined
  ```

### 3-2 빌트인 전역 함수(메서드)

빌트인 전역 함수(Built-in Global Function)는 전역에서 호출 가능한 빌트인 함수로서 전역 객체의 메서드이기 때문에 빌트인 전역 메서드라고도 부른다

### 3-2-1 eval 함수

`eval` 함수는 문자열을 인수(Argument)로서 전달받으며, 이 문자열은 JS 코드를 문자열 형태로 나타낸 것이다

```javascript
// JS 코드를 나타내는 문자열을 인수로 전달받는다
eval("const foo = 10; console.log(foo);"); // → 10
```

`eval` 함수는 인수로 전달받은 문자열이 [표현식](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/expression%20%26%20statement(feat.%20value%2C%20literal).md)으로 평가되는 JS 코드라면 인수를 런타임에 평가해서 값을 생성한다

```javascript
// 표현식인 문
eval("1 + 1 + 1"); // → 3
```

전달받은 인수가 표현식이 아닌 문이라면 인수를 런타임에 실행한다

```javascript
// 표현식이 아닌 문
eval("const x = 5"); // → undefined
```

대표적인 표현식인 문은 변수 선언문이다. 변수 선언문은 [호이스팅](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/variable.md#6-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)되어 소스 코드가 실행되는 런타임 이전에 실행된다. 그러나 아래 예제처럼 `eval` 함수에 인수로 전달된 변수 선언문은 호이스팅되지 않고 소스 코드가 순차적으로 실행되는 런타임에 실행되는 것이다 

```javascript
// 인수로 전달된 변수 선언문은 호이스팅되지 않는다
console.log(x); // → Uncaught ReferenceError: x is not defined

// 표현식이 아닌 문(변수 선언문)
eval("const x = 5;");

// eval 함수에 전달된 인수는 런타임에 실행된다
console.log(x); // → 5
```

인수로 전달된 문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행한다. 그리고 마지막 결과 값을 반환한다 

```javascript
// 표현식이 아닌 문으로만 이루어진 문자열 코드
eval("var x = 5; var y = 10; var z = 15;");
console.log(x, y, z); // → 5 10 15

// 표현식인 문으로 이루어진 문자열 코드
eval("1+1; 1+2; 1+3;"); // → 4
```

객체 리터럴과 함수 리터럴은 괄호(`()`)로 감싸서 인수로 전달해야 된다

```javascript
const obj = eval("({ a: 1, b: 2 })");
console.log(obj); // → { a: 1, b: 2 }

const func = eval("( function() {return 1; })");
console.log(func()); // → 1
```

함수는 원래 런타임 이전에 함수 몸체 내부의 모든 선언문을 실행하고 결과를 스코프에 등록한다. 따라서 아래 예제와 같이 `var x = 2;` 변수 선언문은 호이스팅되어 변수 선언이 먼저 이뤄진다. 따라서 `foo` 함수 몸체 내부의 `x` 참조문에서 에러가 발생하지 않는다

```javascript
const x = 1; 

function foo() {
  // 변수 선언문이 호이스팅되어 x에 대한 참조문에서 에러가 발생하지 않는다
  console.log(x); // → undefined
  var x = 2; 
  console.log(x); // → 2
}

foo();
console.log(x); // → 1
```

`eval` 함수는 호출된 위치의 기존 스코프를 런타임에 동적으로 수정한다. 즉 아래 예제의 `foo` 함수 몸체 내부에서 `eval` 함수에 인수로 전달된 변수 선언문은 호이스팅되지 않고 순차적으로 코드가 실행된다. 따라서 첫 번째 `x`에 대한 참조문은 전역 변수인 `const x = 1;`의 값을 참조하고, 두 번째 `x`에 대한 참조문은 `eval` 함수에 전달된 변수 선언문의 `x` 값을 참조한다

```javascript
const x = 1; 

function foo() {
  console.log(x); // → 1
  eval("var x = 2;");
  console.log(x); // → 2
}

foo();
console.log(x); // → 1
```

다만 인수로 전달한 문자열 코드에 `let`, `const` 키워드를 사용한 변수 선언문이 존재한다면 암묵적으로 strict mode가 적용되어 `eval` 함수는 기존의 스코프를 동적으로 수정하지 않고 자신의 자체적은 스코프를 생성한다. 즉 `eval` 함수는 따로 함수 몸체가 생성되기 때문에 `eval` 함수 몸체 외부에서 `eval`  함수 몸체 내부의 변수를 참조할 수 없게 된다(이해가 되지 않는다면 아래 예제를 참고하자)

```javascript
const x = 1; 

function foo() {
  // eval 함수의 인수에 전달된 변수 선언문이 호이스팅 되지 않기 때문에 첫 번째 참조문은 전역 x 변수의 값을 참조한다
  console.log(x); // → 1
  // const 키워드를 사용한 변수 선언문이 인수로 전달되었다. eval 함수 자체만의 스코프가 생성된다
  eval("const x = 2;");
  // 두 번째 참조문 또한 전역 x 변수의 값을 참조한다. eval 함수 자체만의 스코프가 생겼기 때문에 eval 함수 몸체 외부에서 eval 함수 몸체 내부의 변수를 참조할 수 없다 
  console.log(x); // → 1
}

foo();
console.log(x); // → 1
```

위 예제에서 `eval` 함수 몸체 내부의 `x` 변수 값을 참조하고 싶다면 참조문을 `eval` 함수 몸체 내부에 추가하면 된다

```javascript
const x = 1; 

function foo() {
  console.log(x); // → 1
  eval("const x = 2; console.log(x);"); // → 2
  console.log(x); // → 1
}

foo(); 
console.log(x); // → 1
```

이렇게 `eval` 함수에 대해 살펴봤지만, 사실 모던 JS에서는 `eval` 함수를 사용하지 않는다. 이전의 JS는 문자 값을 객체로 만드는 방법이 없어서 자주 사용되었던 함수다. 그러나 현재는 JS에서는 새롭고 완성도 높은 기능들이 제공되기도 하고, 보안적인 이슈로 사용하지 않는 함수이니 참고만 해두자

### 3-2-2 isInfinite 함수

전달받은 인수가 유한수이면 불리언 `true` 값을 반환하고, 무한수이면 불리언 `false` 값을 반환한다

```javascript
isFinite(0); // → true
isFinite(2e64); // → true

isFinite(Infinity); // → false
isFinite(-Infinity); // → false
```

만약 전달받은 인수가 숫자 타입이 아닌 경우, 숫자 타입으로 [암묵적 타입 변환](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Coercion%20%26%20Short-circuit/implicit%20coercion.md)한 후 검사를 수행한다

```javascript
isFinite("10"); // → true
isFinite(null); // → true
isFinite(false); // → true
isFinite([]); // → true
isFinite(""); // → true
```

이때 인수가 `NaN`으로 평가되는 값이라면 `false`를 반환한다

```javascript
isFinite(NaN); // → false
isFinite("Hello World"); // → false
```

### 3-2-3 isNaN 함수

전달받은 인수가 `NaN`인지 검사하여 결과에 따라 불리언 타입 `true` 혹은 `false`를 반환한다

```javascript
isNaN(NaN); // → true
isNaN(10); // → false
```

만약 전달받은 인수가 숫자가 아닌 경우 숫자 타입으로 암묵적 타입 변환한 후 검사를 수행한다

```javascript
isNaN("Hello World"); // → true
isNaN(undefined); // → true
isNaN("10"); // → false
isNaN(null); // → false
isNaN(false); // → false
isFinite([]); // → false
isNaN(""); // → false
```

### 3-2-3 isNaN 함수

### 3-2-4 parseFloat 함수

전달받은 문자열 형태의 인수를 부동 소수점 숫자(실수)로 파싱(해석)해서 반환한다

```javascript
parseFloat("3.14"); // → 3.14
parseFloat("1 * 4"); // → 4
```

공백으로 구분된 문자열은 첫 번째 문자열만 파싱해서 반환한다

```javascript
parseFloat("30 31 32"); // → 30
parseFloat("30 years old"); // → 30
```

전달받은 인수가 문자열이 아니면 문자열로 반환한 후 실수로 파싱해서 반환한다

```javascript
parseFloat(10.11); // → 10.11
parseFloat(1*5); // → 5
```

전달받은 문자열 형태의 인수가 숫자로의 변환이 불가능할 경우 `NaN`을 반환한다

```javascript
parseFloat("I am 30 years old"); // → NaN
```

전달받은 문자열 형태의 인수에서 앞뒤 공백은 무시된다

```javascript
parseFloat(" 30 "); // → 30
```

### 3-2-5 parseInt 함수

`parseInt` 빌트인 함수는 첫 번째 인수에 문자열, 두 번째 인수에 기수(Radix)를 전달한다. 두 번째 인수인 기수를 생략하면 첫 번째 인수로 전달된 문자열을 10진수로 파싱해서 반환한다

```javascript
parseInt(string, radix);
```

전달받은 문자열 형태의 첫 번째 인수를 정수(Integer)로 파싱해서 반환한다

```javascript
parseInt("10"); // → 10
parseInt("11.11"); // → 11
```

전달받은 인수가 문자열이 아니면 문자열로 반환한 후 정수로 파싱해서 반환한다 

```javascript
parseInt(10.11); // → 10
parseInt(1*5); // → 5
```

두 번째로 인수로 진법을 나타내는 기수(Radix, `2~36`를 전달하면 첫 번째 인수로 전달된 문자열을 해당 기수의 숫자로 파싱해서 반환한다. 이때 파싱되어 반환되는 값은 언제나 10진수다

```javascript
// 문자열 "10"을 파싱하고 그 결과를 10진수 정수로 반환
parseInt("10"); // → 10

// 문자열 "10"을 파싱하고 그 결과를 2진수 정수로 반환
parseInt("10", 2); // → 2

// 문자열 "10"을 파싱하고 그 결과를 16진수 정수로 반환
parseInt("10", 16); // → 16
```

만약 첫 번째 인수로 전달된 문자열이 `"0x"` 혹은 `"0X"`로 시작하는 16진수 리터럴이라면 두 번째 인수로 진법을 나타내는 기수를 따로 지정하지 않아도 16진수로 파싱되어 10진수 정수로 반환한다

```javascript
// 두 번째 인수로 진법을 나타내는 기수를 16진수로 지정
parseInt("f", 16); // → 15

// 두 번째 인수로 진법을 나타내는 기수를 지정하지 않았지만 전달된 문자열이 16진수로 자동 파싱되는 경우
parseInt("0xf"); // → 15
```

2진수 리터럴과 8진수 리터럴은 제대로 파싱되지 않는다. 따라서 문자열을 8진수로 파싱하려면 기수를 반드시 지정해야한다

> ES5(ECMAScript 2009) 이전까지는 `"0b"`로 시작하는 2진수 리터럴을 파싱하지 못했다. ES5(ECMAScript 2009)부터는 `"0"`으로 시작하는 숫자를 8진수로 파싱되었다. 그러나 ES6(ECMAScript 2015)부터는 `"0"`으로 시작하는 숫자를 8진수로 파싱되지 않고 10진수로 파싱된다

```javascript
// "0b"로 시작하는 2진수 리터럴. 0 이후가 무시된다
parseInt("0b10"); // → 0

// "0o"로 시작하는 8진수 리터럴. 0 이후가 무시된다
parseInt("0o10"); // → 0
```

첫 번째 인수로 전달된 문자열의 첫 번째 문자가 지정된 기수의 숫자로 파싱될 수 없다면 `NaN`을 반환한다

```javascript
// 문자열 타입 "A"는 10진수로 파싱될 수 없다
parseInt("A0"); // → NaN

// 문자열 타입 "2"는 2진수로 파싱될 수 없다
parseInt("20", 2); // → NaN
```

첫 번째 인수로 전달된 문자열의 두 번째 문자부터 지정한 기수로 파싱되지 못하는 문자와 마주치면 이후의 문자는 모두 무시되어 파싱되지 않으며, 이전에 파싱된 정수값만 반환한다

```javascript
// 10진수로 파싱될 수 없는 문자 "A" 이후의 문자들은 모두 무시된다
parseInt("1A0"); // → 1

// 2진수로 파싱될 수 없는 문자 "2" 이후의 문자들은 모두 무시된다
parseInt("102", 2); // → 2
parseInt("10", 2); // → 2

// 8진수로 파싱될 수 없는 문자 "8" 이후의 문자들은 모두 무시된다
parseInt("58", 8); // → 5
parseInt("5", 8); // → 5

// 16진수로 파싱될 수 없는 문자 "G" 이후의 문자는 모두 무시된다
parseInt("FG", 16); // → 15
parseInt("F", 16); // → 15
```

첫 번째 인수로 전달된 문자열에 공백이 있다면 첫 번째 문자열만 파싱해서 반환하고 앞뒤 공백은 무시된다

```javascript
parseInt("30 31 32"); // → 30
parseInt(" 30 "); // → 30
parseInt("30 years old"); // → 30
```

### 3-2-6 encodeURI와 decodeURI 함수

<br>

## 4 암묵적 전역

선언하지 않은 변수에 대해서 JS 엔진이 암묵적으로 전역 객체의 프로퍼티로 동적 생성하는 것을 암묵적 전역(Implicit Global)이라 부른다

아래 예제를 살펴보면 `func` 함수 내의 `y` 식별자는 변수 선언문 키워드(`var`, `let`, `const`)로 선언하지 않은 식별자다. 따라서 변수 선언 없이 식별자에 값을 할당하는 `y = 20` 할당문을 실행하면 에러가 발생할 것처럼 보인다. 그러나 `y` 식별자는 이미 선언된 전역 변수처럼 동작하는 것을 확인할 수 있다. 이는 앞서 언급한대로 선언하지 않은 식별자에 값을 할당하면 JS 엔진이 암묵적으로 전역 객체의 프로퍼티로서 동적 추가하기 때문이다

```javascript
var x = 10; 

function func() {
  // 변수 선언 키워드를 사용하지 않고 식별자에 값을 할당
  y = 20;
}

foo(); 

// 선언하지 않은 y 식별자에 대한 참조가 가능하다
console.log(x + y); // → 30
```

이는 JS 엔진이 `y = 20` 할당문을 프로퍼티 접근 연산자를 통해 `window` 전역 객체에 `y` 식별자를 프로퍼티로서 추가하도록 해석한다(e.g. `window.y=20;`). 다시 말해, `y` 변수 선언 없이 전역 객체에 프로퍼티로서 추가하는 것 뿐이다. 이때 주의해야할 것은 `y` 식별자는 변수가 아니므로 따로 호이스팅이 발생하지 않는다는 사실이다

```javascript
console.log(x); // → undefined
console.log(y); // → Uncaught ReferenceError: y is not defined

var x = 10; 

function func() {
  y = 20; 
}

foo();
console.log(x + y); // → 30
```

변수로 선언되지 않고 전역 객체의 프로퍼티인 `y` 식별자는 프로퍼티이기 때문에 `delete` 연산자를 통해 전역 객체 프로퍼티에서 삭제할 수 있다

```javascript
var x = 10; 

function func() {
  y = 20; 
  console.log(x + y); // → 30
}

foo(); 

console.log(window.x); // → 10
console.log(window.y); // → 20

delete y; 

console.log(window.y); // → undefined
```

`var`  키워드로 선언한 변수는 전역 객체의 프로퍼티가 되지만 변수이기 때문에  `delete` 연산자로 삭제할 수 없다

```javascript
var x = 10; 

console.log(window.x); // → 10

delete x; // → false

console.log(window.x); // → 10
```

<br>

***

### 참고

- [모던 자바스크립트 Deep Dive]()
- [eval() 사용과 문제점](https://webclub.tistory.com/512)
- [스크립트 코드를 실행시키는 `eval()` 사용 방법을 알아보자](https://7942yongdae.tistory.com/149)
- [eval 함수 사용법](https://developer-talk.tistory.com/270)

