# 실행 컨텍스트와 블록 레벨 스코프

**Table of Contents**

- [let, const 키워드와 블록 레벨 스코프](#let,-const-키워드와-블록-레벨-스코프)
  - [전역 객체 생성](#전역-객체-생성)
  - [전역 코드 평가](#전역-코드-평가)
  - [전역 코드 실행](#전역-코드-실행)
  - [코드 블록과 렉시컬 환경](#코드-블록과-렉시컬-환경)


<br>

## let, const 키워드와 블록 레벨 스코프

앞서 [`let`, `const` 키워드와 블록 레벨 스코프]()에서 살펴보았듯이, `let`, `const` 키워드로 선언한 변수는 모든 코드 블록(`if`문, `for`문, `while`문, `try`/`catch`문, 함수 등)을 지역 스코프로 인정한다. 반면 `var` 키워드로 선언한 변수는 함수의 코드 블록만 지역 스코프로 인정하는 방식의 함수 레벨 스코프를 따른다. 아래 예제를 살펴보자:

```javascript 
let x = 1;
const y = 2;
var z = 3;

if (true) {
  let x = 10;
  const y = 20;
  var z = 30;
	console.log(x, y, z); // → 10 20 30
}

console.log(x, y, z); // → 1 2 30
```

### 전역 객체 생성

앞서 [실행 컨텍스트와 식별자 검색 과정](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/03-execution-context--identifier.md#전역-객체-생성)에서 언급했듯이 전역 코드가 JS 엔진에 의해 평가 되기 전에 전역 객체 `window`가 디폴트로 생성된다. 전역 객체가 생성된 이후 전역 코드의 평가가 시작된다

### 전역 코드 평가

**1 전역 실행 컨텍스트 생성**

전역 코드가 평가되는 것이기 때문에 가장 먼저 전역 실행 컨텍스트가 생성되고 이는 실행 컨텍스트 스택에 추가된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a4884c3a-4119-4193-85c2-f022c1794e01" width="100%">

**2 전역 렉시컬 환경 생성**

전역 실행 컨텍스트의 전역 렉시컬 환경이 생성되며, 전역 렉시컬 환경은 전역 환경 레코드와 외부 렉시컬 환경에 대한 참조로 구성된다. 이때 전역 렉시컬 환경은 스코프 체인 최상위에 위치하기 때문에 외부 렉시컬 환경에 대한 참조는 `null`이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/6a3e84da-00d2-421a-acdb-721642674d2e" width="100%">

**2.1 전역 환경 레코드 생성**

스코프와 스코프 내에서 선언된 변수의 관리를 위해 전역 환경 레코드가 생성되며, 전역 환경 레코드는 객체 환경 레코드와 선언적 환경 레코드로 구성된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b3212dc3-a5ed-44ec-8375-7521401bb9d5" width="100%">

**2.1.1 객체 환경 레코드 생성**

객체 환경 레코드가 생성되며 이는 `var` 키워드로 선언한 전역 변수를 객체 환경 레코드의 속한 `BindingObject`라는 객체를 통해 관리한다. 현 시점은 코드 평가 단계이기 때문에 `var` 키워드로 선언된 `z` 전역 변수는 `undefined`를 할당받게 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f0d04aa1-c565-4e99-ad7b-2a9e8cbe30da" width="100%">

**2.1.2 선언적 환경 레코드 생성**

선언적 환경 레코드가 생성되며 이는 `var` 키워드로 선언한 전역 변수와 함수 선언문으로 정의한 전역 함수 이외의 모든 선언들을 관리한다. `x`, `y` 변수는 `let`, `const` 키워드로 선언되었으므로 선언 단계와 초기화 단계가 분리되어 진행되어 `<unintialized>`로 표현된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/8792c842-d379-495d-bb94-075db427f027" width="100%">

**2.2 this 바인딩**

전역 환경 레코드에 원래 포함되어 있는 `[[GlobalThisValue]]` 내부 슬롯은 전역 객체를 가리키므로, `this`는 전역 객체 `window`에 바인딩된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/55eb38dd-750b-4635-913f-4eb892db08d6" width="100%">

**2.2 this 바인딩**

**2.3 외부 렉시컬 환경에 대한 참조 결정**

현 시점은 전역 코드의 평가 시점으로 전역 코드를 포함하는 상위 스코프가 존재하지 않는다. 따라서 전역 렉시컬 환경의 외부 렉시컬 환경에 대한 참조는 `null`로 할당된다 

### 전역 코드 실행

전역 코드의 평가가 종료된 이후 전역 코드가 순차적으로 실행되는 런타임에 진입한다

```javascript
let x = 1;
const y = 2;
var z = 3;

if (true) {
  let x = 10;
  const y = 20;
  var z = 30;
	console.log(x, y, z);
}

console.log(x, y, z);
```

전역 코드가 순차적으로 실행되므로 먼저 식별자에 값이 할당된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/725b6e1e-05b5-4a6f-b950-6b0b06966f06" width="100%">

### 코드 블록과 렉시컬 환경

그리고 `if`문이 실행된다. 하지만 여기서 문제가 발생한다. `var` 키워드로 선언된 `z` 변수는 중복 선언이 가능하기 때문에 이미 전역 객체 `window`에 프로퍼티가 된 `z` 식별자에 값을 재할당 할 수 있다. 그러나 `let`, `const` 키워드로 선언한 `x`, `y` 변수는 전역 코드의 평가와 실행이 앞서 진행되었기 때문에 이미 선언적 환경 레코드에 등록된 상태다. 그렇다면 `if`문 코드 블록 내에서 `let`, `const` 키워드로 선언된 `x`, `y` 변수는 렉시컬 환경에 어떻게 등록되고 관리될까?

`if`문 내에서 `let`, `const` 키워드로 선언된 변수는 블록 레벨 스코프를 따른다. 따라서 `if`문 코드 블록을 위한 블록 레벨 스코프가 따로 생성되어 `if`문 코드 블록 내의 `x`, `y` 변수를 관리할 수 있어야 한다

이를 위해 선언적 환경 레코드를 갖는 "블록 레벨 렉시컬 환경"이라는 새로운 렉시컬 환경을 따로 생성하여 전역 실행 컨텍스트와 연결되는 기존의 전역 렉시컬 환경을 임시적으로 교체한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f31ba34d-6eec-469c-9fbe-89b5e49f72c8" width="100%">

그리고 블록 레벨 렉시컬 환경의 외부 렉시컬 환경에 대한 참조는 전역 렉시컬 환경을 가리킨다. 이는 `if`문 코드 블록의 코드가 실행되고 난 후 다시 전역 렉시컬 환경으로 되돌리기 위함이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/fd751cde-a2a2-46ae-952c-5b4d45a85cdf" width="100%">

`if`문 코드 블록 내의 모든 코드가 실행되고 난 뒤(`z` 변수에는 값이 재할당된다) 앞서 언급했던대로 외부 렉시컬 환경에 대한 참조를 통해 전역 렉시컬 환경으로 되돌아간다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e86798dd-51c1-40dd-87b9-19300e799fd5" width="100%">

블록 레벨 렉시컬 환경을 임시로 생성하는 방식은 `if`문 뿐만 아니라 모든 블록 레벨 스코프를 생성하는 블록문에 적용된다

<br>

## 참고

- [모던 자바스크립트 Deepdive]()