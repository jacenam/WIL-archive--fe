# 함수와 일급 객체

### 목차

- [1 자바스크립트 함수와 일급 객체](#1-자바스크립트-함수와-일급-객체)
  - [1-1 무명의 리터럴로 생성할 수 있는 객체 값](#1-1-무명의-리터럴로-생성할-수-있는-객체-값)
  - [1-2 변수나 자료구조에 할당할 수 있는 객체 값](#1-2-변수나-자료구조에-할당할-수-있는-객체-값)
  - [1-3 함수의 매개변수에 전달할 수 있는 객체 값](#1-3-함수의-매개변수에-전달할-수-있는-객체-값)
  - [1-4 함수의 반환값으로 사용할 수 있는 객체 값](#1-4-함수의-반환값으로-사용할-수-있는-객체-값)
  - [1-5 호출할 수 있는 객체 값](#1-5-호출할-수-있는-객체-값)

- [2 함수 객체](#2-함수-객체)

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

JS에서 객체는 프로퍼티를 갖는다. 함수는 객체이기 때문에 함수도 일반 객체와 동일하게 프로퍼티를 가질 수 있다. 