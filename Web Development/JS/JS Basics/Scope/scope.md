# 스코프

### 목차

- [1 스코프란](#1-스코프란)
- [2 스코프의 종류](#2-스코프의-종류)
- [3 스코프 체인](#3-스코프-체인)
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

> 코드 문맥에 따라 JS 엔진에 의해 코드 실행 결과가 달라지는 현상은 [명시적/암묵적 타입변환](), [단축평가](), [함수 정의]() 등에서 자주 접했었다. 이러한 코드 문맥과 환경은 추후에 다뤄질 [렉시컬 환경](), [실행 컨텍스트]()와 관련이 깊다

<img src="https://user-images.githubusercontent.com/92138751/236974091-fe42ed01-7696-465c-ae76-c91b4bda78e3.png" style="width: 100%"> 
<br>


## 2 스코프의 종류

코드는 코드 전체 영역인 전역(Global)과 코드 일부 영역인 지역(Local)으로 구분된다. 그리고 이를 전역 스코프와 지역 스코프라고 부른다. 

- **전역 스코프**: **코드의 가장 바깥 영역**(함수 외부)에서 변수가 선언되면 이를 전역 변수라 부르며 전역 스코프를 갖게 된다
- **지역 스코프**: **함수 몸체 내부**에서 변수가 선언되면 이를 지역 변수라 부르며 지역 스코프를 갖게 된다

즉, 변수가 선언된 위치(전역 또는 지역)에 따라 스코프가 결정된다. 아래 예제의 결과를 통해 알 수 있듯이, 전역 스코프를 갖는 전역 변수는 어떤 지역(함수 내부)에서도 참조가 가능하다. 지역 스코프를 갖는 지역 변수는 자신이 선언된 지역(`outerScope` 함수 지역)과 하위 지역(`innerScope` 함수 지역)에서만 참조가 가능하다 

> 실무 프로그래밍에서 실제로 전역 변수의 사용을 지양한다. 전역 변수는 어떤 지역에서도 참조가 가능하기 때문에 의도치 않은 오류가 발생할 수 있기 때문이다. 이에 대해서는 [여기](https://intzzzero.netlify.app/blog/no-more-global-variables)를 참고하자

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

앞서 스코프는 전역과 지역으로 나뉜다고 했다. 또한 지역 스코프는 함수 몸체 내부에서 선언된 변수가 갖는 유효범위라고 했다. 만약 함수 몸체 내부에 다른 함수를 정의하면 이를 [중첩 함수]()라 부르며, 이는 외부 함수가 갖는 지역 스코프 내에 중첩 함수의 또 다른 지역 스코프가 중첩되어 생성된다는 의미다. 이때 외부 함수의 지역 스코프는 상위 스코프, 중첩 함수의 지역 스코프는 하위 스코프로 계층적 구조를 갖게 된다. 이렇게 스코프가 계층적으로 연결된 것을 스코프 체인이라 부르며 아래 그림과 같이 스코프 체인을 표현할 수 있다: 

> 스코프의 개념을 이해한다면 스코프 체인의 개념은 쉽게 이해할 수 있어 중요하지 않아 보일 수 있다. 그러나 JS 엔진은 전역 코드와 함수 코드를 실행하기 앞서 위 스코프 체인과 굉장히 유사한 자료구조인 [렉시컬 환경]()을 생성하기 때문에, 뒤에 다룰 렉시컬 환경을 보다 쉽게 이해하기 위해서는 스코프 체인 형태의 자료 구조를 미리 파악해두는 것이 좋다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/4b9efdc8-5062-454b-bb51-2838cc2257c7" width="100%">

변수를 참조 시 JS 엔진은 스코프 체인을 통해 변수를 참조하는 코드의 지역 스코프에서부터 상위 스코프 방향으로 이동하며 참조된 변수를 찾아나가며, 이러한 과정을 '변수의 검색(Identifier Resolution)'이라 부른다. 다시 말해, 변수의 검색은 아래 예제와 같이 '하위 스코프 → 상위 스코프' 방향으로 이뤄지지만, '상위 스코프 → 하위 스코프' 방향으로 이뤄지지 않는다

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

다른 스코프 체인 개념의 '변수 검색' 예제를 살펴보자. 



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

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [전역 변수를 왜 지양해야 하는가](https://intzzzero.netlify.app/blog/no-more-global-variables)