# 함수 호출

**Table of Contents**

- [함수 호출](#함수-호출)
- [매개변수와 인수](#매개변수와-인수)
  - [인수 확인](#인수-확인)
  - [부적절한 함수 호출의 방지](#부적절한-함수-호출의-방지)
- [반환문](#반환문)

***

<br>

## 함수 호출

함수를 가리키는 식별자(함수 몸체 내에서만 유효한 함수 이름 또는 함수 값이 저장된 메모리에 접근하게 해주는 식별자)와 함수 호출 연산자(한 쌍의 소괄호, `()`)로 함수를 호출한다. 함수가 호출되면 매개변수에 인수가 순서대로 할당되고 함수 몸체의 블록문들이 실행된다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207501691-305277a1-0dfd-45cf-a1ed-039ef5e60eb1.png">

<br>

## 매개변수와 인수

함수를 실행(호출)했을 때 정해진 명령(블록문)을 통해 결과(output)를 반환하려면 함수 외부에서 함수 내부로 필요한 값(input)을 전달해야 한다. 이 역할을 매개변수(Parameter, 인자)와 인수(Argument)가 수행한다. 매개변수는 함수 몸체 내부에서 변수와 동일하게 취급된다. 따라서 인수는 함수 몸체 내부의 변수인 매개변수에 할당해줄 값이라고 생각하면 된다. 인수는 함수를 호출할 때 지정한다 

```javascript
function sum(x, y) {
  return x + y; 
} 

var result = sum(3, 5); 

console.log(sum(3,5)); // → 8
console.log(result); // → 8
```

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207501949-1bc4520d-2a62-4383-89be-70de9b1beee6.png">

함수 이름과 마찬가지로, 매개변수는 함수 몸체 내부에서만 참조할 수 있고 함수 몸체 외부에서는 참조할 수 없다. 다시 말해, 매개변수의 [스코프]()(유효 범위)는 함수 내부다

```javascript
// 함수 이름은 함수 몸체 내부에서만 참조 가능
var add = function sum(x, y) {
  return x + y; 
}

sum(2, 5); // → Uncaught ReferenceError: sum is not defined

// 매개변수도 함수 몸체 내부에서만 참조 가능
function sum(x, y) {
  return x + y;
}

console.log(x, y); // → Uncaught ReferenceError: x is not defined

// 매개변수의 스코프에 대한 다른 예제 
var sum = function(x, y) {
  return x + y; 
}

var x = 10; 
var y = 20; 

console.log(sum(x, y)); // → 30
console.log(sum(30, 40)); // → 70
console.log(x, y); // → 10 20
```

<br>

앞서 [매개변수는 없을 수도 있고 한 개 이상일 수도 있다]() 했다. 매개변수가 한 개 이상일 때 함수 호출 시 지정한 인수가 순서대로 할당된다고도 했다. 함수 호출 시 매개변수에 전달해야 할 인수의 순서를 고려해야 되기 때문에, 매개변수가 많아질수록 함수의 사용법을 이해하기 어렵게 만들고 실수를 발생시킬 가능성을 높인다. 따라서 매개변수는 최대한 적을수록 좋다. 이상적인 함수는 한 가지 일만 하는 함수이므로, 함수는 가급적 작게 만들어야 한다

```javascript
function sum(x, y, z) {
  return x + y + z; // x에 1, y에 3, z에 2가 순서대로 할당된다
} 

console.log(sum(1, 3, 2)); // → 6 
```

매개변수의 개수와 인수의 개수가 일치하지 않아도 된다. 함수를 호출할 때 매개변수의 개수에 상응하는 만큼 인수를 전달하는 것이 일반적이다. 그러나 그렇지 않은 경우에도 에러가 발생하지는 않는다. 인수의 개수가 적어서 할당받지 못한 매개변수의 값은 `undefined`다. 아래 예제에서는 함수 몸체 내 반환문 `x + y + z`의 매개변수에 인수로 전달받은 값 `3`, `5`가 각각 순서대로 할당된다. 그렇다면 반환문은 `3 + 5 + undefined`로 `NaN`이 반환된다

```javascript
function sum(x, y, z) {
  return x + y + z; 
} 

sum(3, 5); // → NaN
```

반대로 매개변수의 개수가 인수의 개수보다 더 적은 경우 초과된 인수는 무시된다

```javascript
function sum(x, y, z) {
  return x + y + z; 
}

sum(3, 5, 7, 9); // → 15
```

단, 초과된 인수가 그냥 버려지는 것은 아니다. 모든 인수는 암묵적으로 [`arguments` 객체]()의 프로퍼티로서 보관되기 때문이다. `arguments` 객체에 대해서는 추후 자세히 다룰 예정이다

```javascript
function sum(x, y, z) {
  console.log(arguments); // → ▸Arguments(4) [3, 5, 7, 9, callee: ƒ, Symbol(Symbol.iterator): ƒ]
  return x + y; 
} 

sum(3, 5, 7, 9); // → 15
```

### 인수 확인

아래 코드의 의도는 2개의 숫자 타입인 인수를 매개변수에게 전달해서 그 합계를 반환하려는 것이다. 하지만 사람의 의도와는 다르게, 프로그램상으로는 매개변수가 어떤 타입의 인수를 전달받아야 하는지, 어떤 타입의 값을 반환할지 명확하지 않다. 어떠한 타입의 값이든, 값으로 평가가 가능한 표현식이 인수로 지정된다면 아무런 문제 없이 코드는 실행된다

```javascript
function sum(x, y) {
  return x + y; 
} 

// 원래의 의도(2개의 숫자 타입을 인수로 전달받는 것
sum(3, 5);

// 매개변수 타입, 값의 반환이 명확하게 지정되지 않을 때
sum(2); // → NaN 
sum("a", "b"); // → 'ab'
```

이러한 상황이 발생한 이유는 무엇일까:

- (앞서 언급한대로) JS 함수에서 매개변수와 인수의 개수가 일치하는지는 상관없다
- JS 동적 타입의 언어다. 따라서 JS 함수에서 매개변수의 타입을 사전에 지정할 수 없다

따라서 JS의 경우 함수 정의 시 적절한 인수가 매개변수에게 전달되는지 명시적으로 확인할 필요가 있다

```javascript
function sum(x, y) { 
  if (typeof x !== "number" || typeof y !== "number") {
    throw new TypeError("인수는 모두 숫자 값이어야 합니다"); 
  }
  return x + y; 
}

console.log(sum(3, 5)); // → 8
console.log(sum(3)); // → Uncaught TypeError: 인수는 모두 숫자 값이어야 합니다
console.log(sum("a", "b")); → Uncaught TypeError: 인수는 모두 숫자 값이어야 합니다
```

그러나 바로 위의 예제 코드처럼 매개변수에 적절한 인수가 전달되었는지 확인이 가능하더라도, 에러가 발생하는 ‘부적절한’ 호출을 여전히 방지할 수는 없다. 따라서 인수를 코드가 실행되는 시점인 런타임 이전에 미리 확인함으로써 의도와는 다른 ‘부적절한’ 함수의 호출을 방지할 방법이 필요하다

### 부적절한 함수 호출의 방지

이에 대해서는 아래와 같은 방법들이 존재한다(지금은 단축 평가를 통해 매개변수에 기본 값을 할당하는 방법만을 자세히 살펴볼 것이다:

- [타입스크립트]()와 같은 정적 타입의 선언을 통한 컴파일 시점에 부적절한 호출 방지
- `arguments` 객체를 통한 인수의 개수 확인
- (인수가 전달되지 않은 경우) 단축 평가를 통해 매개변수에 기본 값을 할당

먼저 매개변수의 기본 값을 살펴보자. 매개변수 기본 값은 ES6에서 도입된 개념으로, 매개변수에 인수를 전달하지 않았을 경우와 `undefined`를 전달한 경우에만 활용 가능하다

```javascript
function sum(x = 0, y = 0, z = 0) {
  return x + y + z; 
} 

console.log(sum(3, 5, 7)); // → 15
console.log(sum(3, 5)); → 8
console.log(sum()); → 0
console.log(sum(undefined)); → 0
```

매개변수 기본 값의 설정을 활용한 예제를 살펴보자

```javascript
var msg; 

function sayHello(name = "Unnamed User") {
 msg = `Hello, ${name}`; 
 console.log(msg); 
}

sayHello("Jace"); // → Hello, Jace
sayHello(); // → Hello, Unnamed User
```

위 예제에서 함수 호출 시 매개변수 name에 인수가 전달되지 않았을 경우, 문자열 “Unnamed User”를 매개변수의 기본 값이 되도록 설정한 것이다

매개변수 기본 값에 대해 간단히 알아봤으니, 여기에 단축 평가를 더해서 부적절한 함수의 호출을 사전에 방지하는 방법을 살펴보자

```javascript
function sum(x, y, z) {
 x = x || 0; 
 y = y || 0; 
 z = z || 0; 
 return x + y + z;
}

console.log(sum(3, 5, 7)); // → 15
console.log(sum()); // → 0; 
```

<br>

## 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
