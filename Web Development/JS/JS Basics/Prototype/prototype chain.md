# 프로토타입 체인

### 목차

- [1 프로토타입 체인](#1-프로토타입-체인)
- [2 프로토타입 체인 탐색](#2-프로토타입-체인-탐색)

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

이렇게 생성자 함수, `prototype` 객체, 인스턴스가 연결되어 있는 관계도를 프로토타입 체인으로 표현할 수 있다. 그리고 JS에서는 특정 객체의 프로퍼티에 접근하고자 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 `[[Prototype]]` 내부 슬롯의 저장된 `prototype` 객체의 참조 값을 통해 부모 `prototype` 객체의 프로퍼티를  순차적으로 탐색 해나간다. 이로써 프로토타입 체인은 JS의 객체지향 프로그래밍을 가능하게 하는 매커니즘이라 볼 수 있는 것이다

<br>

## 2 프로토타입 체인 탐색

앞서 생성자 함수, `생성자 함수.prototype`, `Object.prototype`, 인스턴스의 관계인 프로토타입 체인에 대해 살펴봤다. 부모와 자식 관계일 때 객체(프로토타입, 인스턴스)끼리 특정 프로퍼티 혹은 메서드를 상속받아 사용이 가능하다는 것도 살펴봤다 

특정 객체(프로토타입, 인스턴스)를 기준으로 특정 프로퍼티를 참조하거나 활용하고자 할 때, JS 엔진은 현재 기준이 되는 객체에서부터 프로퍼티 탐색을 시작한다. 만약 접근하고자 하는 프로퍼티가 기준이 되는 객체에 존재한다면, 해당 프로퍼티를 반환 혹은 활용하고 그 즉시 프로퍼티 탐색을 종료한다. 다음 예제를 살펴보자:

> 앞서 [`__proto__` 접근자 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-4-proto-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0) 파트에서 살펴본대로,  `setPrototypeOf` 메서드는 `Object.prototype`의 프로퍼티로서  두 번째 매개변수를 첫 번째 매개변수의 `prototype` 객체로 교체해주는 기능을 수행한다

```javascript
const car = {
  wheels: 4,
  drive() {
    console.log("drive away");
  },
}

const brand = {
  name: "benz",
  navigation: true,
}

const model = {
  name: "c200",
  color: "black",
  price: 5000,
}

// __proto__ 접근자 프로퍼티가 아닌 setPrototypeOf 메서드를 통해 프로토타입 관계 설정
Object.setPrototypeOf(brand, car);
Object.setPrototypeOf(model, brand);
```

위 예제는 `model` 객체를 기준으로 `brand.prototype` → `car.prototype` → `Object.prototype` 객체가 프로토타입 체인으로서 연결되도록 설정한 것이다. 다시 아래 예제 코드를 살펴보자:

```javascript
console.log(model.name); // → c200
```

위 예제에서 `model` 객체는 `name` 프로퍼티를 가지고 있기 때문에 `c200` 프로퍼티 값을 바로 반환한다. `model` 객체의 부모인 `brand` 객체(`brand.prototype` 객체이기도 하다)도 `name: "benz"` 프로퍼티를 가지고 있지만, 앞서 언급했듯이 JS 엔진은 현재 기준이 되는 객체에서부터 프로퍼티를 탐색하기 때문에 `model` 객체의 `name` 프로퍼티를 발견하는 즉시 프로토타입 체인 탐색을 중단한다

아래 예제를 다시 살펴보자. 이번에는 `model` 객체가 가지지 않은 프로퍼티를 참조할 때 JS 엔진은 프로토타입 체인을 따라 부모 객체(프로토타입)까지 탐색을 진행한다. 부모 객체에 참조하고자 하는 프로퍼티가 탐색되는 즉시 해당 프로퍼티들을 반환하고 탐색을 중지한다

```javascript
console.log(model.navigation); // → true
console.log(model.drive()); // → drive away
console.log(brand.wheels); // → 4
```

정리하자면, 특정 프로퍼티에 접근하고자 했을 때 기준이 되는 객체에서 탐색을 시작해서 상위 `prototype` 객체까지 한 단계씩 탐색해나가며, 접근하고자 하는 프로퍼티를 찾는 즉시 프로토타입 체인 탐색은 그 즉시 멈추는 것이다. 프로토타입 체인의 프로퍼티 탐색의 과정을 아래 그림과 같이 나타낼 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/c99c95b5-fb04-4e79-ad8d-23dd86241169" width="100%">

<br>

***

### 참고

- 