# 스코프

### 목차

- [1. 스코프란]()
- [2. 스코프의 종류]()
- [3. 스코프 체인]()
- [4. 함수 레벨 스코프]() 
- [5. 렉시컬 스코프]()

***

<br>

## 1 스코프란

스코프(Scope, 유효 범위)는 식별자(변수뿐만 아니라 함수, 클래스 등도 포함)의 유효 범위를 의미한다. 앞선 JS의 기초 개념들에서 스코프를 이미 접한 적이 있다. 바로 [함수]()에서 매개변수가 함수 몸체 내부에서만 유효하여 함수 외부에서는 참조될 수 없다는 것이다. 다시 말해, 매개변수의 스코프는 함수 몸체 내부로 한정되어 있다는 것이다
```javascript
function checkScope(x, y) {
  console.log(x, y); // → 1, 2
}

checkScope(1, 2); 

console.log(x, y); // → Uncaught ReferenceError: x is not defined 
```
다음 예제를 살펴보자. 코드의 가장 바깥 영역(최상위 영역)과 함수 `func` 내부에 동일한 이름을 갖는 변수 `number`를 선언했다. 이때 변수 `number`의 참조 결과를 살펴보면, 함수 내부에서는 `2`이 반환되고 함수 외부에서는 `1`이 반환된다. 왜 그런 것일까?
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

> 코드 문맥에 따라 JS 엔진에 의해 코드 실행 결과가 달라지는 현상은 [명시적/암묵적 타입변환](), [단축평가](), [함수 정의]() 등에서 자주 접했었다. 이러한 코드 문맥과 환경은 추후에 다뤄질 [렉시컬 환경](), [실행 컨텍스트]()와 관련이 깊다

<img src="https://velog.velcdn.com/images/jacenam/post/3ce44f33-6246-448e-9f08-8026f78f55f1/image.png" style="max-width: 100%" align="center"> 
<br>


## 2 스코프의 종류

코드는 코드 전체 영역인 전역(Global)과 코드 일부 영역인 지역(Local)으로 구분된다. 그리고 변수는 선언된 위치(전역 또는 지역)에 따라 스코프가 결정된다 

- **전역 변수**: **코드의 가장 바깥 영역**에서 선언된 변수로 전역 스코프를 갖게 된다
- **지역 변수**: **함수 몸체 내부**에서 선언된 변수로 지역 스코프를 갖게 된다

각 변수 스코프에 따라 
```javascript
// 전역 변수 선언
var x = 10; 
var y = 20; 

// 지역 변수 선언
function func() {
  var z = 30; 
  // 전역 변수 x, y는 함수 내부 영역인 지역에서도 참조할 수 있다 
  console.log(x + y + z); // → 60
} 

func()

// 지역 변수 z는 코드의 가장 바깥 영역인 전역에서 참조할 수 없다
console.log(x + y + z); // → Uncaught ReferenceError: z is not defined
```

```javascript 
var x = "global x"; 
var y = "global y"; 

function outer() {
  var z = "outer's local z";
  
  console.log(x); // 1 
  console.log(y); // 2
  console.log(z); // 3
  console.log(a); //
  
  function inner() {
    var x = "inner's local x"; 
    var a = "inner's local a"; 
    
    console.log(x); // 4
    console.log(y); // 5
    console.log(z); // 6
  }
  
  inner(); 
}

outer(); 

console.log(x); // 7
console.log(y); // 8
```
```javascript
function foo() {
  console.log("global function foo"); 
}

function bar() {
  function foo() {
    console.log("local function foo"); 
  }
  foo();
}

bar();  
```

같은 코드의 가장 바깥 영역(전역)에서 선언된 변수와 함수들이 전역 스코프(아래 예제처럼 전역 변수, 전역 스코프를 가진 함수들의 조합)를 가질 때 어떻게 상위 스코프를 가릴까?  
```javascript
var x = 1; 

function foo() {
  var x = 10; 
  bar(); 
}

function bar() {
  console.log(x); 
}
```
```javascript
var x = 1; 

function foo() {
  var x = 10; 
  
  function bar() {
    console.log(x); 
  }
  
  bar(); 
}

foo(); 
```

<br>

### <span style="color:  #F15F3F">학습하며 정리 중</span>

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)