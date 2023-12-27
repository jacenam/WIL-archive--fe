# 클로저

클로저(Closure)는 하스킬(HasKell), 리스프(Lisp), 얼랭(Erinag), 스칼라(Scala) 등에서도 사용되는 개념이다. 그러나 JS에서의 클로저는 난해하기로 유명한 개념 중 하나이기 때문에 이해가 될 때까지 반복 학습할 예정이다

**Table of Contents**
- [클로저와 렉시컬 스코프](#클로저와-렉시컬-스코프)
- [렉시컬 스코프](#렉시컬-스코프)
- [함수 객체의 내부 슬롯 Environment](#함수-객체의-내부-슬롯-Environment)
- [클로저란](#클로저란)
- [클로저의 조건](#클로저의-조건)
- [클로저의 활용](#클로저의-활용)

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

## 클로저란

아래 예제를 살펴보자 

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

앞서 외부 함수보다 외부 함수 내부에서 선언된 중첩 함수가 더 오래 생존하는 경우, 해당 중첩 함수를 클로저라고 부른다고 했다. 그러나 모든 중첩 함수를 클로저라고 하지는 않는다. 클로저가 되기 위해서는 앞서 [클로저](#클로저란) 파트에서 언급한 조건을 충족해야 한다

- 외부 함수보다 중첩 함수의 생명주기가 더 길어야 한다
- 중첩 함수가 상위 스코프의 식별자를 참조해야 한다

**Case 1: 중첩 함수 생명주기 O, 상위 스코프 식별자 참조 X**

아래 예제를 살펴보자. `foo` 외부 함수의 호출 실행 결과로 `bar` 중첩 함수만을 `foo` 함수 외부로 반환한다. 그리고 반환된 `bar` 중첩 함수는 `baz` 변수에 할당되고, `baz` 함수가 호출되기 때문에 `bar` 중첩 함수의 렉시컬 환경을 계속해서 참조된다. 즉, `foo` 외부 함수보다 `bar` 중첩 함수가 더 오래 생존한다. 그러나 `bar` 중첩 함수는 상위 스코프(`foo` 외부 함수)의 어떠한 식별자도 참조하지 않는다. 따라서 `bar` 중첩 함수는 클로저라 정의할 수 없다 

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

const baz = foo(); 
baz();
```

그렇다면 위 예제 코드의 `bar` 중첩 함수를 클로저가 되게 하려면 어떻게 해야할까? `bar` 중첩 함수가 상위 스코프의 식별자를 참조하도록 변경하면 된다

```javascript
function foo() {
  const x = 1;
  const y = 2;

  function bar() {
    const z = 3;

    // 상위 스코프의 식별자를 참조하도록 변경
    console.log(x, y, z);
  }

  return bar;
}

const baz = foo();
baz();
```

실제로 웹 브라우저에서 디버거로 확인하면 `bar` 중첩 함수가 클로저가 되어 `foo` 외부 함수 내부에서 선언된 지역 변수 중 참조가 되는 변수들의 정보를 저장하고 있음을 알 수 있다

<img src = "https://github.com/jacenam/WIL-archive/assets/92138751/9c2f4aca-11d0-4531-8765-92f4cb64511a" width="100%">


**Case 2: 중첩 함수 생명 주기 X, 상위 스코프 식별자 참조 O** 

아래 예제에서는 `bar` 중첩 함수의 생명주기가 `foo` 외부 함수보다 길지 않지만, `bar` 중첩 함수 내부 코드가 상위 스코프의 식별자를 참조하고 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/c0139210-fa50-425e-82f3-d14407d2afb3" width="100%">


브라우저에서 디버깅으로 실행하면 `bar` 중첩 함수가 상위 스코프의 `x` 지역 변수를 참조하고 있기 때문에 클로저라고 나온다. 그러나 `bar` 중첩 함수가 `foo` 외부 함수보다 일찍 소멸되기 때문에 일반적으로 클로저라고 하지 않는다

<br>

## 클로저의 활용

그렇다면 클로저는 필요한 것일까? 언제 사용해야할까? 클로저는 데이터의 상태(State)을 안전하게 은닉(Information Hiding)하고 특정 함수만이 상태 변경이 가능하도록 하기 위해 사용된다

아래 예제를 살펴보자. 함수가 호출될 때마다 `num` 변수의 변수 값을 `1`만큼 증가시키는 카운터 함수다. 그러나 아래 카운터 함수는 오류가 발생할 가능성이 있다. 카운터 함수는 아래 조건이 지켜져야한다. 그러나 `num` 변수는 `let` 키워드로 선언된 전역 변수로서 의도치 않게 동일한 변수 값이 변경될 가능성이 있기 때문이다

- `num` 변수 값이 `increase` 함수가 호출되기 전까지 변경되지 않아야 한다. 그래야 `increase` 함수가 호출되었을 때 정확한 카운트가 가능하다
- 따라서 `increase` 함수만이 `num` 변수의 카운트 값을 변경할 수 있어야 한다

```javascript
let num = 0;

function increase() {
  return ++num;
}

console.log(increase()); // → 1
console.log(increase()); // → 2
console.log(increase()); // → 3
```

따라서 위 예제 코드에 오류가 발생할 가능성을 개선하기 위해 `num` 변수를 아래와 같이 지역 변수로 변경해볼 수 있다. 이제 `num` 변수의 상태는 `increase` 함수만이 변경 가능하다. 그러나 `num` 지역 변수 값이 `1`만큼 증가하는 카운터의 기능을 수행하지 못한다. 이는 `increase` 함수가 호출될 때마다 `num` 지역 변수는 다시 선언되어 `0`으로 초기화되기 때문이다

```javascript
function increase() {
  let num = 0; 

  return ++num; 
}

console.log(increase()); // → 1
console.log(increase()); // → 1
console.log(increase()); // → 1
```

이런 상황에서 클로저를 활용해볼 수 있다. `increase` 함수 호출되었을 때 실행 결과로 `num` 지역 변수를 선언하고 `closure` 함수를 반환한다. 그리고 `increase` 함수의 호출 결과를 `result` 변수에 할당한다. 이에 `result` 변수를 함수로 호출할 때마다 증가 연산자가 실행되어 `num` 지역 변수 값을 `1`씩 증가시키는 것이다

```javascript
function increase() {
  let num = 0;

  function closure() {
    return ++num;
  };
  
  return closure;
}

const result = increase();

console.log(result()); // → 1
console.log(result()); // → 2
console.log(result()); // → 3
```

위 예제는 아래와 같이 즉시 실행 함수를 활용하여 다듬을 수 있다. `increase` 변수에 할당될 즉시 실행 함수는 JS 엔진에 의해 평가되어 바로 실행된다. 그리고 실행 결과로 즉시 실행 함수의 내부 코드인 `num` 지역 변수 선언과 클로저(중첩 함수)를 반환이 이뤄진다. 그리고 즉시 실행 함수는 소멸되고 `increase` 변수에 클로저가 할당된다. 즉, `increase()` 호출문이 호출되었을 때 `increase` 변수에 할당된 클로저가 실행되는 것이다. 클로저가 호출될 때 자신이 정의된 위치(즉시 실행 함수 내부)의 렉시컬 환경을 기억한다. 따라서 이미 소멸된 즉시 실행 함수의 `num` 지역 변수를 언제든지 참조할 수 있다. `num` 지역 변수는 `let` 키워드로 선언된 변수이므로 값의 재할당이 가능하기 때문에 클로저를 통해 가장 적합한 카운트 함수를 만들 수 있는 것이다 

>[즉시 실행 함수](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/08-Function/Function-Types/01-immediately-invoked-function.md)는 값으로 평가가 가능한 표현식이므로 변수에 할당할 수 있다. 그리고 즉시 실행 함수는 이름 그대로 평가되는 즉시 실행되는 함수다

```javascript
const increase = (function() {
  let num = 0;

  return function() {
    return ++num
  }
}());

console.log(increase()); // → 1
console.log(increase()); // → 2
console.log(increase()); // → 3
```

앞서 언급했듯이, 이처럼 클로저는 상태가 의도치 않게 변경되지 않도록 은닉하고 특정 함수만이 상태 변경이 가능하도록 사용된다. `num` 변수는 외부에서 접근(참조)이 불가능하기 때문에 은닉된 변수로서 의도치 않은 상태 변경에 대한 걱정할 필요가 없다

이러한 클로저의 특성을 활용하면 증감 카운트가 가능한 함수를 아래와 같이 구현할 수 있다

```javascript
const counter = (function() {
  let num = 0;

  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
}());

console.log(counter.increase()); // → 1
console.log(counter.increase()); // → 2

console.log(counter.decrease()); // → 1
console.log(counter.decrease()); // → 0
console.log(counter.decrease()); // → -1
```

아래 즉시 실행 함수는 JS 엔진에 의해 런타임에 실행되어 `num` 지역 변수를 선언하고 객체를 반환한다. 이때 반환되어 `counter` 변수에 할당되는 객체는 메서드다. 그리고 객체 리터럴의 중괄호(`{}`)는 코드 블록(함수 코드 블록 혹은 제어문의 코드 블록)이 아니므로 별도의 스코프를 따로 생성하지 않는다. 즉, 객체 리터럴이 평가되어 생성되는 객체에 대해서는 별도의 렉시컬 환경이 생성되지 않는다는 의미다. 전역 실행 컨텍스트 → 전역 렉시컬 환경 → 선언적 환경 레코드에 `counter` 식별자가 등록되며, 이는 `counter` 식별자가 전역 스코프를 갖는다는 의미다

>[실행 컨텍스트와 블록 레벨 스코프](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/04-execution-context--block-level-scope.md) 파트에서 살펴보았듯이, 코드 블록 내에 블록 레벨 스코프를 따르는  `let`, `const` 키워드로 선언된 지역 변수가 존재한다면 지역 변수의 관리를 위해 임시적으로 "블록 레벨 렉시컬 환경"이 따로 생성되어 실행 컨텍스트와 연결되는 렉시컬 환경을 임시적으로 교체한다. 위 예제의 `counter` 변수에는 객체가 할당되므로 블록 레벨 렉시컬 환경이 따로 생성되지는 않는다

그리고 위 예제의 `increase`, `decrease` 메서드는 함수 객체이므로 함수 객체와 동일한 방식으로 렉시컬 환경이 생성된다. 함수 객체는 JS 엔진에 의해 런타임 이전에 함수 객체가 평가되는 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경에 등록되어 관리된다. 따라서 전역 코드인 즉시 실행 함수가 실행되는 시점에 실행 컨텍스트인 전역 실행 컨텍스트의 렉시컬 환경에 등록된다. 
<br>

## 참고 

- [JavaScript MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
- [모던 JavaScript 튜토리얼 - 변수의 유효범위와 클로저](https://ko.javascript.info/closure)