# 프로토타입 체인

### 목차

- [1 프로토타입 체인](#1-프로토타입-체인)

<br>

## 1 프로토타입 체인

앞서 [프로토타입 상속](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#2-2-%EC%83%81%EC%86%8D) 파트에서 살펴봤듯이, 생성자 함수를 통해 생성된 인스턴스의 불필요한 중복을 줄이기 위해 프로토타입이 활용된다 했다. 즉, 부모 객체에서 자식 객체로 특정 요소들을 사용하게 할 수 있다. 이는 생성자 함수, `prototype` 객체, 인스턴스(또는 객체)가 모두 연결되어 있기 때문이며, 이러한 연결성을 '프로토타입 체인(Prototype Chain)'이라 부른다. 아래 예제를 살펴보자:

```javascript
function User(name) {
  this.name = name;
}

// 프로토타입 객체에 프로퍼티(메서드) 추가
User.prototype.sayHello = function () {
  console.log(`Hello, ${this.name}`);
}

// 인스턴스 생성
const a = new User("Jace");
const b = new User("Ju Hyung");

// 프로토타입 객체로부터 상속받은 프로퍼티(메서드)를 사용
a.sayHello(); // → Hello, Jace
b.sayHello(); // → Hello, Ju Hyung
```

위 코드 예제를 통해 생성된 인스턴스, 생성자 함수, `prototype` 객체의 관계는 아래 그림과 같이 나타낼 수 있다: 

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/71f8372a-64b7-484f-b346-9cac65657ff3" width="100%">

만약 `prototype` 객체의 프로퍼티를 삭제하면 어떻게 될까? 아래 예제를 살펴보자:

```javascript
function User(name) {
  this.name = name;
}

// 프로토타입 객체에 프로퍼티(메서드) 추가
User.prototype.sayHello = function () {
  console.log(`Hello, ${this.name}`);
}

// 인스턴스 생성
const a = new User("Jace");
const b = new User("Ju Hyung");

// 프로토타입 객체의 프로퍼티(메서드) 삭제
delete User.prototype.sayHello;

// 프로토타입 객체로부터 상속 받을 sayHello 프로퍼티가 사라진다
a.sayHello(); // → Uncaught TypeError: a.sayHello is not a function
```

앞서 살펴본 [프로퍼티 객체의 프로퍼티 구성](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-1-2-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98) 파트에서 생성자 함수 예제와 유사한 예제를 살펴보자: 

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

Square.prototype.printArea = function () {
  console.log(Math.pow(this.sideLength, 2));
}

const squareInstance = new Square(5);

console.log(squareInstance.hasOwnProperty("sideLength")); // → true
```

프로퍼티 객체의 프로퍼티 구성 파트에서 언급했듯이, 일반 함수 정의 방식으로 정의된 생성자 함수와 함께 생성되는 `Square.prototype` 객체는 `constructor` 프로퍼티와 `[[Prototype]]` 내부 슬롯만을 소유한다. 이런 상황에서 `squareInstance` 인스턴스는 어떻게 `hasOwnProperty` 프로퍼티(메서드)를 사용할 수 있는 것일까? 

이 또한 프로퍼티 객체의 프로퍼티 구성에서 언급했듯이, `Square.prototype` 객체도 객체로서   `[[Prototype]]` 내부 슬롯에 `Object.prototype` 객체를 가리키는 참조 값을 저장하고 있다. 이를 통해 `Square` 생성자 함수, `Square.prototype` 객체, `Object.prototype` 객체, `squareInstance` 인스턴스는 연결되어 있기 때문에 `squareInstance` 인스턴스는 `Object.prototype` 객체의 프로퍼티(메서드)인 `hasOwnProperty`를 활용할 수 있는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/6099b79c-93b4-4164-bd30-d66680583d20" width="100%">

이렇게 생성자 함수, `prototype` 객체, 인스턴스가 연결되어 있는 관계도를 프로토타입 체인으로 표현할 수 있다 

<br>

***

### 참고

- 