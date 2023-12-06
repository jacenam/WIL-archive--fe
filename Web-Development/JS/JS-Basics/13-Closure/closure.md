# 클로저

클로저(Closure)는 하스킬(HasKell), 리스프(Lisp), 얼랭(Erinag), 스칼라(Scala) 등에서도 사용되는 개념이다. 그러나 JS에서의 클로저는 난해하기로 유명한 개념 중 하나이기 때문에 이해가 될 때까지 반복 학습할 예정이다

**Table of Contents**
- [클로저란](#클로저란)
<br>
## 클로저란

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
bar(); // → 1
```

이는 [렉시컬 스코프](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/09-Scope/01-scope-intro.md#렉시컬-스코프) 파트에서 살펴보았듯이, JS는 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프가 결정되는 렉시컬 스코프(정적 스코프) 방식을 따르기 때문이다. 다시 말해 함수가 호출된 위치는 상위 스코프를 가리는데에 어떠한 영향도 없다는 의미다
<br>
## 참고 

- [JavaScript MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
- [모던 JavaScript 튜토리얼 - 변수의 유효범위와 클로저](https://ko.javascript.info/closure)