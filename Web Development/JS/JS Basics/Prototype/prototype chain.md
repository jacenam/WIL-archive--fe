# 프로토타입 체인

### 목차

- [1 프로토타입 체인](#1-프로토타입-체인)
- [2 프로토타입 체인 탐색](#2-프로토타입-체인-탐색)
- [3 오버라이딩과 프로퍼티 섀도잉](#3-오버라이딩과-프로퍼티-섀도잉)
- [4 프로토타입 프로퍼티의 변경](#4-프로토타입-프로포티의-변경)
- [5 프로토타입의 교체](#5-프로토타입의-교체)
  - [5-1 생성자 함수에 의한 프로토타입 교체](#5-1-생성자-함수에-의한-프로토타입-교체)
  - [5-2 인스턴스에 의한 프로토타입 교체](#5-2-인스턴스에-의한-프로토타입-교체)


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

생성자 함수를 통해 생성한 인스턴스의 경우를 포함하여 프로토타입 체인의 최상위에 위치하는 `prototype` 객체는 언제나 `Object.prototype`이다. 따라서 모든 객체는 `Object.prototype`으로부터 프로퍼티를 상속받을 수 있다

실제로 `Object.prototype`의 `prototype` 객체, 즉 `[[Prototype]]` 내부 슬롯의 참조 값은 `null`이다

```javascript
console.log(Object.prototype.__proto__ === null); // → true
```

> 스코프 체인과 프로토타입 체인의 차이도 추후 살펴볼 예정이다

<br>

## 3 오버라이딩과 프로퍼티 섀도잉

오버라이딩(Overriding)이란 프로토타입 체인 상에서 상위 객체가 이미 가지고 있는 메서드를 하위 객체가 재정의하여 사용하는 방식을 의미한다

프로퍼티 섀도잉(Property Shadowing)은 상위 객체가 가지고 있는 메서드를 하위 객체가 재정의함에 따라, 상위 객체의 메서드가 '가려지는' 현상을 의미한다. 즉, 상속 관계에 의해 프로퍼티가 가려지는 현상을 프로퍼티 섀도잉이라 하는 것이다

아래 예제를 살펴보자: 

```javascript 
function User(name) {
  this.name = name; 
}

// User.prototype 객체에 메서드 추가
User.prototype.sayHello = function() {
  console.log(`Hello, ${this.name} from prototype`);
}

// User 생성자 함수를 통한 인스턴스 생성
const a = new User("Jace");

// User.prototype 객체의 메서드를 상속받아 호출
a.sayHello(); // → Hello, Jace from prototype

// 인스턴스에 동일한 이름의 메서드를 재정의
a.sayHello = function() {
  console.log(`Hello, ${this.name} from instance`);
}

// 인스턴스의 메서드를 호출
a.sayHello(); // → Hello, Jace from instance
```

`prototype` 객체가 소유한 프로퍼티를 '프로토타입 프로퍼티', 인스턴스가 소유한 프로퍼티를 '인스턴스 프로퍼티'라 부른다. 위 예제는 프로토타입 프로퍼티인 `sayHello` 메서드와 같은 이름의 프로퍼티를 인스턴스에 추가한 상황이다. 이때 "인스턴스 프로퍼티인 `sayHello` 메서드는 프로토타입 프로퍼티인 `sayHello` 메서드를 오버라이딩했고, 프로토타입의 `sayHello` 메서드는 인스턴스의 `sayHello` 메서드에 의해 섀도잉되었다" 라고 표현한다

<br>

## 4 프로토타입 프로퍼티의 변경

프로토타입도 객체기 때문에 프로퍼티를 추가, 삭제, 수정할 수 있다. 프로토타입 체인 상에서 하위 객체는 상위 객체의 프로퍼티를 마음대로 삭제 혹은 수정할 수 없다(하위 객체에서 프로퍼티를 추가하면 하위 객체에 해당 프로퍼티가 추가된다)

위 [오버라이딩과 프로퍼티 섀도잉](#3-오버라이딩과-프로퍼티-섀도잉)에서 살펴본 예제를 이어서 활용해보자. 인스턴스의 `sayHello` 메서드를 삭제하면 인스턴스는 `sayHello` 메서드를 더 이상 가지지 않기 때문에, `sayHello` 메서드를 호출하면 `User.prototype` 객체의 메서드를 상속받아서 호출하게 된다

```javascript
// 인스턴스의 메서드를 삭제한다
delete a.sayHello(); 

// 인스턴스의 메서드를 삭제했기 때문에 프로토타입의 메서드를 상속받아 호출
a.sayHello(); // → Hello, Jace from prototype
```

여기서 `sayHello` 메서드를 다시 한번 더 삭제해보자. `sayHello` 메서드를 상속받았기 때문에 인스턴스를 통해 `sayHello` 메서드에 접근해 삭제하려해도 삭제 되지 않는다. 이는 프로토타입 체인 때문이다. 프로토타입 체인 상에서 하위 객체는 상위 객체의 프로퍼티를 변경하거나 삭제하는 것은 불가능하다

```javascript
delete a.sayHello;

// User.prototype의 sayHello 메서드는 삭제되지 않아 정상적으로 호출된다
a.sayHello(); // → Hello, Jace from prototype
```

따라서 프로토타입 프로퍼티를 변경 또는 삭제하려면 상위 객체인 프로토타입 자체에서 해당 프로퍼티를 변경 혹은 삭제해야한다

```javascript
delete User.prototype.sayHello(); 

a.sayHellp(); // → Uncaught TypeError: a.sayHellp is not a function
```

<br>

## 5 프로토타입의 교체

앞서 [프로토타입 체인 탐색](#2-프로토타입-체인-탐색) 파트의 예제에서 살펴봤듯이 `prototype` 객체는 다른 객체로도 변경이 가능하다. 즉, 특정 객체의 부모 객체인 `prototype` 객체를 동적으로 변경할 수 있는 것이며 이는 객체 간의 상속 관계 또한 동적으로 변경이 가능하다는 것을 의미한다

### 5-1 생성자 함수에 의한 프로토타입 교체

아래 예제를 살펴보자. 일반 함수 정의 방식으로 정의된 `User` 생성자 함수가 있다. 앞서 [프로토타입 객체의 프로퍼티 구성](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-1-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EA%B0%9D%EC%B2%B4%EC%9D%98-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EA%B5%AC%EC%84%B1)에서 살펴봤듯이 `User` 생성자 함수의 프로퍼티로서 `User.prototype` 객체가 존재하며, `User.prototype` 객체의 프로퍼티는 `constructor` 프로퍼티가 존재한다. 그리고 `constructor` 프로퍼티 값은 `User` 생성자 함수에 대한 참조 값이다

```javascript
function User(name) {
  this.name = name; 
}

// User 생성자 함수의 User.prototype 객체는 constructor 프로퍼티를 갖는다
console.log(User.prototype); // → {constructor: ƒ User(name)}
```

여기서 위 예제와 이어지는 아래 예제를 다시 살펴보면, `User` 생성자 함수의 프로퍼티인 `User.prototype` 객체에 객체 리터럴을 통해 생성한 `obj` 객체를 할당했다. 즉 `User` 생성자 함수의 프로퍼티인 `User.prototype` 객체의 값을 동적으로 변경한 것이다. 프로토타입을 교체했기 때문에 객체 간의 상속 관계는 변경된다. 객체 리터럴을 통해 생성된 `obj` 객체의 프로퍼티인 `sayHello` 메서드를 `User` 생성자 함수에 의해 생성된 `a` 인스턴스가 사용할 수 있게 된다

```javascript
// sayHello 메서드를 갖는 obj 객체를 객체 리터럴을 통해 생성한다
const obj = {
  sayHello() {
    console.log(`Hello, ${this.name}`);
  }
};

// User.prototype을 객체 리터럴 방식으로 생성한 obj 객체로 교체했다 
User.prototype = obj; 

const a = new User("Jace");

a.sayHello(); // → Hello, Jace
```

`User.prototype`을 참조해보면 `obj` 객체의 프로퍼티를 갖는 것을 확인할 수 있다. 이전에 `User.prototype` 객체가 갖던 `constructor` 프로퍼티는 더 이상 `User.prototype` 객체의 프로퍼티가 아닌 것이다

```javascript
console.log(User.prototype); // → {sayHello: ƒ}
```

이때 `User.prototype` 객체는 더 이상 `User` 생성자 함수의 참조 값을 갖지 않게 되기 때문에 `User` 생성자 함수, `User.prototype` 객체, `a` 인스턴스의 연결성은 유효하지 않게 된다. 즉 `a` 인스턴스의 생성자 함수는 `User` 생성자 함수가 아닌 `Object` 생성자 함수가 된다

```javascript
console.log(a.constructor === User); // → false
console.log(a.constructor === Object); // → true
```

위 예제와 설명을 아래 그림과 같이 표현할 수 있다. 앞서 [constructor 프로퍼티](https://github.com/jacenam/WIL-archive/assets/92138751/231eb0b0-6aaa-4854-8a3e-c97e768c79c3) 파트에서 살펴본 대로 생성자 함수에 의해 생성된 인스턴스가 존재한다면 생성자 함수, 프로토타입, 인스턴스는 언제나 연결되어 존재해야 한다. 따라서 `User` 생성자 함수와 `a` 인스턴스의 관계가 유효하지 하지 않기 때문에 JS 엔진은 가상의 `Object` 생성자 함수를 만들어내어 `a` 인스턴스를 `Object.prototype` 객체의 자식 객체로 연결짓는다. 그리고 이 때문에 `User.prototype`은 더 이상 `constructor` 프로퍼티를 갖지 않지만, `a` 인스턴스와 `User.prototype` 객체는 `constructor` 프로퍼티를 `Object.prototype`으로부터 상속받아 사용할 수 있게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/231eb0b0-6aaa-4854-8a3e-c97e768c79c3" width="100%">

```javascript
// User.prototype 객체는 자신에게서 없어진 constructor 프로퍼티를 상속받아 사용이 가능하다
console.log(User.prototype.constructor === User); // → false
console.log(User.prototype.constructor === Object); // → true
```

만약 `User` 생성자 함수, `User.prototype` 객체, `a` 인스턴스의 연결을 다시 되살리고자 하면 아래와 같이 실행할 수 있다

> 프로퍼티의 동적 생성에 대해서는 [객체 타입](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#3-4-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%9D%98-%EB%8F%99%EC%A0%81-%EC%83%9D%EC%84%B1-%EC%82%AD%EC%A0%9C-%EB%B0%8F-%ED%99%95%EC%9D%B8) 파트를 참고하자

```javascript
// 현재 User.prototype에 obj 객체가 할당되어 있다
console.log(User.prototype); // → {sayHello: ƒ}

// User.prototype 객체에 동적으로 constructor 프로퍼티와 프로퍼티 값을 추가한다
User.prototype.constructor = User;

// a 인스턴스와 User.prototype 객체는 다시 User 생성자 함수와 연결된다
console.log(a.constructor === Object); // → false
console.log(a.constructor === User); // → true
console.log(User.prototype.constructor === Object); // → false
console.log(User.prototype.constructor === User); // → true
```

### 5-2 인스턴스에 의한 프로토타입 교체

인스턴스의 프로토타입은 없다. 이걸 주의해야한다. 

User 생성자 함수는 prototype을 프로퍼티로서 갖는다. 인스턴스는 prototype을 프로퍼티로서 갖는게 아니라 [[prototype]] 내부 메서드를 통해 User.prototype에 접근하는 것.

객체 리터럴도 마찬가지. model.prototype은 없음. model은 인스턴스니까

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

undefined
model.__proto__ === Object.prototype;
true
Object
ƒ Object() { [native code] }
model.prototype;
undefined
```



***

### 참고

- 