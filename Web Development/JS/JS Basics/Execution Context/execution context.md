# 실행 컨텍스트

### 목차

- [1 실행 컨텍스트란](#1-실행-컨텍스트란)
  - [1-1 소스코드의 종류](#1-1-소스코드의-종류)
  - [1-2 소스코드의 평가와 실행](#1-2-소스코드의-평가와-실행)
  - [1-3 실행 컨텍스트의 역할](#1-3-실행-컨텍스트의-역할)
- [2 실행 컨텍스트 스택](#2-실행-컨텍스트-스택)
- [3 렉시컬 환경](#3-렉시컬-환경)
- [4 실행 컨텍스트의 생성과 식별자 검색 과정](#4-실행-컨텍스트의-생성과-식별자-검색-과정)
- [5 실행 컨텍스트와 블록 레벨 스코프](#5-실행-컨텍스트와-블록-레벨-스코프)

<br>

## 1 실행 컨텍스트란

실행 컨텍스트(Execution Context)는 소스코드를 실행하는 데 필요한 환경을 제공하고 코드의 실행 결과를 관리한다.  JS의 동작 원리를 이해하는데 핵심 개념으로서, 추후 살펴볼 식별자 바인딩, 호이스팅 발생 이유, 클로저, 태스큐 큐, 이벤트 핸들러, 비동처 처리 등의 동작 원리와 개념들을 이해할 수 있다. 실행 컨텍스트에 대해 정리하기에 앞서 ECMAScript 사양을 살펴봐야 한다

### 1-1 소스코드의 종류

JS는 개발자가 컴퓨터에게 전달한 소스코드를 한 줄씩 실행하는 인터프리터 언어라고 했다. JS 명세인 ECMAScript 사양에 따르면 소스코드는 4가지 타입으로 구분된다. 아래 4가지 타입의 소스코드는 종류에 따라 실행 컨텍스트를 각각 다르게 생성하고 관리한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/3e9ca6cf-ecb8-4e79-b730-99909c89313d" width="100%">

| 소스코드 타입            | 설명                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 전역 코드(Global Code)   | 전역에 존재하는 소스코드. 단, 전역에서 정의된 함수, 클래스 등의 내부 코드들은 포함하지 않는다 |
| 함수 코드(Function Code) | 함수 내부에 존재하는 소스코드. 단, 함수 내부에 중첩된 함수(헬퍼 함수), 클래스 등의 내부 코드는 포함하지 않는다 |
| `eval` 코드(Eval Code)   | 빌트인 전역 `eval` 함수에 인수로 전달되어 실행되는 소스코드  |
| 모듈 코드(Module Code)   | 모듈 내부에 존재하는 소스코드. 단, 모듈 내부의 함수, 클랫 등의 내부 코드는 포함하지 않는다 |

- **전역 코드**

  코드의 가장 바깥 영역인 전역에서 `var` 키워드로 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수를 전역 코드라고 부른다. 이렇게 전역에서 선언된 전역 코드는 JS 엔진에 의해 평가되어 전역 실행 컨텍스트를 생성하게 되고, 이를 통해 전역 변수와 전역 함수를 전역 객체 `window`의 프로퍼티와 메서드로 바인딩한다

  ```javascript
  // 1 전역 코드(전역에서의 소스코드) 입력
  var variable = 1; 
  
  function func() {
    console.log("global method");
  }
  
  // 2 전역 실행 컨텍스트 생성
  
  // 3 전역 객체 window의 프로퍼티와 메서드로 바인딩
  console.log(window.variable); // → 1
  console.log(window.func); // → ƒ func() { console.log("global method") }
  ```

- **함수 코드**

  함수 몸체 내부에서 변수 혹은 중첩된 함수가 선언되어 [지역 스코프](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Scope/scope.md)를 갖게되는 코드를 함수 코드라고 부른다. 지역 변수 혹은 지역 함수가 선언되어 JS 엔진에 의해 평가되면 함수 실행 컨텍스트를 생성하게 된다. 함수 실행 컨텍스트는 지역 스코프를 생성하고 지역 변수, 매개변수, [`argments` 객체]()를 관리하게 된다. 또한 지역 스코프를 전역 스코프 체인의 일원으로 연결하는 기능도 실행한다

  ```javascript
  // 1 전역 함수 입력(전역 스코프는 갖지 않는다)
  function func() {
    
    // 2 지역 변수 및 지역 함수 선언문 호이스팅
    // 3 함수 실행 컨텍스트 생성
   
    // 지역 변수
    var outer = "outer";
    console.log(outer); // → global outer
   
    // 지역 함수
    function innerFunc() {
      var inner = "inner";
      console.log(outer, inner); // → global outer inner
    }
    innerFunc();
  }
  
  func();
  ```

- **`eval` 코드**

  > 앞서 [빌트인 전역 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/built-in%20object.md#3-2-%EB%B9%8C%ED%8A%B8%EC%9D%B8-%EC%A0%84%EC%97%AD-%ED%95%A8%EC%88%98%EB%A9%94%EC%84%9C%EB%93%9C) 파트에서 `eval` 함수를 살펴봤다

  전역 객체의 `eval` 메서드와 해당 메서드에 문자열로 전달된 소스코드를 `eval` 코드라고 부른다. `eval` 코드는 JS 엔진에 의해 평가되면 스코프를 동적으로 변경하는 하는 자신만의 자체적인 스코프를 생성해야 하기 때문에, 이를 위해 `eval` 코드는 평가되어 `eval` 실행 컨텍스트를 생성하게 된다

  ```javascript
  const x = 1;
  
  function func() {
    // 원래 변수 선언문은 호이스팅 된다
    // 그러나 eval 코드는 평가되어 자체적인 스코프를 생성한다
    // 따라서 eval 코드 위 참조문의 x는 전역 변수를 참조한다
    console.log(x); // → 1
    eval("const x = 2");
    console.log(x); // → 1
  }
  
  func();
  console.log(x); // → 1
  ```

- **모듈 코드**

  > 모듈에 대해서는 [모듈]() 파트를 참고하자

  모듈은 하나의 `.js` 파일에 몰려 있는 JS 소스코드를 특정 기준에 따라 여러 파일에 나누어 작성하고 상호 연결을 통해 필요한 코드를 재사용할 수 있는 시스템을 의미한다. 분리된 모든 파일들은 서로 연결되어 있기 때문에 코드 중복의 문제들을 해결하기 위해 모듈별로 독립적인 모듈 스코프가 필요하다. 모듈 코드는 평가되면 모듈 실행 컨텍스트를 생성하게 되고, 이를 통해 모듈 스코프를 생성하게 된다

  ```javascript
  // index.js 파일
  function printHello {
    console.log("Hello");
  }
  
  // CommonJS 형태의 모듈 코드
  // 모듈 코드는 평가되어 모듈 실행 컨텍스트를 생성한다
  module.exports = {
    printHello
  };
  ```

  ```javascript
  // commonFunction.js 파일(공통적으로 자주 사용되는 함수를 따로 모은 파일)
  
  const func = require("./index.js")
  func.printHello(); // → Hello
  ```

### 1-2 소스코드의 평가와 실행

앞서 [표현식과 문](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/expression%20%26%20statement(feat.%20value%2C%20literal).md) 파트에서 일부 살펴본 내용으로 JS에서 소스코드는 JS 엔진에 의해 "평가"와 "실행"의 과정으로 나뉘어 실행된다

소스코드 평가 과정에서 위에서 언급한 4가지 타입의 실행 컨텍스트를 생성하고, 변수와 함수 등의 선언문을 먼저 실행하는 호이스팅, 그리고 생성된 식별자(변수, 함수, 클래스 등)를 [렉시컬 환경]()에 등록하여 키와 값의 형태로 관리한다

이러한 소스코드의 평가 과정이 완료되면 비로서 소스코드가 한 줄씩 순차적으로 실행되는 런타임이 시작된다. 그리고 JS 엔진은 런타임에 소스코드가 실행되므로 식별자와 연결된 정보(값)를 실행 컨텍스트가 관리하는 스코프에서 검색해서 취득한다. 그리고 소스코드의 실행 결과를 다시 실행 컨텍스트가 관리하는 스코프에 등록한다. 마치 변수의 동작 원리 파트에서 다뤘듯이 선언문 평가, 값의 할당, 변수의 참조 및 소스코드의 결과를 메모리에 재등록하는 과정과 유사하다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/414de7ff-fce6-4b52-b2ad-5ed2da166c3e" width="100%">

아래 예제를 살펴보자. 앞서 [값의 할당](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/assignment.md#1-1-%EA%B0%92%EC%9D%98-%ED%95%A0%EB%8B%B9%EA%B3%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC) 파트에서 언급했듯이 변수 선언과 값의 할당이 동시에 이뤄지는 변수 선언 및 할당문의 경우 하나의 코드 문장처럼 보이지만 JS 엔진은 이를 2개의 과정으로 나누어 처리한다고 했다

1. 변수 선언 `var x;`
2. 값의 할당 `x = 1;`  

```javascript
var x = 1; 
```

소스코드의 평가 및 실행 측면으로 바라보면 변수 선언과 할당이 왜 2단계로 나뉘어져 처리되는지 이해할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/08be76a7-54a9-4fa7-98e6-8aefa0b8344b" width="100%">

0. (종류별 실행 컨텍스트의 생성)

1. 소스코드의 평가 `var x;`

   전체 소스코드 중 식별자의 탐색과 호이스팅, 실행 컨텍스트의 스코프(렉시컬 환경)에 등록

2. 소스코드의 실행 `x = 1;`: 

   런타임에 값의 할당이 시작된다. `x` 변수가 선언된 변수인지 확인이 필요하기 때문에, JS 엔진은 (1) 실행 컨텍스트의 스코프에 `x` 변수가 등록되어 있는지 확인한다.  확인이 완료되면 값을 `x` 변수에 할당한다. 마지막으로 (3) 실행 결과를 다시 실행 컨텍스트에 등록한다

### 1-3 실행 컨텍스트의 역할

```javascript
const x = 1;
const y = 2;

function foo(z) {
  const x = 10;
  const y = 20; 
  
  console.log(x + y + z);
}

foo(100);

console.log(x + y); 
```



<br>

## 2 실행 컨텍스트 스택

<br>

## 3 렉시컬 환경

<br>

## 4 실행 컨텍스트의 생성과 식별자 검색 과정

<br>

## 5 실행 컨텍스트와 블록 레벨 스코프

<br>

***

### 참고

- [모던 자바스크립트 Deep Dive]()