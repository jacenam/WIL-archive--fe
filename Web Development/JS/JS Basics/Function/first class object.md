# 함수와 일급 객체

### 목차

- [1 자바스크립트 함수와 일급 객체](#1-자바스크립트-함수와-일급-객체)
- [2 함수 객체](#2-함수-객체)

<br>

## 1 자바스크립트 함수와 일급 객체

앞서 함수는 객체이면서 일급 객체라 했다. 일반 객체와 일급 객체는 무엇이 다를까? 일급 객체란 객체이면서 아래 조건들을 모두 만족해야 한다: 

- 무명의 리터럴로 생성할 수는 객체 값 있어야 한다
- 변수나 자료구조(객체, 배열 등)에 할당할 수 있는 객체 값이어야 한다
- 함수의 매개변수에 전달할 수 있는 객체 값이어야 한다
- 함수의 반환값으로 사용할 수 있는 객체 값이어야 한다
- 호출할 수 있는 객체 값이어야 한다

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

일반 객체는 변수 혹은 객체 자료구조에 할당하거나  저장할 수 있다

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

일반 객체는 함수의 매개변수에 전달하여 값으로 활용이 가능하다

```javascript
function printMsg(userInfo) {
  const name = userInfo.name;
  const age = userInfo.age;

  console.log(`Hello, ${name} whose age is ${age}!`);
}

printMsg((userInfo = { name: "Jace", age: 31 })); // → Hello, Jace whose age is 31!
```

함수 또한 객체 값으로서 함수의 매개변수에 전달하여 객체 값으로 활용이 가능하다. 따라서 일반 객체와 함수 모두  일급 객체로서의 세 번째 조건을 충족한다

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
```







함수가 일급 객체라는 의미는 함수를 객체와 동일하게 값으로서 취급할 수 있다는 것이다. JS에서 원시 타입을 제외한 그 외의 값은 모두 객체라 했다. 따라서 함수는 객체 값을 아래와 같이 사용할 수 있는 곳에서라면 어디서든지 사용할 수 있다:

- 변수 할당문
- 객체의 프로퍼티 값
- 배열의 요소
- 함수 호출의 인수
- 함수 반환문

JS에서 함수가 일급 객체로서 가지는 가장 큰 특징은 아래와 같다:

- 함수의 매개변수에 값으로서 전달할 수 있으며 함수의 반환 값으로도 사용이 가능하다
- 일반 객체는 호출할 수 없지만, 함수 객체는 호출 할 수 있다