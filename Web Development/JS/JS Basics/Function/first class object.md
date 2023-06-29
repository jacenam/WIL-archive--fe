# 함수와 일급 객체

### 목차

- [1 자바스크립트 함수와 일급 객체](#1-자바스크립트-함수와-일급-객체)
  - [1-1 무명의 리터럴로 생성할 수 있는 객체 값](#1-1-무명의-리터럴로-생성할-수-있는-객체-값)
  - [1-2 변수나 자료구조에 할당할 수 있는 객체 값](#1-2-변수나-자료구조에-할당할-수-있는-객체-값)
  - [1-3 함수의 매개변수에 전달할 수 있는 객체 값](#1-3-함수의-매개변수에-전달할-수-있는-객체-값)
  - [1-4 함수의 반환값으로 사용할 수 있는 객체 값](#1-4-함수의-반환값으로-사용할-수-있는-객체-값)
  - [1-5 호출할 수 있는 객체 값](#1-5-호출할-수-있는-객체-값)

- [2 함수 객체](#2-함수-객체)
- [3 함수 객체의 데이터 프로퍼티](#3-함수-객체의-데이터-프로퍼티)

<br>

## 1 자바스크립트 함수와 일급 객체

앞서 함수는 객체이면서 일급 객체라 했다. 일반 객체와 일급 객체는 무엇이 다를까? 일급 객체란 객체이면서 아래 조건들을 모두 만족해야 한다: 

1. 무명의 리터럴로 생성할 수는 객체 값 있어야 한다
2. 변수나 자료구조(객체, 배열 등)에 할당할 수 있는 객체 값이어야 한다
3. 함수의 매개변수에 전달할 수 있는 객체 값이어야 한다
4. 함수의 반환값으로 사용할 수 있는 객체 값이어야 한다
5. **호출할 수 있는 객체 값이어야 한다**

일반 객체와 일급 객체인 함수는 위 1-4번 조건을 모두 동일하게 충족한다. 그러나 일반 객체는 5번 조건, 다시 말해 호출할 수 없는 객체 값이다. 호출할 수 있는 객체는 함수가 유일하므로 JS에서 일급 객체는 함수만을 지칭한다. 각 조건별로 일반 객체와 함수의 차이를 살펴보자:

### 1-1 무명의 리터럴로 생성할 수 있는 객체

앞서 [리터럴](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/expression%20%26%20statement(feat.%20value%2C%20literal).md#2-%EB%A6%AC%ED%84%B0%EB%9F%B4) 파트에서 언급했듯이, 모든 객체 값은 리터럴로서 표현될 수 있다

```javascript
// 일반 객체 리터럴
{ name: "Jace", age: "31" };
[1, 2, 3];

// 함수 리터럴
function() {};
```

함수 또한 리터럴로서 생성할 수 있는 객체 값이며, 다른 객체와도 동일하게 무명의 리터럴 형태로서 객체 값인 함수를 생성할 수 있다. 따라서 일반 객체와 함수 모두 일급 객체로서의 첫 번째 조건을 충족한다

```javascript
function() {
  console.log("함수 무명의 리털럴로 생성할 수 있는 객체다")
}
```

### 1-2 변수나 자료구조에 할당할 수 있는 객체

일반 객체는 변수 혹은 객체 자료구조에 할당하거나 저장할 수 있다. 즉, 객체의 프로퍼티가 되거나 배열의 요소가 될 수 있다는 의미다

```javascript
// 객체를 변수에 할당할 수 있다
const userInfo = {
  name: "Jace",
  age: 31
};

// 객체를 일반 객체 형태의 자료구조에 할당할 수 있다
const userInfo = {
  name: "Jace",
  age: 31
};

const array = [userInfo, 2, 3, "apple"];
console.log(typeof array[0], array[0]); // → object { name: 'Jace', age: 31 }
```

함수 또한 객체 값이기 때문에 **무명의 함수 리터럴로 생성한 함수**를 변수 혹은 객체 자료구조에 할당하여 저장할 수 있다. 따라서 일반 객체와 함수 모두 일급 객체로서의 두 번째 조건을 충족한다

```javascript
// 함수를 변수에 할당할 수 있다
const sayHello = function(name) {
  console.log(`Hello, ${name}`);
}

// 함수를 일반 객체 형태의 자료구조에 할당할 수 있다
const userInfo = {
  name: "Jace",
  age: 31,
  sayHello: function() {
    console.log(`Hello, ${this.name`}); 
  }
};
```

### 1-3 함수의 매개변수에 전달할 수 있는 객체 값

일반 객체는 함수의 매개변수에 전달하여 값으로 활용이 가능하다. 즉, 함수 호출 시 인수로 지정이 가능한 값으로 활용이 가능하다는 의미다

```javascript
function printMsg(userInfo) {
  const name = userInfo.name;
  const age = userInfo.age;

  console.log(`Hello, ${name} whose age is ${age}!`);
}

printMsg((userInfo = { name: "Jace", age: 31 })); // → Hello, Jace whose age is 31!
```

함수 또한 객체 값으로서 함수의 매개변수에 전달하여 활용이 가능하다. 따라서 일반 객체와 함수 모두  일급 객체로서의 세 번째 조건을 충족한다

```javascript
function welcomeMsg(name, age) {
  return `Hello, ${name} whose age is ${age}`;
}

function printMsg(welcomeMsg, userName, userAge) {
  console.log(welcomeMsg(userName, userAge));
}

printMsg(welcomeMsg, "Jace", 31); // → Hello, Jace whose age is 31!
```

### 1-4 함수의 반환값으로 사용할 수 있는 객체 값

일반 객체는 함수의 반환 값으로 사용할 수 있다

```javascript
function printObj(arr) {
  return arr;
}

console.log(printObj(["Jace", 31])); // → ["Jace", 31]
```

함수 또한 함수의 반환 값으로 사용할 수 있다. 따라서 일반 객체와 함수 모두 일급 객체로서의 네 번째 조건을 충족한다

```javascript
function welcomeMsg(name) {
  return function (age) {
    console.log(`Hello, ${name} whose age is ${age}`);
  };
}

welcomeMsg("Jace")(31); // → Hello, Jace whose age is 31
```

### 1-5 호출할 수 있는 객체 값

일반 객체는 호출해서 재사용하는 것이 불가능하다. 

```javascript
const userInfo = {
  name: "Jace",
  age: 31
}

// 참조를 통한 사용. 그러나 함수와 같이 호출로 인한 재사용이 불가능
console.log(userInfo); // → { name: "Jace", age: 31 }
```

그러나 함수는 정의된 이후 함수 호출을 통해 재사용이 가능한 유일한 객체다

```javascript
function sayHello() {
  const name = "Jace";
  const age = 31
  
  console.log(`Hello, ${name} whose age is ${age}`);
}

sayHello();
```

객체 프로퍼티 값으로 할당된 함수의 경우에도 객체 전체를 호출하는 것이 아닌, 객체에 포함된 함수인 메서드만을 호출할 수 있다

```javascript
const obj = {
  name: "Jace",
  age: 31, 
  sayHello: function() {
    console.log(`Hello, ${this.name} whose age is ${this.age}`);
  }
}

obj.sayHello();
```

따라서 일급 객체에 해당하는 마지막 조건으로, 호출 가능한 객체 값은 함수만이 존재하기 때문에 JS에서 함수는 일급 객체로서 구분된다

그렇다면 함수가 일급 객체로서 JS에서 가능해지는 세부 기능들에는 무엇이 있을까? 대표적으로 고차함수(Higher-order Function)와 콜백함수(Callback Function)가 존재한다

- 고차함수

  > 고차함수란 함수를 인자로서 매개변수에 전달하거나 함수를 반환하는 함수를 의미한다. 대표적으로 JS에서 내부적으로 제공되는 빌트인 함수 `Array.prototype.map()`, `Array.prototype.reduce` 등이 존재한다. 빌트인 고차함수에 대해서는 [여기]()를 참고하자  

  ```javascript
  const numbers = [1, 2, 3, 4, 5];
  const tripledNumbers = numbers.map(element => element * 3);
  
  console.log(tripledNumbers); // → [3, 6, 9. 12, 15]
  ```

  위 예제의 빌트인 고차함수 `Array.prototype.map()`을 풀어서 쓰면 아래와 같다. 즉, 함수를 빌트인 고차함수 `Array.prototype.map()`의 매개변수에 전달할 인자로서 함수를 전달하는 것이다

  ```javascript
  const numbers = [1, 2, 3, 4, 5];
  const tripledNumbers = numbers.map(function(element) { 
  	return element * 3;
  });
  
  console.log(tripledNumbers);// → [3, 6, 9. 12, 15]
  ```

  위 예제를 고차함수가 아닌 일반 함수로 표현했을 때는 아래와 같다: 

  ```javascript
  const numbers = [1, 2, 3, 4, 5];
  const tripledNumbers = [];
  
  for(let i = 0; i < numbers.length; i++) {
    tripledNumbers.push(numbers[i] * 3);
  }
  
  console.log(tripledNumbers); // → [3, 6, 9. 12, 15]
  ```

- 콜백 함수

  > 콜백 함수에 대해서는 [여기](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/callback%20function.md)를 참고하자

  ```javascript
  function calculate(num1, num2, callback) {
    const result = num1 + num2; 
    callback(result);
  }
  
  function printResult(sum) {
    console.log(`The result is ${sum}`); // → The result is 15
  }
  
  calculate(5, 10, printResult);
  ```

<br>

## 2 함수 객체

JS에서 객체는 프로퍼티를 갖는다. 함수는 객체이기 때문에 함수도 일반 객체와 동일하게 프로퍼티를 가질 수 있다

> `console.dir` 메서드의 `dir`는 'Directory'의 약자로, 매개변수에 특정 객체를 전달하면 디렉토리(파일 구조) 형태의 리스트로 객체 정보를 콘솔에 출력한다

```javascript 
// 브라우저 콘솔에서 실행해야 한다

function sayHello(firstName, lastName, age) {
  return `Hello, ${firstName} ${lastName} whose age is ${age}`;
}

console.dir(sayHello);
```

브라우저 환경에서 함수 `sayHello`의 객체 정보를 확인하면 결과는 아래와 같이 반환된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/bb1739bf-fc09-45b7-b180-4febae3153a8" width="100%">

반환된 결과에서 볼 수 있듯이 함수 `sayHello`는 객체이므로 키와 값으로 이루어진 프로퍼티가 함수 내부를 구성하고 있다. `arguments`, `caller`, `length`, `name`, `prototype`이 모두 함수 `sayHello`의 데이터 프로퍼티다. 보다 쉬운 이해를 위해 함수 `sayHello`의 프로퍼티 어트리뷰트를 확인해보면 아래와 같다

```javascript
function sayHello(firstName, lastName, age) {
  return `Hello, ${firstName} ${lastName} whose age is ${age}`;
}

const descriptor = Object.getOwnPropertyDescriptors(sayHello);
console.log(descriptor); 
/* 
{
  length: { value: 3, writable: false, enumerable: false, configurable: true },
  name: { value: 'sayHello', writable: false, enumerable: false, configurable: true },
  arguments: { value: null, writable: false, enumerable: false, configurable: false},
  caller: { value: null, writable: false, enumerable: false, configurable: false },
  prototype: { value: {}, writable: true, enumerable: false, configurable: false }
}
*/
```

<br>

## 3 함수 객체의 데이터 프로퍼티 

함수의 프로퍼티를 살펴보면 일반 객체의 프로퍼티와는 다르다. 이는 함수가 일반 객체에 없는 고유 프로퍼티를 갖기 때문이다. 함수 객체만이 갖는 고유 프로퍼티를 살펴보자

### 3-1 arguments 프로퍼티

함수의 `arguments` 프로퍼티의 값은 `arguments` 객체다. 즉, 앞서 살펴본 함수의 고유 프로퍼티 5가지 중 첫 번째 프로퍼티인 `arguments` 프로퍼티는 `arguments`라는 객체를 값으로 가진다는 의미다. 따라서, 함수 `sayHello`를 호출하며 매개변수에 전달되는 인수(Argument)를 확인해보면 아래 예제와 같은 결과를 확인할 수 있다

> 앞서 [객체의 구성 요소](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#3-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C)에서 언급했듯이, 객체의 프로퍼티 값에는 JS에서 사용할 수 있는 모든 값이 자리할 수 있다했다. 따라서, 프로퍼티 값에 또 다른 객체가 사용될 수 있다는 것이다

> 반환된 인수의 정보를 확인하면 마치 배열과 같은 구조를 지녔다. 이러한 객체의 형태를 유사 배열 객체라고 부르며 이에 대해서는 [배열](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Array/array.md) 파트를 참고하자

> 아래 예제의 `arguments`는 JS에 자체적으로 지정된 예약어로서 따로 선언하지 않아도 사용 가능한 식별자다

```javascript
function sayHello(firstName, lastName, age) {
	// 함수 sayHello의 매개변수에 전달된 인수(arguments)의 정보 확인	
  console.log(arguments); // → { '0': 'Jace', '1': 'Nam', '2': 31 }
  return `Hello, ${firstName} ${lastName} whose age is ${age}`;
}

// 함수 sayHello를 호출하여 매개변수에 값을 전달
sayHello("Jace", "Nam", 31);
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ca2ca5c5-0b13-48d7-b3e9-9e5ff120d8d4" width="100%">

`arguments` 객체는 프로퍼티 값으로 매개변수에 전달된 인수 값을 갖게 되며, `arguments` 객체의 프로퍼티 키([배열의 인덱스](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Array/array.md#2-%EC%9D%B8%EB%8D%B1%EC%8A%A4%EC%99%80-%EB%B0%B0%EC%97%B4-%EA%B8%B8%EC%9D%B4)와 동일한 개념)는 인수의 순서를 나타낸다. 이때, 매개변수 개수보다 인수를 더 많이 전달한 경우에는 초과된 인수는 버려지는 것이 아니라 암묵적으로 `arguments` 객체의 프로퍼티로서 보관된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b22c104d-0b08-4858-8455-d67e6b9b1195" width="100%">

### 3-2 caller 프로퍼티

함수 객체의 `caller` 프로퍼티는 'caller'의 의미 그대로 '호출자', 즉 함수 자신을 호출한 함수를 가리키는 호출자를 의미한다 

아래 예제에서 함수 `checkCaller1`을 호출하여 인자로서 함수 `checkCaller2`를 매개변수에 전달한다. `checkCaller1`의 매개변수로 전달된 `checkCaller2`는 호출되어 실행되고 반환된다. 따라서 `result1`을 참조했을 때는 함수 `checkCaller1`이 함수 `checkCaller2`의 `caller`로서 지정된다

 `result2`의 결과는 `null`이다. 이는 함수 `checkCaller2`를 호출하는 함수가 따로 없기 때문에 `caller` 프로퍼티의 값은 `null`로 반환된다

```javascript
function checkCaller1(func) {
  // 매개변수로 전달받은 함수를 실행하여 반환한다
  return func();
}

function checkCaller2() {
  // checkCaller2 함수의 프로퍼티인 caller를 프로퍼티 접근 연산자로 참조한다
  return `caller: ${checkCaller2.caller}`;
}

const result1 = checkCaller1(checkCaller2);
const result2 = checkCaller2();

console.log(result1); // → caller: function checkCaller1(func) { func() }
console.log(result2); // → caller: null
```

그러나 `caller` 프로퍼티는 ECMAScript 사양에 포함되지 않는 비표준 프로퍼티다. 그리고 이후에도 표준화될 예정이 없는 프로퍼티로서 참고사항으로 알아만 두자

### 3-3 length 프로퍼티

함수의 `length` 프로퍼티는 [앞서](#3-1-arguments-프로퍼티) 언급한 `arguments` 프로퍼티의 값 `arguments` 객체의 `length` 프로퍼티와 동일한 개념이다. 함수의 `length` 프로퍼티는 매개변수의 개수를 나타낸다. 반면, `arguments` 객체의 `length` 프로퍼티는 인수의 개수를 나타낸다

```javascript
// 함수의 length 프로퍼티
function checkLength1() {};
console.log(checkLength1.length); // → 0

function checkLength2(x, y, z) {};
console.log(checkLength2.length); // → 3
```

```javascript
// arguments 객체의 length 프로퍼티
function checkLength(x, y, z) {
  console.log(arguments.length); // → 2
}

// 함수의 매개변수는 3개, 그러나 전달된 인수는 2개다
checkLength(1, 2);
console.log(checkLength.length); // → 3
```

### 3-4 name 프로퍼티

함수의 `name` 프로퍼티는 함수 이름을 나타낸다. `name` 프로퍼티에 대한 주의사항은 아래와 같다: 

- 기명 함수의 경우

  ```javascript
  const namedFunc = function checkName() {};
  console.log(namedFunc.name); // → checkName
  ```

  여기서 주의해야 할 점은 값으로서 변수에 할당된 기명 함수는 함수 이름이 있어도 함수를 가리키는 식별자(변수)로 `name` 프로퍼티에 접근해야 한다. 이는 [함수 이름이 함수 몸체 내부에서만 유효](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#4-%ED%95%A8%EC%88%98-%EC%84%A0%EC%96%B8%EB%AC%B8%EA%B3%BC-%ED%95%A8%EC%88%98-%ED%91%9C%ED%98%84%EC%8B%9D%EC%9D%98-%EC%B0%A8%EC%9D%B4)하기 때문이다

  ```javascript
  const namedFunc = function checkName() {};
  console.log(checkName.name); // → Uncaught ReferenceError: checkName is not defined
  ```

- 익명 함수의 경우

  익명 함수의 경우에도 기명 함수와 동일하게 함수를 가리키는 식별자(변수)로 `name` 프로퍼티에 접근해야 한다

  ```javascript
  const anonymousFunc = function() {};
  console.log(anonymousFunc.name); // → anonymousFunc
  ```

- 함수 선언문

  함수 선언문의 경우, [함수 이름 없이 선언할 수 없기 때문](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/function.md#3-1-%ED%95%A8%EC%88%98-%EC%9D%B4%EB%A6%84)에 함수 이름을 통해 `name`  프로퍼티에 접근하면 된다

  ```javascript
  function checkName() {}; 
  console.log(checkName.name); // → checkName
  ```

  





### 3-5 __proto__ 접근자 프로퍼티



<br>

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [일급 객체(First Class Object)란?](https://velog.io/@reveloper-1311/%EC%9D%BC%EA%B8%89-%EA%B0%9D%EC%B2%B4First-Class-Object%EB%9E%80)
- [자바스크립트 개발자라면 알아야 할 33가지 개념 #22 - 고차함수 이해하기](https://velog.io/@jakeseo_me/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%9D%BC%EB%A9%B4-%EC%95%8C%EC%95%84%EC%95%BC-%ED%95%A0-33%EA%B0%80%EC%A7%80-%EA%B0%9C%EB%85%90-22-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EA%B3%A0%EC%B0%A8-%ED%95%A8%EC%88%98Higher-Order-Function-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
