# 클로저

클로저(Closure)는 하스킬(HasKell), 리스프(Lisp), 얼랭(Erinag), 스칼라(Scala) 등에서도 사용되는 개념이다. 그러나 JS에서의 클로저는 난해하기로 유명한 개념 중 하나이기 때문에 이해가 될 때까지 반복 학습할 예정이다

**Table of Contents**
- [클로저와 렉시컬 스코프](#클로저와-렉시컬-스코프)
- [렉시컬 스코프](#렉시컬-스코프)
- [함수 객체의 내부 슬롯 Environment](#함수-객체의-내부-슬롯-Environment)
- [클로저](#클로저)
- [클로저의 조건](#클로저의-조건)

<br>

## 클로저와 렉시컬 스코프

["클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다"](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)

클로저는 MDN에서 위와 같이 정의된다. 단 번에 이해하기 어려운 정의인만큼 예제를 통해 클로저에 대해 살펴보자

아래는 중첩 함수다. `outerFunc` 함수 내부에서 `innerFunc` 함수가 정의되고 호출되었다. `outerFunc`의 함수 환경 레코드에 `innerFunc` 함수가 등록되고 관리되며, `innerFunc`의 외부 렉시컬 환경에 대한 참조는 상위 스코프인 `outerFunc`을 가리킨다. 따라서 `innerFunc` 함수 내부에 선언되지 않은 `x` 식별자를 상위 스코프에서 탐색하고 반환할 수 있게 된다.

```javascript
const x = 1;

function outerFunc() {
  const x = 10;
  
  function innerFunc() {
    console.log(x); // → 10
  }
  innerFunc();
}

outerFunc();
```

아래 예제를 다시 살펴보자. 이번에는 `bar` 함수가 `foo` 함수 내부에서 정의된 중첩 함수가 아닌 모두 전역에서 정의된 함수다. 이런 경우 `bar` 함수를 `foo` 함수 내부에서 호출한다 해도 `foo` 함수 내부에서 선언된 변수에는 접근할 수 없게 된다

```javascript
const x = 1;

function foo() {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // → 1
```

이는 [렉시컬 스코프](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/09-Scope/01-scope-intro.md#렉시컬-스코프) 파트에서 살펴보았듯이, JS는 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프가 결정되는 렉시컬 스코프(정적 스코프) 방식을 따르기 때문이다. 다시 말해 함수가 호출된 위치는 상위 스코프를 가리는데에 어떠한 영향도 없다는 의미다

<br>

## 렉시컬 스코프

아래는 렉시컬 스코프 파트에서 살펴본 예제다. 아래 두 함수가 모두 실행되었을 때 참조문의 결과는 어떠할까?

```javascript
const x = 1; 

function foo() {
	const x = 10; 
	bar();
}

function bar() {
	console.log(x);
}

foo(); // ? 
bar(); // ?
```

위 예제의 `foo` 함수와 `bar` 함수는 모두 전역에서 정의되었다. 앞서 언급했듯이, 함수를 어디서 호출했는지가 아닌 어디에서 정의했는지에 따라 상위 스코프의 관계가 결정된다. 따라서 `foo` 함수와 `bar` 함수의 상위 스코프는 모두 전역 스코프다

```javascript
// 전역에서 선언된 전역 변수는 전역 스코프를 가진다
// 전역 스코프는 스코프 체인상 종점에 위치한다 
const x = 1; 

// 전역에서 정의된 foo 전역 함수는 전역 스코프를 가진다
// 따라서 전역 함수의 상위 스코프는 존재하지 않는다
function foo() {
	const x = 10; 
	bar();
}

// 전역에서 정의된 bar 함수도 마찬가지로 전역 스코프를 가진다
function bar() {
	console.log(x);
}

foo();
bar();
```

그리고 앞서 [실행 컨텍스트와 식별자 검색 과정 파트](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/03-execution-context--identifier.md#innerfunc-함수-코드-실행)에서 살펴보았듯이 `bar` 함수 내부의 `console.log(x)` 참조문을 실행하기 위해 `console.log` 메서드에 인수로 전달된 `x`를 평가하기 위해 `x` 변수(식별자)를 스코별로 검색하게 된다. 이때 `bar` 함수는 `foo` 함수 내부에서 호출되었지만 전역에서 정의되었기 때문에 전역 스코프를 가진다. 그리고 `bar` 함수의 렉시컬 환경의 외부 렉시켤 환경에 대한 참조 값은 `null`(없음)이다. 즉, `bar` 함수는 스코프 종점에 위치하기 때문에 상위 스코프가 존재하지 않는다는 것을 의미한다

[실행 컨텍스트 스택과 렉시컬 환경](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/02-stack-lexical.md#렉시컬-환경) 파트에서 살펴보았듯이, 스코프 체인은 상위 방향으로만 이루어진 단방향 [린크드 리스트인]() 자료구조의 형태를 띄고 있어 하위 스코프의 방향으로 식별자 검색 과정이 이뤄지지 않는다. 따라서 `bar` 함수 내부의 `console.log(x)` 참조문의 `x` 변수(식별자)는 전역 스코프에서만 참조가 가능하다. 그렇게 `x` 변수에 대한 참조문은 전역 변수 `x`의 값인 `1`을 참조하게 된다

```javascript
const x = 1; 

function foo() {
	const x = 10; 
	bar();
}

function bar() {
	console.log(x);
}

foo(); // → 1 
bar(); // → 1
```

만약 `foo` 함수 내부에서 선언된 지역 변수 `x`의 값 `10`을 `bar` 함수에서 참조하려면 지금까지 살펴보았던대로 `foo` 함수 내부에 `bar` 함수가 지역 함수로서 정의되어야 한다

```javascript
const x = 1;

function foo() {
	const x = 10;

	function bar() {
		// bar 함수의 상위 스코프를 가진 것은 foo 함수다
		// 따라서 상위 스코프 체인의 방향에 따라 아래 참조문의 x 식별자는 foo 함수의 지역 변수를 참조할 수 있게 된다
		console.log(x);
	}
	bar();
}

foo(); // → 10
```

정리하자면 [실행 컨텍스트](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/01-execution-context.md) 파트에서 살펴보았듯이 소스코드 타입별로 실행 컨텍스트가 생성이 되고, 각각의 실행 컨텍스트는 렉시컬 환경을 가진다. 또한, 렉시컬 환경마다 외부 렉시컬 환경에 대한 참조를 통해 상위 스코프에 대한 참조 정보를 갖게된다. 함수의 경우 함수가 어디서 정의되었는지에 따라 함수가 평가되어 함수 실행 컨텍스트와 함수 렉시컬 환경이 생성되는 시점에 외부 렉시컬 환경에 대한 참조 정보가 결정된다. 이는 다시 말해 함수가 정의된 환경(위치)에 따라 상위 스코프가 결정된다는 것을 의미한다

<br>

## 함수 객체의 내부 슬롯 Environment

앞서 함수는 자신이 정의된 위치에 따라 스코프가 결정된다고 했다. 함수는 자신의 내부 슬롯 `[[Environment]]`에 함수 자신이 정의된 환경(위치), 즉 상위 스코프의 참조 정보를 저장한다. 아래는 앞서 살펴본 함수 예제다

```javascript
const x = 1; 

function foo() {
	const x = 10; 
	bar();
}

function bar() {
	console.log(x);
}

foo(); // → 1 
bar(); // → 1
```

위 함수 예제에서의 실행 컨텍스트와 렉시컬 환경은 아래 그림과 같이 나타낼 수 있다. 여기서 자세히 살펴봐야할 부분은 `foo`와 `bar` 함수 객체의 `[[Environment]]` 내부 슬롯이 전역 실행 컨텍스트에 대한 참조 정보를 저장한다는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a978bcc2-8604-4dd9-bd1d-6ce18f1bf5a9" width="100%">

`foo`와 `bar` 함수 모두 전역에서 정의된 함수로서 JS 엔진에 의해 런타임 이전에 호이스팅 식별자 정보가 실행 컨텍스트의 렉시컬 환경에 등록되며 함수 객체를 생성한다. 이때 생성된 함수 객체는 `[[Environment]]` 내부슬롯을 갖게 된다. 그리고 함수 객체가 평가되어 생성되는 시점에 환경(위치) 정보를 `[[Environment]]` 내부 슬롯에 저장하게 된다. 그리고 이후 함수의 렉시컬 환경이 생성되고 외부 렉시컬 환경에 대한 참조가 구성될 때 함수 객체의 `[[Environment]]` 내부 슬롯에 저장된 렉시컬 환경의 참조 정보가 할당되는 것이다. 이를 통해 외부 렉시컬 환경에 대한 참조는 상위 스코프를 가리키며 렉시컬 스코프와 스코프 체인을 형성하게 되는 것이다

<br>

## 클로저

아래 예제를 살펴보자 
```javascript
const x = 1;

function outer() {
	const x = 10;

	function inner() {
		console.log(x)
	}
}

foo();
bar();
```

```javascript
const x = 1;

function outer() {
	const x = 10;

	function inner() {
		console.log(x);
	}
}

foo();
bar();
```

```javascript
const x = 1;

function outer() {
	const x = 10;

	function inner() {
		console.log(x);
	}
}

foo();
bar();
```

```javascript
const x = 1;
function outer() {
	const x = 10; 
	
	function inner() { 
		console.log(x);
	}
	
}


```

```javascript
const x = 1; 

function outer() {
	const x = 10;
	const inner = function() { console.log(x) };
	return inner;
}

const innerFunc = outer();

// 결과는 어떠할까
innerFunc(); // ?
```

전역에서 `outer` 함수가 정의된 것을 확인할 수 있다. 따라서 `outer` 함수의 내부 코드들을 제외한 함수 객체의 관리를 위해 전역 실행 컨텍스트가 생성된다. `outer` 함수가 호출되면 `outer` 함수의 내부 코드들이 실행되며 반환문(`return`)에 따라 `outer` 함수의 실행이 종료되면 `outer` 함수의 전역 실행 컨텍스트는 스택에서 제거(Pop)된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a59810b9-4e58-42d4-abc5-acc30239485e" width="100%">

다시말해 `outer` 함수의 생명주기가 종료되는 것을 의미하며, `outer` 함수 내부에서 선언된 지역 변수 `x`와 변수 값 `10`을 관리하던 실행 컨텍스트가 제거되었으므로 지역 변수는 모두 유효하지 않게 되어 `x` 지역 변수에 접근할 방법이 없어 보인다. 그러나 코드의 실행 결과는 `outer` 함수 내부에서 선언된 `x` 지역 변수의 값 `10`이다. 즉, 이미 종료된 `outer` 함수의 `x` 지역 변수가 다시 동작하고 있다는 의미다. 이런 현상이 어떻게 가능한 것일까?

- (앞서 [실행 컨텍스트와 식별자 검색 과정](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/03-execution-context--identifier.md#outerFunc-함수-코드-실행-종료) 파트에서 언급했듯이) 코드의 실행이 종료되어 실행 컨텍스트가 실행 컨텍스트 스택에서 제거되었다고 해도 렉시컬 환경은 독립적인 객체로서 누군가에게 참조되고 있다면 제거되지 않는다
	```javascript
	const x = 1; 

	function outer() {
		// x 지역 변수, inner 함수는 outer 함수의 렉시컬 환경에 등록된다
		const x = 10;
		const inner = function() { console.log(x) };
		// outer 함수 외부에서 inner를 참조할 수 있도록 함수 밖으로 반환한다
		return inner;
	}

	// outer 함수 내부에서 정의된 inner 함수를 참조하여 inner 함수의 실행 결과를 innerFunc 변수에 할당한다
	// 즉, outer 함수의 렉시컬 환경에 등록된 식별자를 계속해서 참조 중이므로 outer 함수가 종료되어도 렉시컬 환경이 당장 사라지지 않는다
	const innerFunc = outer();

	innerFunc(); // ?
	```
- 따라서 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 이미 생명 주기가 종료된 외부 함수의 변수를 참조할 수 있게 된다
	<img src="https://github.com/jacenam/WIL-archive/assets/92138751/4776d510-c7b7-4602-a312-9bb8363eaf7c" width="100%">

메모리에서 특정 데이터를 해제하고 삭제하는 가비지 컬렉터는 누군가에게도 더 이상 참조되지 않는 데이터를 메모리에서부터 해제한다. 비록 `outer` 함수 실행 컨텍스트가 실행 컨텍스트 스택에서 제거되었지만, 전역에서 선언된 `innerFunc` 변수는 변수 값으로 함수 객체인 `inner` 함수를 참조하고 있으므로 가비지 컬렉터의 해제 대상이 되지 않는 것이다

마지막 전역 코드인 `innerFunc()` 함수 호출문이 실행되면 `inner` 함수를 실행하기 위해 `inner` 함수의 실행 컨텍스트가 실행 컨텍스트 스택에 추가(Push)된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/afb80cc4-4ae9-4780-ad04-0538a25b86c5" width="100%">

이처럼 외부 함수(`outer` 함수)보다 중첩 함수(`inner` 함수)가 더 오래 생존하는 중첩 함수를 클로저(Closure)라 부른다. 모든 함수는 자신이 정의된 환경(위치)에 따른 정보를 `[[Environment]]` 내부 슬롯에 저장하며, 이는 각 함수 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 할당된다. 따라서 특정 함수의 상위 스코프(실행 컨텍스트)가 종료되어 실행 컨텍스트 스택에서 제거되어도, 여전히 스코프 체인상에 존재하는 렉시컬 환경의 식별자들을 참조할 수 있는 메커니즘이 가능해진다

<br>

## 클로저의 조건

앞서 외부 함수보다 외부 함수 내부에서 선언된 중첩 함수가 더 오래 생존하는 경우, 해당 중첩 함수를 클로저라고 부른다고 했다. 그러나 모든 중첩 함수를 클로저라고 하지는 않는다. 아럐 예제를 살펴보자

```javascript
function foo() {
	const x = 1;
	const y = 2;

    function bar() {
        const z = 3;
        
        console.log(z);
    }

    return bar; 
}

const bar = foo(); 
bar();
```


<br>

## 참고 

- [JavaScript MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
- [모던 JavaScript 튜토리얼 - 변수의 유효범위와 클로저](https://ko.javascript.info/closure)