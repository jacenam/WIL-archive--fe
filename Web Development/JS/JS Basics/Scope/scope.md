# 스코프

### 목차

- [1 스코프란](#1-스코프란)
- [2 스코프의 종류](#2-스코프의-종류)
- [3 스코프 체인](#3-스코프-체인)
  - [3-1 변수의 스코프 체인](#3-1-변수의-스코프-체인)
  - [3-2 함수의 스코프 체인](#3-2-함수의-스코프-체인)

- [4 함수 레벨 스코프](#4-함수-레벨-스코프) 
- [5 렉시컬 스코프](#5-렉시컬-스코프)

***

<br>

## 1 스코프란

스코프(Scope, 유효 범위)는 식별자(변수뿐만 아니라 함수, 클래스 등도 포함)의 유효 범위를 의미한다. 앞선 JS의 기초 개념들에서 스코프를 이미 접한 적이 있다. 바로 [함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/function.md)에서 매개변수가 함수 몸체 내부에서만 유효하여 함수 외부에서는 참조될 수 없다는 것이다. 다시 말해, 매개변수의 스코프는 함수 몸체 내부로 한정되어 있다는 것이다
```javascript
function checkScope(x, y) {
  console.log(x, y); // → 1, 2
}

checkScope(1, 2); 

console.log(x, y); // → Uncaught ReferenceError: x is not defined 
```
다음 예제를 살펴보자. 코드의 가장 바깥 영역(최상위 영역)과 함수 `func` 내부에 동일한 이름을 갖는 변수 `number`를 선언했다. 이때 변수 `number`의 참조 결과를 살펴보면, 함수 내부에서는 `2`가 반환되고 함수 외부에서는 `1`이 반환된다. 왜 그런 것일까?
```javascript
var number = 1; 

function func() {
  var number = 2; 
  console.log(number); // → 2
}

func(); 

console.log(number); // → 1
```
JS 엔진은 코드 문맥에 따라 이름이 같은 두 개의 변수 `number` 중에서 어떤 변수를 참조할지를 결정한다. 동일한 이름을 가졌기 때문에 하나의 식별자라고 생각할 수도 있지만, JS 엔진은 코드 문맥에 따라 두 개의 변수 `number`에게 각각 다른 스코프를 지정해서 오류 없이 코드가 실행될 수 있도록 한다

> 코드 문맥에 따라 JS 엔진에 의해 코드 실행 결과가 달라지는 현상은 [명시적](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Coercion%20%26%20Short-circuit/explicit%20coercion.md)/[암묵적 타입변환](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Coercion%20%26%20Short-circuit/implicit%20coercion.md), [단축평가](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Coercion%20%26%20Short-circuit/short-circuit%20evaluation.md), [함수 정의](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md) 등에서 자주 접했었다. 이러한 코드 문맥과 환경은 추후에 다뤄질 [렉시컬 환경](), [실행 컨텍스트]()와 관련이 깊다

<img src="https://user-images.githubusercontent.com/92138751/236974091-fe42ed01-7696-465c-ae76-c91b4bda78e3.png" style="width: 100%"> 
<br>


## 2 스코프의 종류

코드는 코드 전체 영역인 전역(Global)과 코드 일부 영역인 지역(Local)으로 구분된다. 전역은 코드의 가장 바깥 영역을 의미하고, 지역은 함수 몸체 내부를 의미한다. 그렇다면 스코프는 무엇일까? 

- **전역 스코프:** 코드의 가장 바깥 영역에서 변수가 선언되면 이를 전역 변수라 부르며 전역 스코프를 갖게 된다. 전역 스코프는 최상위 스코프로 코드 전체에 영향을 끼칠 수 있다
- **지역 스코프:** 함수 몸체 내부에서 변수 혹은 함수(중첩 함수)가 선언되면 이를 지역 변수/함수라고 부르며 지역 스코프를 갖게 된다. 지역 스코프는 해당 지역 내에서만 코드에 영향을 끼칠 수 있다("함수는 함수 몸체 내부에서만 유효하다"는 개념과 동일)

여기서 주의해야 할 점으로, 코드의 가장 바깥 영역에서 함수가 선언(정의)되면 이를 '전역 함수'라 부르긴 하나, **함수는 함수 몸체 내에서만 기능하는 유효성이 있으므로 코드 전체에 영향을 끼치는 전역 스코프는 갖지 않는다**

변수 혹은 함수가 선언/정의된 위치(전역 또는 지역)에 따라 스코프가 결정된다. 아래 예제의 결과를 통해 알 수 있듯이, 전역 스코프를 갖는 전역 변수는 어떤 지역(함수 내부)에서도 참조가 가능하다. 지역 스코프를 갖는 지역 변수는 자신이 선언된 지역(`outerScope` 함수 지역)과 하위 지역(`innerScope` 함수 지역)에서만 참조가 가능하다 

> 실무 프로그래밍에서 실제로 전역 변수의 사용을 지양한다. 전역 변수는 어떤 지역에서도 참조가 가능하기 때문에 의도치 않은 오류가 발생할 수 있기 때문이다. 이에 대해서는 [전역 변수의 문제점]()을 참고하자

```javascript
// 전역 변수 선언
var w = 1; 
var x = 2; 

// 지역 변수 선언
function outerScope() {
  var y = 3;
  // 전역 변수 w, x는 함수 내부 영역인 지역에서도 참조할 수 있다
  console.log(w, x, y); // → 1 2 3
  
  function innerScope() {
    var w = 4; 
    var z = 5; 
    // 상위 지역 변수 x, y는 하위 중첩 함수 영역인 지역에서도 참조할 수 있다 
    // 전역 변수 w가 아닌 지역에서 선언된 지역 변수 w를 참조한다
    console.log(w, x, y, z); // → 4 2 3 5    
  }
  innerScope();
} 

outerScope();

// 지역 변수 y와 z는 코드의 가장 바깥 영역인 전역에서 참조할 수 없다
console.log(w); // → 1
console.log(w, x, y); // → Uncaught ReferenceError: y is not defined
console.log(w, x, y, z); // → Uncaught ReferenceError: y is not defined
```

<br>

## 3 스코프 체인

앞서 스코프는 전역과 지역으로 나뉜다고 했다. 또한 지역 스코프는 함수 몸체 내부에서 선언된 변수 혹은 함수가 갖는 유효범위라고 했다. 만약 함수 몸체 내부에 다른 함수를 정의하면 이를 [중첩 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/nested%20function.md)라 부르며, 이는 외부 함수가 갖는 지역 스코프 내에 중첩 함수의 또 다른 지역 스코프가 중첩되어 생성된다는 의미다. 이때 외부 함수의 지역 스코프는 상위 스코프, 중첩 함수의 지역 스코프는 하위 스코프로 계층적 구조를 갖게 된다. 다시 말해, 전역 스코프를 갖는 전역 변수는 외부 함수의 상위 스코프라고도 해석할 수 있다. 

이렇게 스코프가 계층적으로 연결된 것을 스코프 체인이라 부르며 아래 그림과 같이 스코프 체인을 표현할 수 있다: 

> 스코프의 개념을 이해한다면 스코프 체인의 개념은 쉽게 이해할 수 있어 중요하지 않아 보일 수 있다. 그러나 JS 엔진은 전역 코드와 함수 코드를 실행하기 앞서 위 스코프 체인과 굉장히 유사한 자료구조인 [렉시컬 환경]()을 생성하기 때문에, 뒤에 다룰 렉시컬 환경을 보다 쉽게 이해하기 위해서는 스코프 체인 형태의 자료 구조를 미리 파악해두는 것이 좋다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/243b35da-1934-496f-b76f-2ca2190aa992" width="100%">

### 3-1 변수의 스코프 체인

변수를 참조 시 JS 엔진은 스코프 체인을 통해 변수를 참조하는 코드의 지역 스코프에서부터 상위 스코프 방향으로 이동하며 참조된 변수를 찾아나가며, 이러한 과정을 '식별자의 검색(Identifier Resolution)'이라 부른다. 다시 말해, 식별자의 검색은 아래 예제와 같이 '하위 스코프 → 상위 스코프' 방향으로 이뤄지지만, '상위 스코프 → 하위 스코프' 방향으로 이뤄지지 않는다

```javascript
// 전역 스코프
var x = 1; 

// 지역 스코프 1
function outerScope() {
  var y = 2; 
  console.log(x, y, z); // → ReferenceError: z is not defined
  
  // 지역 스코프 2
  function innerScope() {
    var z = 3; 
    console.log(x, y, z); // → 1 2 3
  }
  innerScope();
}

outerScope();
```

### 3-2 함수의 스코프 체인

전역 스코프를 갖는 전역 함수와 지역 스코프를 갖는 지역 함수만이 존재할 때는 스코프 체인이 어떠한 구조로 형성 되는지 아래 예제를 살펴보자

```javascript
function foo() {
  console.log("global function foo"); // 전역 함수가 실행될까?
}

function bar() {
  function foo() {
    console.log("local function foo"); // 지역 함수가 실행될까?
  }
  foo();
}

bar();  
```

앞서 [함수 선언문과 함수 표현식의 차이](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#4-%ED%95%A8%EC%88%98-%EC%84%A0%EC%96%B8%EB%AC%B8%EA%B3%BC-%ED%95%A8%EC%88%98-%ED%91%9C%ED%98%84%EC%8B%9D%EC%9D%98-%EC%B0%A8%EC%9D%B4)에서 언급했듯이, 함수 선언문으로 함수를 정의할 시 JS 엔진은 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고 함수라는 객체 값이 식별자에 할당된다. 함수 선언문을 통해 암묵적으로 생성된 식별자는 변수와도 같은 기능을 수행함으로 위 [변수의 스코프 체인](#3-1-변수의-스코프-체인)과 동일한 함수의 스코프 체인을 확인할 수 있다:

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/3e9dcbb7-624b-4b4c-8e0d-eb4de4a1ea01" width="100%">

따라서 전역에서 선언된 함수 `foo`는 같은 전역에서 호출되었을 때 실행되고, 지역에서 선언된 함수 `foo`는 지역에서 호출되었을 때 스코프 체인을 따라 식별자 검색을 통해 실행되는 것이다

<br>

## 4 함수 레벨 스코프

지역 스코프는 함수가 정의되면 생성된다. 즉, 지역 스코프는 [제어문](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Control%20Flow/control%20flow%20statements.md)과 같은 [코드 블록](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Control%20Flow/control%20flow%20statement%20intro.md#2-%EB%B8%94%EB%A1%9D%EB%AC%B8)(`{ }`)이 아닌 함수에 의해서 생성된다. 물론 함수도 제어문과 같이 코드블록에 의해 실행될 내부 코드들이 묶이는 것이다. 그러나 지역 스코프는 오직 함수에 의한 코드블록에 한해서만 생성된다는 의미이며, 이러한 특성을 함수 레벨 스코프(Function Level Scope)이라 부른다

> C 언어, Java 등 대부분의 프로그래밍 언어는 함수 몸체뿐만 아니라 제어문을 포함한 모든 코드블록에 의해 지역 스코프를 생성하게 되며, 이러한 특성을 블록 레벨 스코프(Block Level Scope)이라 부른다

```javascript
var x = 1;

if (x = 1) {
  var x = 10; 
}

// JS의 지역 스코프는 블록 레벨 스코프가 아니기 때문에 제어문 내의 변수 x의 재할당은 전역 변수 선언문으로써 전역에서 유효하다
console.log(x); // → 10
```

<br>

## 5 렉시컬 스코프

코드의 가장 바깥 영역에서 선언된 전역 변수와 전역 함수들이 전역 스코프(아래 예제처럼 전역 변수, 지역 스코프를 가진 함수들의 조합)를 가질 때 어떻게 상위 스코프를 가릴까?  아래 예시를 살펴보자:

```javascript
var x = 1; 

function foo() {
  var x = 10; 
  bar(x); 
}

function bar() {
  // 전역 변수 x의 값을 참조할까? 아니면 지역 변수 x의 값을 참조할까?
  console.log(x); 
}

foo(); // ?
bar(); // ?
```

함수 `foo`와 `bar`의 호출 실행 결과는 `1`, `1`이다. 비록 함수 `foo`에는 지역 변수 `x`가 선언되어 새로운 값 `10`을 할당하는 변수 선언문이 존재하고 함수 `bar`의 호출문 `bar(x)`를 통해 매개변수로 지역 변수의 값 `10`을 전달받는 것처럼 보이지만, 함수 `bar`는 전역 변수 `x`의 값 `1`을 참조하여 참조문 `console.log(x)`을 실행한다. 왜 그런 것일까? 

이는 JS가 렉시컬 스코프(Lexical Scope) 방식을 따르기 때문이다. 함수를 어디서 호출했는지가 기준이 아닌, 함수를 어디서 정의했는지에 따라 상위 스코프가 결정되는 방식이다. 위 예시 코드를 살펴보면, 함수 `foo`와 `bar` 모두 전역(코드의 가장 바깥 영역)에서 선언되었기 때문에 함수 호출이 함수 내부 혹은 전역에서 있든 상관없이 상위 스코프에 해당하는 전역 변수 `x`의 값을 참조하는 것이다. 만약 함수 `foo`의 지역 변수 `x`를 참조하게끔 하려면 아래와 같이 코드를 변경하면 된다: 

```javascript
var x = 1; 

function foo() {
  var x = 10; 
  function bar() {
    console.log(x); // → 10
  }; 
  bar();
}

foo(); 
```

다시 말해, 함수가 호출된 위치는 상위 스코프를 가리는데에 어떠한 영향도 없다

> 함수는 정의 이후 필요할 때 호출하여 사용하는 방식이기 때문에, 함수 정의의 시점과 함수 호출의 시점이 대부분 다르게 이뤄진다. 만약 함수 호출 위치에 따라 상위 스코프가 결정된다면 상위 스코프의 기준은 매번 바뀔 것이다. 따라서 이러한 스코프 결정 방식을 동적 스코프(Dynamic Scope)라고 부른다

지역 스코프가 중첩되어 있다면 상위 스코프는 언제나 함수가 정의된 위치에 따라 결정되기 때문에 그 기준은 바뀌지 않는다. 따라서 렉시컬 스코프 방식을 정적 스코프(Static Scope) 방식이라 부르기도 한다

<br>

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)