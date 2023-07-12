# 프로토타입

JS는 프로토타입 기반의 [객체지향 언어](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/object%20oriented%20programming.md)다. JS를 이루고 있는 값 중 원시 타입을 제외한 모든 값이 객체다. 따라서 모든 객체 연결되어 있는 프로토타입은 JS에서 가장 중요한 개념 중 하나이며, JS를 C++, Java 같은 클래스 기반의 객체지향 언어와 구분하는 가장 핵심적인 요소라고 볼 수 있다. 

<br>

### 목차

- [1 프로토타입이란](#1-프로토타입이란)
- [2 프로토타입과 상속](#2-프로토타입과-상속)
  - [2-1 생성자 함수의 단점](#2-1-생성자-함수의-단점)
  - [2-2 상속](#2-2-상속)
- [3 프로토타입 객체](#3-프로토타입-객체)
  - [3-1 constructor 프로퍼티(feat. 생성자 함수)](#3-1-constructor-프로퍼티(feat.-생성자-함수))
  - [3-2 constructor 프로퍼티(feat. 객체 리터럴)](#3-2-constructor-프로퍼티(feat.-객체-리터럴))
  - [3-3 proto 접근자 프로퍼티](#3-3-proto-접근자-프로퍼티)
- [4 함수 객체의 prototype 프로퍼티](#4-함수-객체의-prototype-프로퍼티)

<br>

## 1 프로토타입이란 

아래 예제와 같이 브라우저 환경에서 특정 객체를 참조하면 할당한 프로퍼티가 포함된 객체가 반환된다. 그러나  할당하지 않은 `[[Prototype]]` 내부 슬롯 또한 반환되는 것을 확인할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/06bf9b6d-e62d-40ed-bb2d-b6c95f120166" width="100%">

 JS의 모든 객체는 디폴트로 `[[Prototype]]` 내부 슬롯을  갖게 된다. 심지어 빈 객체(`{}`)도 `[[Prototype]]` 내부 슬롯을 갖는다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/14c2d409-2f06-4c29-9a70-73851325040e" width="100%">

이렇듯, JS에서 모든 객체는 `[[Prototype]]` 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조값이다. 즉, `[[Prototype]]` 내부 슬롯에 저장된 참조 값은 프로토타입을 가리킨다. 프로토타입도 프로퍼티로 구성된 객체로서 이를 `prototype`  객체라고 부르기도 한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b7236193-bf26-493c-99d5-c1716dbcd30d" width="100%">

이러한 `prototype` 객체는 어디에 쓰이는 것일까? 아래 예제를 살펴보면, `user` 객체에는 `name` 프로퍼티가 할당되어 있기 때문에 `name` 프로퍼티를 참조하면 프로퍼티 값이 반환된다. 근데 아래와 같이  `user` 객체에 존재하지 않았던 `hasOwnProperty` 프로퍼티는 어떻게 참조하는 것일까?

```javascript
const user = { name: "Jace" };

console.log(user.name); // → Jace
console.log(user.hasOwnProperty); // → ƒ hasOwnProperty()  { [native code] }
```

이는 `user` 객체가 생성될 때 `prototype` 객체와 디폴트로 연결되기 때문이다. 엄밀히 말해,  `user` 객체가 직접적으로 `prototype` 객체를 소유하고 있진 않지만, `user` 객체에 자동적으로 할당되는 `[[Prototype]]` 내부 슬롯에 `prototype` 객체를 가리키는 참조 값을 저장하고 있다. 그리고 이렇게 연결된 `user` 객체와 `prototype` 객체는 서로 상속 관계로서, `prototype` 객체의 프로퍼티를 `user` 객체가 상속 받아 사용할 수 있게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/4bcb9d44-446f-404b-b7d8-fa7b9641949d" width="100%">

따라서, 아래 예제와 같이 `user` 객체는 `prototype` 객체의  `hasOwnProperty` 프로퍼티를 상속 받아 참조할 수도 있고, `hasOwnProperty` 프로퍼티에 할당되어 있는 함수 값인 메서드를 사용할 수도 있게 된다

> `hasOwnProperty()` 메서드는 매개변수로 프로퍼티 이름을 전달받으며, 전달 받은 프로퍼티 이름이 객체에 존재하는지에 따라 결과 값을 불리언 타입으로 반환한다

```javascript
const user = { name: "Jace" };

// 참조
console.log(user.hasOwnProperty); // → ƒ hasOwnProperty()  { [native code] }
// 메서드 사용
console.log(user.hasOwnProperty("name")); // → true
```

JS에서는 '상속'이라는 특성이 프로토타입을 통해 구현되며, 이 때문에 프로토타입이 JS에서 굉장히 중요한 개념으로서 간주되는 것이다. 그렇다면 프로토타입을 통한 상속 기능은 왜 중요하며 어떻게 활용할 수 있을까?

<br>

## 2 프로토타입과 상속

### 2-1 생성자 함수의 단점

[생성자 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/constructor%20function.md) 파트에서 살펴봤듯이, 객체 리터럴은 하나의 리터럴로 단 하나의 객체만을 생성할 수 있다

```javascript
const square1 = {
  sideLength: 5, 
  getArea: function() {
  	return Math.pow(this.sideLength, 2);
  },
}

const square2 = {
  sideLength: 10, 
  getArea: function() {
  	return Math.pow(this.sideLength, 2);
  },
}
```

따라서 유사 구조를 가진 객체를 여러 개 생성할 시 생성자 함수를 통해 객체 리터럴보다 편한 방식으로 다수의 객체를 생성 및 관리할 수 있게 된다. 생성자 함수에 대한 아래 예제를 살펴보자:

```javascript
function Square(sideLength) {
  this.sideLength = sideLength; 
  this.getArea = function() {
    return Math.pow(this.sideLength, 2);
  }
}

const square1 = new Square(5); 
const square2 = new Square(10);
const square3 = new Square(20);

console.log(square1.getArea()); // → 25
console.log(square2.getArea()); // → 100
console.log(square3.getArea()); // → 400
```

위 생성자 함수 `Square`를 통해 생성된 인스턴스(객체)  `square1`, `square2`, `square3`는 모두 동일한 프로퍼티 `sideLength`와 메서드  `getArea`를 갖는다. 즉, 생성자 함수를 통해 인스턴스를 생성할 때마다 각각의 인스턴스는 모두 동일한 형태의 프로퍼티와 메서드를 중복적으로 갖는다는 의미다

```javascript
// 생성된 인스턴스 모두가 getArea 메서드를 중복적으로 갖는다
console.log(square1); // → Square { sideLength: 5, getArea: [Function] }
console.log(square2); // → Square { sideLength: 10, getArea: [Function] }
console.log(square3); // → Square { sideLength: 20, getArea: [Function] }
```

심지어 각 인스턴스에서 사용되는 동일한 메서드처럼 보이지만 실제로 모두 다른 메모리 공간에 저장되기 때문에, 생성자 함수를 통해 인스턴스를 많이 생성하면 할수록 내용이 동일한 내용의 메서드를 저장하기 위해 불필요한 메모리를 차지하게 된다

> 메서드는 객체에 할당되어 있는 함수다. 따라서 객체와 동일하게 [참조에 의한 저장 방식](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/primitive%20%26%20object%20type.md#3-2-%EA%B0%9D%EC%B2%B4-%EA%B0%92%EC%9D%98-%EC%A0%80%EC%9E%A5)을 통해 메모리에 객체 값이 저장된다. 위 예제에서 각 인스턴스의 메서드가 모두 동일해보이지만 저장되는 메모리 공간이 모두 다르다. 즉, 각각의 메서드에 대한 메모리 참조 값이 다르다

```javascript
console.log(square1.getArea === square2.getArea === square3.getArea); // → false
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/d6b2e87b-0645-403f-b4ec-dd5c7de1ef6d" width="100%">

### 2-2 상속

생성자 함수를 통해 생성된 인스턴스 내의 불필요한 중복을 줄이기 위해서는 프로토타입을 활용하면 된다. JS는 프로토타입을 기반으로 원형으로부터 변화된 상태의 인스턴스(원형 → 인스턴스)들로 특정 요소들을 상속시킬 수 있다. 예를 들어, 위 예제에서 각 인스턴스에 중복으로 할당된 메서드 `getArea`를 프로토타입 상속으로 제거해보자:

```javascript 
function Square(sideLength) {
  this.sideLength = sideLength;
}

// 생성자 함수 Sqaure의 prototype 프로퍼티 안에 있는 프로퍼티 getArea에 함수를 할당한 것
Square.prototype.getArea = function() {
	return Math.pow(this.sideLength, 2);
};

const square1 = new Square(5); 
const square2 = new Square(10);
const square3 = new Square(20);

console.log(square1.getArea()); // → 25
console.log(square2.getArea()); // → 100
console.log(square3.getArea()); // → 400
```

> 생성자 함수 `Sqaure`의 `prototype` 프로퍼티 안에 있는 `getArea` 프로퍼티에 함수를 할당한 것이다. 이는 [마침표 프로퍼티 접근 연산자](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#32-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%A0%91%EA%B7%BC)를 통해 [프로퍼티를 동적 생성](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#34-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%9D%98-%EB%8F%99%EC%A0%81-%EC%83%9D%EC%84%B1-%EC%82%AD%EC%A0%9C-%EB%B0%8F-%ED%99%95%EC%9D%B8)한 것이다. 객체의 프로퍼티 값에는 어떠한 값도 올 수 있기 때문에, 프로퍼티 내부에 객체와 프로퍼티를 할당할 수 있다. 앞서 언급했듯이, 모든 객체는 디폴트로 `[[Prototype]]` 내부 슬롯을 가지기 때문에 `prototype` 프로포티를 호출할 수 있다
> ```javascript
> // 완벽히 동일하진 않지만, 유사한 형태의 예제
> const Square = {
>   prototype: {getArea: function() {}},
> };
> 
> console.log(Square); // → { prototype: {...}}
> console.log(Square.prototype); // → { getArea: [Function] }
> console.log(Square.prototype.getArea); // → function() {}
> ```

생성자 함수 `Square`의 프로토타입 프로퍼티에 접근하여 중복되는 메서드를 하나만 생성하여 할당한 것이다. 이를 통해, 생성자 함수 `Square`에 의해 생성되는 모든 인스턴스는 메서드  `getArea`를 유전적 요소와 같이 상속받아 사용할 수 있게 된다

```javascript
// 생성된 인스턴스 모두가 getArea 메서드를 중복적으로 갖지 않는다
console.log(square1); // → Square { sideLength: 5 }
console.log(square2); // → Square { sideLength: 10 }
console.log(square3); // → Square { sideLength: 20 }
```

생성자 함수 `Square`에 의해 생성된 인스턴스는 모두 메서드 `getArea`를 각각 따로 메모리에 저장하지 않게 되며, 이를 통해 메모리 공간 저장 효율도 개선할 수 있게 된다. 즉, 인스턴스별로 상이한 프로퍼티만 개별적으로 소유하고 동일한 메서드는 상속을 통해 공유하여 사용하는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b5def9b3-34de-4a9e-8500-77c4ea6e034f" width="100%">

프로토타입 상속에 대한 위 예제를 아래 그림과 같이 표현할 수 있다. 결론적으로, JS에서는 프로토타입을 통해 프로그래밍적으로 상속이라는 개념을 갖게 된다. 상속이란 특정 객체의 상위(부모) 객체의 메서드를 포함한 프로퍼티를  하위(자식) 객체에게 공유할 수 있는 특성을 의미한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/36e70303-be8a-43e0-a969-28c632ad8415" width="100%">

자식 객체는 부모 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용이 가능하다

```javascript
// 메서드뿐만 아닌 지정한 프로퍼티를 모두 공유할 수 있게 된다
function Square(sideLength) {
  this.sideLength = sideLength;
}

Square.prototype.double = 2;
Square.prototype.getDoubledArea = function() {
  return Math.pow(this.sideLength, 2) * this.double;
}

const square = new Square(5);

console.log(square); // → Square { sideLength: 5 }
console.log(square.getDoubledArea()); // → 50

console.log(Square.prototype); // → {double: 2, getDoulbedArea: [Function] }
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f2e5a109-68bd-484a-be11-2d3c2ff3a300" width="100%">

<br>

## 3 프로토타입 객체

`prototype` 객체는 아래와 같이 여러 개의 프로퍼티를 갖는다:

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/fc0d09cc-0bc3-4537-aec3-18541393ea3d" width="100%">

이 중에서  `constructor` 프로퍼티와 `__proto__` 접근자 프로퍼티를 살펴보자

### 3-1 constructor 프로퍼티(feat. 생성자 함수)

모든 `prototype` 객체는 `constructor` 프로퍼티를 갖는다. `constructor` 프로퍼티는 `prototype` 객체를 참조하고 있는 생성자 함수를 가리킨다

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

const square = new Square(5);

console.log(square); // → Square { sideLength: 5 }
console.log(square.constructor === Square); // → true
console.log(square.constructor); // → ƒ Square(sideLength) { this.sideLength = sideLength } 
```

위 예제서 살펴볼 수 있듯이, `Square` 생성자 함수를 통해 `square` 인스턴스를 생성했다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/0303980c-5737-4573-aa0d-42a2736e6cc4" width="100%">

이때 `square` 인스턴스는 `prototype` 객체의 `constructor` 프로퍼티를 통해 "`square` 인스턴스 → `Square.prototype` 객체 → `Square` 생성자 함수" 순으로 생성자 함수와 연결된다. `constructor` 프로퍼티는 `prototype` 객체를 참조하고 있는 생성자 함수가 누구인지를 가리키므로, 생성자 함수와 인스턴스 사이에는 연결성이 확보된다. 이로 인해서 `square` 인스턴스는 `constructor` 프로퍼티 뿐만 아니라 `Square.prototype` 객체의 모든 프로퍼티를 사용할 수 있게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/68681d96-aeaa-48b8-a6af-c79ffcb6576a" width="100%">

### 3-2 constructor 프로퍼티(feat. 객체 리터럴)

앞서 살펴본대로, 생성자 함수에 의해 생성된 인스턴스는 `prototype` 객체의 `constructor` 프로퍼티를 통해 생성자 함수와 연결된다

```javascript
// Object 생성자 함수
const obj = new Object();
console.log(obj.constructor === Object); // → true

// String 생성자 함수
const strObj = new String("Jace"); 
console.log(strObj.constructor === String);

// Function 생성자 함수 
const sayHello = new Function(console.log("sayHello"));
console.log(sayHello.constructor === Function);

// 생성자 함수
function Square(sideLength) {
  this.sideLength = sideLength;
}

const square = new Square(5);
console.log(square.constructor === Square); // → true
```

그러나 객체 리터럴과 같은 리터럴 표기법에 의해 생성된 객체는 인스턴스를 따로 생성하지 않는다. 직접적으로 객체를 생성할 뿐이다

```javascript
const obj = {};
const sayHello = function() { console.log("sayHello") };
const arr = [1, 2, 3];
```

신기하게도 아래 예제를 살펴보면, `Object` 생성자 함수에 의해 생성된 객체 아닌 객체 리터럴에 의해 생성된 객체가 `Object` 생성자 함수와 연결되어 있다

```javascript
// 객체 리터럴에 의한 객체 생성 방식
const user = { name: "Jace" };

console.log(user.constructor === Object); // → true
```

이는 객체 리터럴에 의해 생성된 객체도 상속을 위해 `prototype` 객체와 연결되어 있어야 하므로, 리터럴 표기법에 의해 생성되는 객체도 가상의 생성자 함수를 갖게 되기 때문이다. 즉, 명시적으로 생성자 함수를 통해 인스턴스를 생성하지 않아도 '상속'이라는 기능을 활용하기 위해 JS 엔진은 가상의 생성자 함수를 만들어 생성자 함수, `prototype` 객체, 객체 리터럴에 의해 생성된 객체를 연결 짓는다는 것이다. 이는 생성자 함수와 `prototype` 객체는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다

> 그러나 엄밀히 말하면, 리터럴 표기법(객체 리터럴, 함수 리터럴, 배열 리터럴, 정규표현식 리터럴 등)에 의해 생성된 객체는 생성자 함수에 의해 생성된 인스턴스는 아니다. 다만, ECMAScript 사양에서도 리터럴 표기법에 의해 생성된 객체를 생성자 함수에 의해 생성된 인스턴스로 생각해도 크게 무리가 없다는 내용이 기재되어 있다(생성 동작에 대한 차이만 존재할 뿐 본질적으로 크게 다르지 않기 때문이다)

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/5da12676-b98f-437f-be99-c7f27e95f5e5" width="100%">

따라서, 리터럴 표기법에 의해 생성된 객체와 연결된 생성자 함수와 `prototype` 객체는 아래와 같다: 

| 리터럴 표기법      | 생성자 함수            | `prototype` 객체     |
| ------------------ | ---------------------- | -------------------- |
| 객체 리터럴        | `Object` 생성자 함수   | `Object.prototype`   |
| 함수 리터럴        | `Function` 생성자 함수 | `Function.prototype` |
| 배열 리터럴        | `Array` 생성자 함수    | `Array.prototype`    |
| 정규 표현식 리터럴 | `RegExp` 생성자 함수   | `RegExp.prototype`   |

### 3-3 proto 접근자 프로퍼티

모든 객체, 프로토타입과 생성자 함수는 서로 연결되어 있는 것을 살펴보았다. `prototype` 객체의 `constructor` 프로퍼티를 통해서 생성자 함수와 연결되고, 인스턴스는 `[[Prototype]]` 내부 슬롯을 통해 `prototype` 객체와 연결된다







![스크린샷 2023-07-12 오전 9.26.27](/Users/jace/Library/Application Support/typora-user-images/스크린샷 2023-07-12 오전 9.26.27.png)

[[prototype]] 내부 슬롯에는 직접 접근할 수 없지만, 위 그림처럼 proto 접근자 프로퍼티를 통해 객체 자신의 [[prototype]] 내부 슬롯이 저장하고 있는 참조 값이 가리키는 프로토타입에 간접적으로 접근할 수 있다. 

그리고 그 프로토타입은 자신의 constructor 프로퍼티를 통해 생성자 함수에 접근할 수 있고, 생성자 함수는 자신의 prototype 프로퍼티를 통해 프로토타입에 접근할 수 있다



생성자 함수.prototype은 생성자 함수 측면에서 봤을 때는 생성자 함수의 프로퍼티가 되는 것이고. 

prototype 하나만을 본다면, 특정 데이터와 로직이 담겨 있는 객체가 되는 것. 

한마디로, JS에서는 객체가 생성되면 prototype이라는 객체가 자동적으로 생성이 되어, 그 안에 있는 프로퍼티들이  각각의 기능으로서 동작하도록 디폴트로 설계되어 생성되고 객체의 [[prototype]] 내부 슬롯에 할당되어 참조 값이 저장되는 그런 JS만의 로직이 있는 것



모든 객체는 `[[Prototype]]` 내부 슬롯을 하나씩 가지며, 객체 생성 방식에 따라 프로토타입 객체의 형태가 결정되고 이는 `[[Prototype]]` 내부 슬롯에 저장된다. 아래 예제에서 볼 수 있듯이, 객체를 참조하면 객체의 프로퍼티 외에도 해당 객체의 프로토타입 값을 확인해볼 수 있다:

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b9586957-2fb6-4ae6-8ff7-d68f913290e9" width="100%">

이때 내부 슬롯은 [프로퍼티 어트리뷰트](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md) 파트에서 언급했듯이 개발자가 직접적으로 접근해서 제어할 수 없다. 그러나 `__proto__` 접근자 프로퍼티를 통해 `[[Prototype]]` 내부 슬롯에 저장된 프로토타입에 간접적으로 접근해서 객체 정보를 참조할 수 있다: 

> [접근자 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#3-1-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)는 직접적으로 값을 갖지 않고, 데이터 프로퍼티의 값을 참조하여 값을 반환해주는 프로퍼티다. `__proto__` 접근자 프로퍼티는 `getter`/`setter` 접근자 함수를 통해 `[[Prototype]]` 내부 슬롯에 저장된 객체의 프로토타입 객체 값을 간접적으로 취득하여 그 값(정보)을 반환해주는 것이다

```javascript
const user = {
	name: "Jace",
}

// 객체 user의 프로토타입을 참조한다
console.log(user.__proto__); // → {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
```

여기서 중요한 것은 `__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 [데이터 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#3-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)가 아니다

```javascript
const user = {
  name: "Jace",
}

// 객체 user가 직접적으로 소유하는 프로퍼티는 데이터 프로퍼티다
console.log(user); // → { name: "Jace" }
```

`__proto__` 접근자 프로퍼티는 바로 모든 객체의 최상위 객체인 `Object.prototype` 객체의 프로퍼티인 것이다. 위 예제에서 객체 `user`는 최상위 객체인 `Object.prototype` 객체의 자식 객체로서, 부모 객체의 프로퍼티를 상속받아 자유롭게 사용할 수 있는 것이다. 따라서 모든 객체는 상속을 통해 `Object.prototype` 객체의 프로퍼티인 `__proto__` 접근자 프로퍼티를 사용하여 각 객체의 프로토타입 정보를 참조할 수 있는 것이다

> 아직까지 살펴보지 않은 '최상위 객체'에 대한 내용은 다음에 나올 내용들을 참고하자

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/99252263-c61a-4847-afd8-72f834952fa8" width="100%">

실제로 아래 예제와 같이 코드를 실행하면 객체 `user`는 데이터 프로퍼티를 갖지만 `__proto__` 접근자 프로퍼티를 직접적으로 소유하고 있지 않다는 것을 알 수 있다

```javascript
const user = { name: "Jace" };

console.log(user.hasOwnProperty("name")); // → true
console.log(user.hasOwnProperty("age")); // → false
console.log(user.hasOwnProperty("__proto__")); // → false

// __proto__ 접근자 프로퍼티는 최상위 객체인 Object.prototype 객체가 직접적으로 소유한다
console.log(Object.prototype.hasOwnProperty("__proto__")); // → true
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__")); // → {enumerable: false, configurable: true, get: ƒ, set: ƒ}
```

그러나 실제 코드 작성 시 프로토타입에 대한 정보를 참조하고 싶은 경우 `__proto__` 접근자 프로퍼티를 사용하지 않는다. 다음에 나올 내용 직접 상속 부분에서 확인할 수 있듯이, 최상위 객체인 `Object.prototype`으로부터 프로퍼티를 상속받지 않는 객체를 생성할 수 있기에 `__proto__` 접근자 프로퍼티를 사용할 수 없는 경우가 존재하기 때문이다

따라서, `Object.getPrototypeOf` 메서드를 통해 `__proto__` 접근자 프로퍼티의 역할을 대체하고, 프로토타입을 교체하고 싶은 경우 `Object.setPrototypeOf` 메서드를 사용하는 것이 좋다

```javascript
const user1 = {};
const user2 = { name: "Jace" };

Object.getPrototypeOf(user1);
Object.setPrototypeOf(user1, user2);

console.log(user1.name); // → Jace
```







***





앞서 JS에서 모든 객체는 `[[Prototype]]` 내부 슬롯을 가지며, 이 내부 슬롯이 저장하는 프로토타입의 참조 값을 통해 `prototype` 객체와 연결될 수 있다 했다

여기서 주목할 점은 모든 객체가 가지는 `[[Prototype]]` 내부 슬롯의 값인 프로토타입의 참조 값은 객체 생성 방식에 의해서 상이할 수 있다는 것이다. 객체 생성 방식은 아래와 같다: 

- 객체 리터럴에 의한 객체 생성
- 생성자 함수에 의한 객체 생성
- `Object.create` 메서드에 의한 객체 생성
- 클래스에 의한 객체 생성

### 3-1 객체 리터럴에 의한 객체 생성 방식과 프로토타입

객체 리터럴에 의해 생성된 객체의 `[[Prototype]]` 내부 슬롯은 `Object`에 할당되어 있는 `prototype` 객체의 참조 값을 저장하게 된다. 즉, `Object.prototype` 객체를 가리키는 참조 값을 내부 슬롯에 저장하는 것이다

```javascript
// 객체 리터럴에 의한 객체 생성 방식
const user = { name: "Jace" };

user.prototype.age = 31; // → Uncaught TypeError: Cannot set properties of undefined (setting 'age')
Object.prototype.age = 31;

console.log(user.age); // → 31
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/54b55930-c891-46ba-a662-602afd3821a4" width="100%">

그렇다면  `Object.prototype`의 `Object`는 어디서 튀어나온 것이며, 객체 리터럴로 생성한 객체는 왜 `Obje 



생성자 함수에 의해 생성된 객체의  `[[Prototype]]` 내부 슬롯에는 생성자 함수의 프로퍼티인 `prototype` 프로퍼티를 가리키는 참조 값이 저장된다. 

> 생성자 함수에 의해 생성된 인스턴스 관점에서 보자면,  `prototype` 객체는 생성자 함수의 `prototype` 프로퍼티다

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

Square.prototype.sideAngle = 90;

const square = new Square(5);

console.log(square.sideAngle); // → 90
```

















## 3 proto 접근자 프로퍼티

모든 객체는 `[[Prototype]]` 내부 슬롯을 하나씩 가지며, 객체 생성 방식에 따라 프로토타입 객체의 형태가 결정되고 이는 `[[Prototype]]` 내부 슬롯에 저장된다. 아래 예제에서 볼 수 있듯이, 객체를 참조하면 객체의 프로퍼티 외에도 해당 객체의 프로토타입 값을 확인해볼 수 있다:

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b9586957-2fb6-4ae6-8ff7-d68f913290e9" width="100%">

이때 내부 슬롯은 [프로퍼티 어트리뷰트](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md) 파트에서 언급했듯이 개발자가 직접적으로 접근해서 제어할 수 없다. 그러나 `__proto__` 접근자 프로퍼티를 통해 `[[Prototype]]` 내부 슬롯에 저장된 프로토타입에 간접적으로 접근해서 객체 정보를 참조할 수 있다: 

> [접근자 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#3-1-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)는 직접적으로 값을 갖지 않고, 데이터 프로퍼티의 값을 참조하여 값을 반환해주는 프로퍼티다. `__proto__` 접근자 프로퍼티는 `getter`/`setter` 접근자 함수를 통해 `[[Prototype]]` 내부 슬롯에 저장된 객체의 프로토타입 객체 값을 간접적으로 취득하여 그 값(정보)을 반환해주는 것이다

```javascript
const user = {
	name: "Jace",
}

// 객체 user의 프로토타입을 참조한다
console.log(user.__proto__); // → {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
```

여기서 중요한 것은 `__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 [데이터 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#3-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)가 아니다

```javascript
const user = {
  name: "Jace",
}

// 객체 user가 직접적으로 소유하는 프로퍼티는 데이터 프로퍼티다
console.log(user); // → { name: "Jace" }
```

`__proto__` 접근자 프로퍼티는 바로 모든 객체의 최상위 객체인 `Object.prototype` 객체의 프로퍼티인 것이다. 위 예제에서 객체 `user`는 최상위 객체인 `Object.prototype` 객체의 자식 객체로서, 부모 객체의 프로퍼티를 상속받아 자유롭게 사용할 수 있는 것이다. 따라서 모든 객체는 상속을 통해 `Object.prototype` 객체의 프로퍼티인 `__proto__` 접근자 프로퍼티를 사용하여 각 객체의 프로토타입 정보를 참조할 수 있는 것이다

> 아직까지 살펴보지 않은 '최상위 객체'에 대한 내용은 다음에 나올 내용들을 참고하자

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/99252263-c61a-4847-afd8-72f834952fa8" width="100%">

실제로 아래 예제와 같이 코드를 실행하면 객체 `user`는 데이터 프로퍼티를 갖지만 `__proto__` 접근자 프로퍼티를 직접적으로 소유하고 있지 않다는 것을 알 수 있다

```javascript
const user = { name: "Jace" };

console.log(user.hasOwnProperty("name")); // → true
console.log(user.hasOwnProperty("age")); // → false
console.log(user.hasOwnProperty("__proto__")); // → false

// __proto__ 접근자 프로퍼티는 최상위 객체인 Object.prototype 객체가 직접적으로 소유한다
console.log(Object.prototype.hasOwnProperty("__proto__")); // → true
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__")); // → {enumerable: false, configurable: true, get: ƒ, set: ƒ}
```

그러나 실제 코드 작성 시 프로토타입에 대한 정보를 참조하고 싶은 경우 `__proto__` 접근자 프로퍼티를 사용하지 않는다. 다음에 나올 내용 직접 상속 부분에서 확인할 수 있듯이, 최상위 객체인 `Object.prototype`으로부터 프로퍼티를 상속받지 않는 객체를 생성할 수 있기에 `__proto__` 접근자 프로퍼티를 사용할 수 없는 경우가 존재하기 때문이다

따라서, `Object.getPrototypeOf` 메서드를 통해 `__proto__` 접근자 프로퍼티의 역할을 대체하고, 프로토타입을 교체하고 싶은 경우 `Object.setPrototypeOf` 메서드를 사용하는 것이 좋다

```javascript
const user1 = {};
const user2 = { name: "Jace" };

Object.getPrototypeOf(user1);
Object.setPrototypeOf(user1, user2);

console.log(user1.name); // → Jace
```

<br>

## 4 함수 객체의 prototype 프로퍼티

앞서 모든 객체는 디폴트로  `[[Prototype]]` 내부 슬롯을 가진다 했다. 그리고 객체 생성 방식에 따라 프로토타입(`prototype` 프로퍼티) 형태가 결정되어 `[[Prototype]]` 내부 슬롯에 저장된다 했다

일반 객체는 최상위 객체인 `Object.prototype`에 `prototype` 프로퍼티가 귀속되어 있으므로, 객체에서 직접으로 `prototype`의 유무를 확인할 시 `false`가 반환되는 것이다

```javascript
// 일반 객체
const user = { name: "Jace" }

console.log(user.prototype); // → undefined
user.hasOwnProperty("prototype"); // → false
```

반면 모든 객체 중에 함수 객체만이 프로토타입(`prototype` 프로퍼티)을 직접적으로 소유한다

```javascript
// 함수 객체
function sayHello() {
  console.log("Hello");
}

console.log(sayHello.prototype); // → { constructor: ƒ }
sayHello.hasOwnProperty("prototype"); // → true
```

왜 함수 객체만이 `prototype` 프로퍼티를 직접적으로 소유할 수 있는것인가? 이는 아래의 이유 때문이다: 

1. 일반적인 함수 정의 방식으로 정의된 함수는 생성자 함수가 될 수 있다. 함수 선언문, 함수 표현식과 같은 방법으로 정의된 함수는 인스턴스를 생성할 수 있는 생성자 함수로서 동작이 가능하다

   > 이는 함수 객체가 `[[Call]]`과 `[[Construct]]` 내부 메서드를 가지기 때문이다.  `new` 연산자와 함께 함수 객체를 호출하게 되면 `[[Construct]]` 내부 메서드가 호출되어 일반적인 함수 객체도 생성자 함수로서 동작할 수 있다

   ```javascript
   function foo() {}
   
   new foo();
   ```

2. 생성자 함수를 통해 생성된 인스턴스들과 프로퍼티 및 메서드를 공유할 수 있는 `prototype` 프로퍼티를 디폴트로 생성하기 때문이다

   <img src="https://github.com/jacenam/WIL-archive/assets/92138751/a23cebe2-8672-4e7b-9e5a-33aa3214a4db" width="100%">

***

### 이게 이해가 안되네





1. 함수가 아닌 일반 객체는 생성되면 최상위 객체인 `Object`라는 놈 밑에 무조건 하위 객체로 놓이게 된다. 그래서 prototype 객체. 즉 Object라는 놈한테 있는 프로퍼티 중 prototype 이라는 프로퍼티는 또 다른 객체 형태로 이루어져 있는 객체 값인거지. 그래서 우리가 생성한 모든 일반 객체들은 모두 이 최상위 객체인 `Object`의 하위 객체로서, `Object`의 프로토타입 프로퍼티의 프로퍼티들을 사용할 수 있는거지. 쉬발...  
   ```javascript
   const user = { name: "Jace" }; 
   undefined
   user;
   {name: 'Jace'}name: "Jace"[[Prototype]]: Objectconstructor: ƒ Object()hasOwnProperty: ƒ hasOwnProperty()isPrototypeOf: ƒ isPrototypeOf()propertyIsEnumerable: ƒ propertyIsEnumerable()toLocaleString: ƒ toLocaleString()toString: ƒ toString()valueOf: ƒ valueOf()__defineGetter__: ƒ __defineGetter__()__defineSetter__: ƒ __defineSetter__()__lookupGetter__: ƒ __lookupGetter__()__lookupSetter__: ƒ __lookupSetter__()__proto__: (...)get __proto__: ƒ __proto__()set __proto__: ƒ __proto__()
   Object;
   ƒ Object() { [native code] }
   Object.prototype.age = 31;
   31
   Object.prototype.intro = function () { 
       console.log(`name is ${this.name}. age is ${this.age}`);
   }
   ƒ () { 
       console.log(`name is ${this.name}. age is ${this.age}`);
   }
   user.age; 
   31
   user.intro();
   VM6293:2 name is Jace. age is 31
   undefined
   const user2 = { name: "Ju Hyung" }
   undefined
   user2.name; 
   'Ju Hyung'
   user2.age;
   31
   user2.intro();
   VM6293:2 name is Ju Hyung. age is 31
   ```

3. 반면, 함수는 좀 달라... 왜냐하면 이 색히는 내부슬롯이랑 내부 메서드부터 일반 객체랑 다르거든... 







***

### 추가할 내용

**객체, 프로토타입, 생성자 함수의 상호 연결성**, **`__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유**



***

### 참고

- [모던 자바스크립트 Deep Dive]()
- [함수의 프로토타입 핵심 정리](https://webclub.tistory.com/509)
