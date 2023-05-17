# var, let, const 키워드의 차이

### 목차

- [1 var 키워드의 문제점](#1-var-키워드의-문제점)
  - [1-1 변수 중복 선언](#1-1-변수-중복-선언) 
  - [1-2 함수 레벨 스코프](#1-2-함수-레벨-스코프)
  - [1-3 변수 호이스팅](#1-3-변수-호이스팅)
  - [1-4 전역 변수의 긴 생명 주기](#1-4-전역-변수의-긴-생명-주기)
  
- [2 let 키워드](#2-let-키워드)
  - [2-1 변수 중복 선언 금지](#2-1-변수-중복-선언-금지) 
  - [2-2 블록 레벨 스코프](#2-2-블록-레벨-스코프) 
  - [2-3 변수 호이스팅](#2-3-변수-호이스팅)
  - [2-4 전역 객체와 let](#2-4-전역-객체와-let)
  
- [3 const 키워드](#3-const-키워드)
  - [3-1 const와 let 키워드](#3-1-const와-let-키워드)
  - [3-2 const 키워드와 원시 값](#3-2-const-키워드와-원시-값)
  - [3-3 const 키워드와 객체 값](#3-3-const-키워드와-객체-값)
  - [3-4 전역 객체와 const](#3-4-전역-객체와-const)


***

<br>

## 1 var 키워드의 문제점

ES5까지 변수를 선언할 수 있는 방법은 변수 선언 키워드인 `var` 키워드가 유일했다. 그러나 `var` 키워드로 선언된 변수에는 부작용이 있기 때문에 ES6(2015) 이후부터는 `let`, `const` 키워드가 추가되어 상황과 필요에 따라 변수를 선언할 수 있게 되었다. 먼저 `var` 키워드로 변수를 선언했을 경우 어떠한 부작용이 있는지 살펴보자

### 1-1 변수 중복 선언
`var` 키워드로 선언한 변수는 중복 선언이 가능하다. 아래 예제와 같이 동일한 이름의 변수가 이미 선언된 경우라도 변수를 중복 선언하면서 기존 변수의 값까지 변경하게 되는 부작용이 발생한다 

```javascript 
var variable = "a"; 
var variable = "b"; 

console.log(variable); // → b
```
물론 중복 선언으로 인한 오류를 대비해 변수에 할당된 값이 초기화되는 대비책은 존재하긴 한다. `var` 키워드로 선언한 변수를 중복 선언할 때, 위 예제와 같이 초기화문(변수 선언과 동시에 값을 할당하는 문)이 있는 변수 선언문은 `var` 키워드가 없는 것처럼 동작해서 재할당문(`varabile = "b"`)이 된다. 그리고 초기화문이 없는 변수 선언문은 변수 선언 코드 자체가 무시되어 어떠한 상태 변화가 일어나지 않는다 
```javascript
var variable = "a"; 
var variable; 

console.log(variable); // → a
```
### 1-2 함수 레벨 스코프

`var` 키워드로 선언한 변수는 제어문이 아닌 [함수의 코드 블록](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Scope/scope.md#4-%ED%95%A8%EC%88%98-%EB%A0%88%EB%B2%A8-%EC%8A%A4%EC%BD%94%ED%94%84)만을 지역 스코프로 인정한다. 따라서 함수 외부에서 `var` 키워드로 선언한 변수는 모두 전역 변수가 된다. 다시 말해, 제어문에서 쓰이는 변수도 전역 변수이기 때문에 변수 네이밍에도 주의를 기울이지 않으면 변수의 중복 선언으로 인해 의도치 않게 변수 값이 변경될 위험이 높다는 의미다. 앞서 [전역 변수의 문제점](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Scope/global%20variable.md)에서도 언급했듯이, 전역 변수는 생명 주기가 길어 오류가 발생될 가능성 또한 크다

```javascript
// 제어문(if, for, while 등)의 코드 블록 내에서도 전역 변수로 간주된다
var i = 50; 

for (var i = 0; i < 10; i++ ) {
  console.log(i); // → 0 1 2 3 4 5 6 7 8 9
} 

console.log(i); // → 10
```
### 1-3 변수 호이스팅
`var` 키워드로 변수를 선언하면 변수 선언이 [호이스팅](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/variable.md#6-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)된다. 따라서 `var` 키워드로 선언한 변수는 참조 코드보다 순서적으로 밑에 위치해도 변수 선언문을 참조할 수 있다. 물론 이러한 이른 참조는 에러를 발생시키지는 않지만 프로그램의 가독성을 떨어뜨리고 원리를 이해하는데 어려움을 남긴다

```javascript 
console.log(variable); // → undefined

var variable = "값의 할당은 런타임 이후에 실행된다"; 

console.log(variable); // → 값의 할당은 런타임 이후에 실행된다 
```

### 1-4 전역 변수의 긴 생명 주기

앞서 [전역 변수의 문제점](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Scope/global%20variable.md#1-2-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98%EC%9D%98-%EC%83%9D%EB%AA%85-%EC%A3%BC%EA%B8%B0)에서도 살펴보았듯이, `var` 키워드로 선언한 전역 변수는 브라우저 환경에서는 `window` 객체, node.js 환경에서는 `global` 객체에 프로퍼티가 되어 전역 객체와 생명 주기가 동일하다. 따라서 생명 주기가 긴 만큼 오류가 발생할 가능성도 높아지고, 메모리 소비 효율도 떨어진다

<br>

## 2 let 키워드
`var` 키워드의 단점을 보완하기 위한 ES6(2015) 이후 처음 도입된 첫 번째 변수 선언 키워드다 

### 2-1 변수 중복 선언 금지 
`let` 키워드로 이름이 같은 변수를 중복 선언하면 `SyntaxError`가 발생한다
```javascript
let variable = 1; 
let variable = 10; // → SyntaxError: Identifier 'variable' has already been declared 
```
### 2-2 블록 레벨 스코프
`let` 키워드로 선언한 변수는 함수(함수 레벨 스코프)를 포함해 모든 코드 블록(`if`문, `for`문, `while`문, `try`/`catch`문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다. 즉, `let` 키워드로 선언한 변수는 모든 코드 블록 내부에서는 지역 변수로 취급된다

```javascript
let variable = 1; 

if (true) {
  let variable = 10;
  let variable2 = 20; 
  
  console.log(variable); // → 10
} 

console.log(variable); // → 1
console.log(variable2); // → ReferenceError: variable2 is not defined
```
### 2-3 변수 호이스팅
`let` 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다 
```javascript
console.log(variable); // → ReferenceError: variable is not defined

let variable = "변수 호이스팅이 발생하지 않는 것처럼 보인다"; 

console.log(variable); // → "변수 호이스팅이 발생하지 않는 것처럼 보인다"
```
앞서 `var` 키워드로 선언한 변수는 선언 단계와 초기화 단계가 런타임 이전에 호이스팅되어 한번에 이뤄지고 값의 할당 단계는 런타임 이후에 이뤄진다 했다 
```javascript
// 변수 선언및 값의 할당문의 실제 실행 단계 
var variable = 1; 

=> var variable; // → undefined (변수 선언과 초기화 단계가 한번에 이뤄진다) 
=> variable = 1; // 변수에 값이 할당되는 값의 할당 단계는 런타임 이후에 따로 이뤄진다
```
`let` 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 보이지만 실제로는 호이스팅이 된다. 단, 변수의 선언 단계와 초기화 단계가 분리되어 진행된다. 다시 말해, **`let` 키워드로 선언한 변수는 JS 엔진에 의해 호이스팅 되어 런타임 이전에는 '변수 선언'은 단계는 실행되지만 초기화 단계는 코드가 순차적으로 실행되는 시점인 런타임 이후에 변수 선언문 및 할당문에 도달했을 때 실행**된다. 따라서 초기화 단계가 실행되기 이전에 변수를 참조하려고 하면 참조 에러가 발생하는 것이다

즉, `let` 키워드로 선언한 변수의 선언, 초기화, 값의 할당 과정은 아래와 같이 이뤄진다: 

> 변수 선언이 호이스팅 되어 변수 이름이 실행 컨텍스트의 렉시컬 환경에 등록된다. 실행 컨텍스트와 렉시컬 환경에 대해서는 추후 다룰 예정이다

```javascript
// (1) 변수 선언: 호이스팅 되어 실행 컨텍스트의 렉시컬 환경에 변수의 존재는 등록된다
console.log(foo); // → ReferenceError: Cannot access 'foo' before initialization

// (2) 메모리 초기화
let foo; 
console.log(foo); // → undefined

// (3) 값의 할당
foo = 1;
console.log(foo); // → 1
```

### 2-4 전역 객체와 let

앞서 `var` 키워드로 선언한 전역 변수는 브라우저 환경에서는 전역 객체 `window`의 프로퍼티가 된다고 했다

```javascript
// var 키워드로 선언한 전역 변수는 
var x = 1;

// 브라우저 환경에서 전역 객체 window의 프로퍼티로 등록된다
window = {
  x: 1
}

console.log(window.x); // → 1
console.log(x); // → 1
```

반면, `let` 키워드로 선언한 변수는 브라우저 환경에서 전역 객체 `window`의 프로퍼티로 등록되지 않는다. 따라서 전역 변수여도 `let` 키워드로 선언한 변수의 생명 주기는 `var` 키워드로 선언한 전역 변수의 생명 주기보다 짧다

```javascript
let x = 1;

console.log(window.x); // → undefined
console.log(x); // → 1
```

<br>

## 3 const 키워드
`const` 키워드는 상수를 선언하기 위해 사용한다. 상수란 일반적으로 '변하지 않는 값'을 나타낼 때 사용되지만, 프로그래밍에서 상수는 '재할당이 금지된 변수'를 의미한다. 즉, `const` 키워드로 선언한 변수는 **재할당이 금지된 변수일 뿐 실제 값의 변경이 '불변'하다는 의미는 아니다**. 이에 대해서는 `const` 키워드의 후반부 내용을 참고하자


### 3-1 const와 let 키워드
`const` 키워드의 특징은 `let` 키워드와 대부분 동일하다. `const` 키워드도 변수 중복 선언이 불가하며 함수를 포함한 모든 코드 블록에 대해 블록 레벨 스코프를 가진다
```javascript
// 변수 중복 선언 금지
const constant = 1; 
const constant = 2; // → SyntaxError: 'constant' has already been declared
```
```javascript
// 코드 블록 레벨 스코프를 가진다 
const variable = 1; 

if (true) {
  const variable = 10;
  const variable2 = 20; 
  
  console.log(variable); // → 10
} 

console.log(variable); // → 1
console.log(variable2); // → ReferenceError: variable2 is not defined
```

따라서 `const` 키워드로 선언한 변수는 반드시 선언과 동시에 값을 할당하는 초기화 단계가 이뤄져야 한다. 재할당이 금지된 변수로 선언하는 것이기 때문에 추후 필요에 따라 값을 할당해줄 수 없기 때문이다 
```javascript
const constant; // →SyntaxError: Missing initializer in const declaration

// const 키워드로 선언한 변수는 반드시 변수 선언과 초기화가 함께 이뤄져야 한다 
const constant = 1; 

// const 키워드로 선언한 변수는 재할당이 금지된 변수다
const constant = 1; 
constant = 2; // → TypeError: Assignment to constant variable
```

또한, `const` 키워드로 선언한 변수 역시 `let` 키워드로 선언한 변수와 같이 호이스팅이 발생하지 않는 것처럼 동작한다(그러나 앞서 `let` 키워드에 대해 언급했듯이, 실제로는 호이스팅이 이뤄진다)
```javascript
console.log(constant); // → ReferenceError: Cannot access 'constant' before initialization

const constant = 1; 

console.log(constant); // → 1
```
### 3-2 const 키워드와 원시 값
앞서 `const` 키워드로 선언한 변수에 기존 값과 다른 다른 값을 재할당할 수 없다고 했다. 거기에 `const` 키워드로 선언한 변수에 원시 값을 할당하는 경우 값 자체도 변경할 수 없게 된다. 이러한 `const` 키워드의 특성을 활용해 가독성, 유지보수성을 개선할 수 있다
```javascript
// INCH를 CM로 변환하는 함수
function convertToCM(inputInch) {
  const convert = inputInch * 2.54;
  
  console.log(`${inputInch}인치는 ${convert}cm 입니다`); 
}

convertToCM(50); // → 50인치는 127cm 입니다
```
위 예제 코드에서 `2.54`는 무엇을 의미하는지 명확히 알 수 없기 때문에 가독성이 좋지 않다. 1인치는 2.54cm라는 사실은 변하지 않는다. 따라서 길이 단위를 상수로 정의하면 `2.54`라는 값의 의미를 보다 직관적으로 표현할 수 있고 고정 값으로도 사용이 가능하다. 또한 만약에 인치당 센치미터의 고정 단위가 전 세계적으로 변경되어 재정의된다면, 상수로 `CM_PER_INCH`의 단위만 재선언하면 되기 때문에 유지보수성이 향상된다

> 의미가 불명확한 숫자를 매직 넘버(Magic Number)라 부르며 실무에서도 inch와 같은 보편적인 단위를 단순히 숫자  `2.54`로 작성하는 것을 지양한다

```javascript 

const CM_PER_INCH = 2.54; 

function convertToCM(inputInch) {
  const convert = inputInch * CM_PER_INCH;
  
  console.log(`${inputInch}인치는 ${convert}cm 입니다`); 
}

convertToCM(50); // → 50인치는 127cm 입니다
```
### 3-3 const 키워드와 객체 값
`const` 키워드는 재할당을 금지할 뿐 값의 절대적 '불변'을 의미하지 않는다. 따라서 `const` 키워드로 선언된 변수에 객체를 할당한 경우 변수 값을 변경할 수 있다. [원시 값과 객체의 비교]()에서 다루었듯이, 객체의 값이 변경된다 하더라도 변수에 할당된 참조 값은 변경되지 않기 때문이다. 
```javascript 
const userInfo = { 
  name: "Jace", 
  age: 30
} 

userInfo["name"] = "Ju Hyung"; 
userInfo.residence = "Seoul";
delete userInfo["age"]; 

console.log(userInfo); // → {name: 'Ju Hyung', residence: 'Seoul'}
```
### 3-4 전역 객체와 const

앞서 [전역 객체와 let](#2-4-전역-객체와-let)에서 살펴보았듯이 `let` 키워드로 선언한 변수는 전역 객체 `window`의 프로퍼티로 등록되지 않는다. `const` 키워드 또한 `let` 키워드와 동일하다. `const` 키워드로 선언한 변수는 전역 변수여도 전역 객체 `window`에 등록되지 않는다

```javascript
const x = 1;

console.log(window.x); // → undefined
console.log(x); // → 1
```

<br>

***앞으로 이후 모든 변수 선언은 `var` 키워드가 아닌 `let` 혹은 `const` 키워드를 사용할 예정이다*** 
<br>

***
### 참고
- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [JavaScript MDN - let, const 키워드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/const)
- [모던 JavaScript 튜토리얼 - 변수와 상수](https://ko.javascript.info/variables)
- [[JavaScript]전역 변수 사용을 줄여보자, 근데 어디까지 전역변수야?](https://hellvelopment.tistory.com/77)