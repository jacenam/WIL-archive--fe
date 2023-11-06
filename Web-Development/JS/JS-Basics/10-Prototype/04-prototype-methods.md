# 프로토타입 관련 메서드

**Table of Contents**

- [instanceof 연산자](#instanceof-연산자)
- [직접 상속](#직접-상속)
  - [Object.create에 의한 직접 상속](#Object.create에-의한-직접-상속)
  - [proto 접근자 프로퍼티에 의한 직접 상속](#proto-접근자-프로퍼티에-의한-직접-상속)
- [프로퍼티 존재 확인](#프로퍼티-존재-확인)
  - [in 연산자](#in-연산자)
  - [Object.prototype.hasOwnProperty 메서드](#Object.prototype.hasOwnProperty-메서드)
- [프로퍼티 열거](#프로퍼티-열거)
  - [for...in 문](#for...in-문)
  - [Object.keys/values/entries 메서드](#Object.keys/values/entries-메서드)

<br>

## instanceof 연산자

`instanceof` 연산자는 좌항에 객체(식별자 이름), 우항에 생성자 함수(식별자 이름)을 지정하여 우항에 놓인 생성자 함수의 `prototype` 객체가 좌항 객체의 프로토타입 체인 상 존재 여부에 따라 불리언 타입의 값을 반환한다

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

const square = new Square(5);

// square 인스턴스의 프로토타입 체인 상 Square 생성자 함수가 존재한다
console.log(square instanceof Square); // → true
// square 인스턴스의 프로토타입 체인 상 Object 생성자 함수가 존재한다
console.log(square instanceof Object); // → true
```

위 `instanceof` 예제를 아래 그림과 같이 표현할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/0cb5709b-80b5-40a2-a6e7-96487be5353a" width="100%">

앞서 [프로토타입 교체](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype%20chain.md#5-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%98-%EA%B5%90%EC%B2%B4) 파트에서 살펴본대로 `prototype` 객체를 다른 객체로 변경할 경우  프로토타입 체인의 구도도 변한다. 아래 예제에서처럼 `square` 인스턴스와 `Square` 생성자 함수 간의 연결이 파괴된다. 따라서 `instanceof` 연산자를 통해 피연산자들의 관계를 확인했을 때 결과 또한 바뀐다

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

console.log(Square.prototype); // → {constructor: ƒ Square(name)}

const square = new Square(5); 
const change = {};

// square 인스턴스의 prototoype 객체를 change 객체로 변경
Object.setPrototypeOf(square, change);

// Square 생성자 함수는 square 인스턴스의 프로토타입 체인 상에 더 이상 존재 하지 않는다
console.log(square instanceof Square); // → false

// Object 생성자 함수는 square 인스턴스의 프로토타입 체인 상에 존재한다
console.log(square instanceof Object); // → true
```

<br>

## 직접 상속

### Object.create에 의한 직접 상속

### proto 접근자 프로퍼티에 의한 직접 상속

<br>

## 프로퍼티 존재 확인

### in 연산자

`in` 연산자는 특정 프로퍼티가 객체에 존재하는지 여부에 따라 불리언 타입을 반환한다

```javascript
// in 연산자의 사용 형태
key in object

const user = {
  name: "Jace", 
  age: 31,
  residence: "Seoul",
};

console.log("name" in user); // → true
console.log("height" in user); // → false
```

`in` 연산자는 확인 대상 객체의 프로퍼티 뿐만 아니라 해당 객체가 프로토타입으로부터 상속 받는 모든 프로퍼티를 확인하므로 결과에 주의해야 한다

```javascript
// Object 생성자 함수는 user 객체의 프로토타입 체인 상에 존재한다 
console.log(user instanceof Object); // → true

// 객체에 존재하지 않지만, 부모 프로토타입 객체로부터 상속받은 프로퍼티도 확인된다
console.log("hasOwnProperty" in user); // → true
console.log("constructor" in user); // → true
```

### Object.prototype.hasOwnProperty 메서드

`Object.prototype.hasOwnProperty` 메서드는 앞서 자주 살펴봤듯이, `Object` 생성자 함수와 연결된  `Object.prototype` 객체의 메서드로 모든 객체가 상속받아 사용할 수 있다

```javascript
const user = {
  name: "Jace", 
  age: 31,
  residence: "Seoul",
};

console.log(user.hasOwnProperty("name")); // → true
```

`Object.prototype.hasOwnProperty` 메서드는 확인 대상 객체의 프로퍼티만을 확인한다. 다시 말해 확인 대상 객체가 프로토타입으로부터 상속 받는 프로퍼티까지는 확인하지 않는다

```javascript
console.log(user.hasOwnProperty("constructor")); // → false
```

<br>

## 프로퍼티 열거

### for...in 문

객체에 존재하는 모든 프로퍼티를 순회하며 열거할 때는 `for...in` 문을 사용한다

```javascript
for (변수선언문 in 객체) { 
  ...
}
```

`for...in` 문은 객체의 존재하는 프로퍼티 개수만큼 객체를 순회하여 프로퍼티 키를 변수 선언문에 할당한다

```javascript
const user = {
  name: "Jace",
  age: 31, 
  residence: "Seoul"
}

for (const key in user) {
  console.log(key); // → name / age / residence
}
```

`for...in` 문은 `in` 연산자처럼 확인 대상 객체의 프로퍼티뿐만 아니라 프로토타입으로부터 상속받은 프로퍼티까지 열거한다. 그러나 실제로 `toString`, `hasOwnProperty`, `__proto__` 등과 같은 `Object.prototype` 객체의 프로퍼티는 열거되지 않는다. 이는 바로  위와 같은 `Object.prototype`의 프로퍼티는 프로퍼티 어트리뷰트 중 `[[Enumerable]]` 값이 `false` 이기 때문이다

```javascript
const user = {
  name: "Jace",
  age: 31, 
  residence: "Seoul"
}

console.log(Object.getOwnPropertyDescriptor(Object.prototype, "toString")); // → {writable: true, enumerable: false, configurable: true, value: ƒ}

console.log(Object.getOwnPropertyDescriptor(Object.prototype, "hasOwnProperty")); // → {writable: true, enumerable: false, configurable: true, value: ƒ}

console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__")); // → {enumerable: false, configurable: true, get: ƒ, set: ƒ}
```

따라서 확인 대상 객체만의 프로퍼티를 열거하고 싶다면 `for...in` 문보다 다음에 나올 `Object.keys`, `Object.values`, `Object.entries` 메서드의 사용을 권장한다

배열도 객체이므로 프로퍼티를 가질 수 있기 때문에 배열에도 `for...in` 문을 사용할 수 있다. 그러나 배열의 요소 외에도 프로퍼티까지 모두 순회한다

```javascript
const arr = [1, 2, 3]; 
arr.prop = 10; 

console.log(arr); // → [1, 2, 3, prop: 10]

// 배열에서는 요소의 인덱스(index)가 프로퍼티 키로서 동작한다
// prop 프로퍼티의 값도 출력된다
for (const idx in arr) {
  console.log(arr[idx]); // → 1 2 3 10 
}
```

따라서 배열의 프로퍼티를 제외한 요소만을 순회하기를 바란다면 `for...in` 문 대신 `for` 문 , `for...of` 문, `Array.prototype.forEach` 메서드를 사용하길 권장한다

> 배열과 관련된 내용은 [여기](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Array/array.md)를 참고하자

```javascript
const arr = [1, 2, 3]
arr.x = 10; 
arr.y = 20; 

console.log(arr.length); // → 3

// for...of 문은 변수에 키(key)가 아닌 값(요소)를 할당한다
for (const element of arr) {
  console.log(element); // → 1 2 3
}

arr.forEach(e => console.log(e)); // → 1 2 3
```

### Object.keys/values/entries 메서드

`for...in` 문은 객체의 프로퍼티뿐만 아니라 프로토타입으로부터 상속받은 프로퍼티도 순회하며 열거하기 때문에 `Object.prototype.hasOwnProperty` 메서드를 통해 객체만의 프로퍼티인지 추가 확인하는 작업이 필요하다. 따라서 이런 경우, `Object.keys`, `Object.values`, `Object.entries` 메서드를 사용하면 추가 작업 없이 객체 고유의 프로퍼티만을 확인할 수 있게 된다

- `Object.keys`: 열거 가능한 프로퍼티의 키를 배열 형태로 반환한다

  ```javascript
  const user = {
    name: "Jace", 
    age: 31,
    residence: "Seoul"
  };
  
  console.log(Object.keys(user)); // → ["name", "age", "residence"]
  ```

- `Object.values`: 열거 가능한 프로퍼티의 값을 배열 형태로 반환한다

  ```javascript
  console.log(Object.values(user)); // → ["Jace", 31, "Seoul"]
  ```

- `Object.entries`: 열거 가능한 프로퍼티의 키와 값을 배열 형태로 담아 배열로 반환한다

  ```javascript
  console.log(Object.entries(user)); // → [["name", "Jace"], ["age", 31], ["residence", "Seoul"]]
  ```

<br>

## 참고

- [모던 자바스크립트 Deep Dive]()

