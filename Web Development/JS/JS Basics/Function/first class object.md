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

```javascript
function sayHello(firstName, lastName, age) {
	// 함수 sayHello의 매개변수에 전달된 인수(arguments)의 정보 확인	
  console.log(arguments); // → { '0': 'Jace', '1': 'Nam', '2': 31 }
  return `Hello, ${firstName} ${lastName} whose age is ${age}`;
}

// 함수 sayHeelo를 호출하여 매개변수에 값을 전달
sayHello("Jace", "Nam", 31);
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ca2ca5c5-0b13-48d7-b3e9-9e5ff120d8d4" width="100%">

`arguments` 객체는 프로퍼티 값으로 매개변수에 전달된 인수 값을 갖게 되며, `arguments` 객체의 프로퍼티 키([배열의 인덱스](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Array/array.md#2-%EC%9D%B8%EB%8D%B1%EC%8A%A4%EC%99%80-%EB%B0%B0%EC%97%B4-%EA%B8%B8%EC%9D%B4)와 동일한 개념)는 인수의 순서를 나타낸다. 이때, 매개변수 개수보다 인수를 더 많이 전달한 경우에는 초과된 인수는 버려지는 것이 아니라 암묵적으로 `arguments` 객체의 프로퍼티로서 보관된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b22c104d-0b08-4858-8455-d67e6b9b1195" width="100%">

### 3-2 caller 프로퍼티

함수 객체의 `caller` 프로퍼티는 'caller'의 의미 그대로 '호출자', 즉 함수 자신을 호출한 함수를 가리킨다. 

```javascript
function checkCaller1(func) {
  return func();
}

function checkCaller2() {
  return `caller: ${checkCaller2.caller}`;
}

const result1 = checkCaller1(checkCaller2);
const result2 = checkCaller2();

console.log(result1);
console.log(result2);
```



`callee`와 `caller`

`arguments` 객체의 `callee` 프로퍼티도 동일한 개념. 

### 3-3 length 프로퍼티



`arguments` 객체의 `length` 프로퍼티도 동일한 개념. 함수의 `length` 프로퍼티는 매개변수의 개수를 나타낸다. 반면, `arguments` 객체의 `length` 프로퍼티는 인수의 개수를 나타낸다.

### 3-4 name 프로퍼티

### 3-5 __proto__ 접근자 프로퍼티



<br>

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)

