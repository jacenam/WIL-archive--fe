# 프로토타입

JS는 프로토타입 기반의 [객체지향 언어](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/object%20oriented%20programming.md)다. JS를 이루고 있는 값 중 원시 타입을 제외한 모든 값이 객체다. 따라서 모든 객체 연결되어 있는 프로토타입은 JS에서 가장 중요한 개념 중 하나이며, JS를 C++, Java 같은 클래스 기반의 객체지향 언어와 구분하는 가장 핵심적인 요소라고 볼 수 있다. 

**Table of Contents**

- [프로토타입이란](#프로토타입이란)
- [프로토타입과 상속](#프로토타입과-상속)
  - [생성자 함수의 단점](#생성자-함수의-단점)
  - [상속](#상속)
  - [정적 프로퍼티와 메서드](#정적-프로퍼티와-메서드)

- [프로토타입 객체](#프로토타입-객체)
  - [프로토타입 객체의 프로퍼티 구성](#프로토타입-객체의-프로퍼티-구성)
    - [객체 리터럴](#객체-리터럴)
    - [생성자 함수](#생성자-함수)
    - [그 외의 생성자 함수](#그-외의-생성자-함수)

  - [constructor 프로퍼티(feat. 생성자 함수)](#constructor-프로퍼티(feat.-생성자-함수))
  - [constructor 프로퍼티(feat. 객체 리터럴)](#constructor-프로퍼티(feat.-객체-리터럴))
  - [proto 접근자 프로퍼티](#proto-접근자-프로퍼티)

- [함수 객체의 프로토타입](#함수-객체의-프로토타입)
  - [생성자 함수와 프로토타입](#생성자-함수와-프로토타입)
  - [Non-constructor 함수와 프로토타입](#Non-constructor-함수와-프로토타입)

<br>

## 프로토타입이란 

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

## 프로토타입과 상속

### 생성자 함수의 단점

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
>
> ```javascript
> console.log(square1.getArea === square2.getArea === square3.getArea); // → false
> ```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/d6b2e87b-0645-403f-b4ec-dd5c7de1ef6d" width="100%">

### 상속

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

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/0c70977b-d8bb-42ae-bfed-ae219a91e257" width="100%">

프로토타입 상속에 대한 위 예제를 아래 그림과 같이 표현할 수 있다. 결론적으로, JS에서는 프로토타입을 통해 프로그래밍적으로 상속이라는 개념을 갖게 된다. 상속이란 특정 객체의 상위(부모) 객체의 메서드를 포함한 프로퍼티를  하위(자식) 객체에게 공유할 수 있는 특성을 의미한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/bb05f2c6-c70c-47e1-880d-11795f6cb61f" width="100%">

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

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/d6a8982b-f59b-41c2-b2cb-bd305d2ad448" width="100%">

### 정적 프로퍼티와 메서드

만약 `prototype` 객체를 통한 프로퍼티 및 메서드 상속을 원하지 않는 경우 명시적으로 상속되지 않는 프로퍼티와 메서드를 지정할 수 있다. 아래 예제를 살펴보자

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

// 생성자 함수 Sqaure의 prototype 프로퍼티 안에 있는 프로퍼티 getArea에 함수를 할당한 것
Square.prototype.getArea = function() {
	return Math.pow(this.sideLength, 2);
};

// 정적 프로퍼티 추가
Square.staticProp = "a static property only for constructor function";
// 정적 메서드 추가
Square.staticMethod = function() {
  console.log("a static method only for constructor function")
};

const square1 = new Square(5); 
const square2 = new Square(10);
const square3 = new Square(20);

console.log(square1.staticProp); // → undefined
console.log(square2.staticMethod()); // → Uncaught TypeError: square2.staticMethod is not a function
```

위 예제에서 살펴볼 수 있듯이, 정적 프로퍼티와 메서드란 생성자 함수에 한해서 사용 가능한 프로퍼티와 메서드를 의미한다. 다시 말해 `Square` 생성자 함수는 갖는 프로퍼티와 메서드이지만 생성자 함수에 의해 생성된 인스턴스는 참조 혹은 호출할 수 없는 프로퍼티와 메서드를 의미하는 것이다 

<br>

## 프로토타입 객체

`prototype` 객체는 생성자 함수 측면에서 봤을 때 생성자 함수의 프로퍼티가 되는 것이며, `prototype` 객체 측면에서 봤을 때는 특정 데이터와 로직이 담겨있는 객체 되는 것이다. 다시 말해, 생성자 함수의 `prototype` 프로퍼티이자 `prototype` 자체적으로는 객체가 되는 것이다. 그럼  `prototype` 객체의 구조를 살펴보자

먼저 주목해야할 부분으로 객체를 생성하는 방식은 객체 리터럴, `Object` 생성자 함수, 생성자 함수 등 다양한 방법이 존재한다는 것이다. 객체 생성 방식에 따라 `prototype` 객체의 구성이 다르다

```javascript
// 일반 함수 정의 방식으로 정의된 생성자 함수
function Square(sideLength) {
  this.sideLength = sideLength;
}

// Object 생성자 함수
const obj = new Object();

// Function 생성자 함수 
const sayHello = new Function(console.log("sayHello"));

// String 생성자 함수
const strObj = new String("Jace"); 

// 객체 리터럴 방식
const user = { name: "Jace" }

......
```

### 프로토타입 객체의 프로퍼티 구성

#### 객체 리터럴

먼저 객체 리터럴에 의해 생성된 일반 객체의 `prototype` 객체를 살펴보자. 브라우저 환경에서 실행된 아래 예제를 살펴보면 객체 리터럴로 빈 객체(`{}`)를 생성했다. 그리고 살펴보면 `[[Prototype]]` 내부 슬롯이 가리키는 `prototype` 객체는 `Object`이며, 프로퍼티 구성이 다양하다

> 객체 리터럴에 의해 생서된 일반 객체의 `[[Prototype]]` 내부 슬롯은 어째서 `Object`를 가리키는 것일까? 이에 대해서는 [constructor 프로퍼티(feat. 객체 리터럴)](#constructor-프로퍼티(feat.-객체-리터럴))를 살펴보자

> `prototype` 객체의 프로퍼티 전부를 다 상세히 볼 필요는 없다. 이 중에서  `constructor` 프로퍼티와 `__proto__` 접근자 프로퍼티를 [다음에 나올 내용](#3-2-constructor-프로퍼티(feat.-생성자-함수))에서 살펴볼 예정이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/85157efe-a3db-40a4-87ff-aa578d2a008f" width="100%">

#### 생성자 함수

다음은 일반 함수 정의 방식으로 정의된 생성자 함수의 `prototype` 객체를 살펴보자. 프로퍼티가 `constructor` 프로퍼티 1개 뿐이다. 그리고 `[[Prototype]]` 내부 슬롯이 존재하며, 이 내부 슬롯은 `Object`를 가리키고 있다.  [앞서 언급했듯이](#1-프로토타입이란), 이는 일반 함수 정의 방식으로 정의된 생성자 함수의 `prototype` 객체도 객체로서 `[[Prototype]]` 내부 슬롯을 가지기 때문이다 

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ad6e3320-8357-43f2-8b0a-14c2da880770" width="100%">

따라서 아래 예제와 같이 `Square` 생성자 함수의 `Square.prototype` 객체가 소유하지 않은 `hasOwnProperty` 프로퍼티(메서드)를 `Object`로부터 상속받아 사용할 수 있게 된다

```javascript
// Square.prototype은 hasOwnProperty 메서드를 소유하고 있지 않지만 사용이 가능하다
Square.prototype.hasOwnProperty("constructor"); // → true

// Square.prototype은 __proto__ 접근자 프로퍼티를 소유하고 있지 않지만 사용이 가능하다
Square.prototype.__proto__ === Object.prototype; // → true
```

즉 `Square` 생성자 함수, `prototype` 객체(`Square.prototype`), `Object`와의 관계도는 아래 그림과 같이 나타날 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f55e53c4-0f69-41b1-89ee-ad50b801078c" width="100%">

`Function` 생성자 함수도 일반 함수 정의 방식으로 정의한 생성자 함수와 동일하다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a75f5905-64b6-49a6-8c9c-0f856760d524" width="100%">

#### 그 외의 생성자 함수

`Object`, `String`, `Number`, `Array` 등의 생성자 함수는 아래와 같은 `prototype` 객체 구조를 갖는다:

- `Object` 생성자 함수

  <img src="https://github.com/jacenam/WIL-archive/assets/92138751/cef231a8-8ca4-44ab-8c4f-a0c448b6592c" width="100%">

- `String` 생성자 함수

  <img src="https://github.com/jacenam/WIL-archive/assets/92138751/9bd91bd0-c8c3-4515-87f2-110268055482" width="100%">

- `Array` 생성자 함수

  <img src="https://github.com/jacenam/WIL-archive/assets/92138751/8ec6ee2d-e2a7-4970-b529-b629f8905aec" width="100%">

위 생성자들의 `prototype` 객체와의 관계도는 아래 그림과 같이 표현된다(물론 생성자별로 프로퍼티는 약간의 차이가 있다)

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/aae16698-b3d8-4a46-a9a1-11dfef4ff260" width="100%">

여기서 주의해야할 점으로 일반 함수 정의 방식으로 정의된 생성자 함수, `Function` 생성자 함수와는 다르게 다른 생성자 함수들은 모두 지정한 변수명을 이용해 `prototype` 객체를 참조하려하면 `undefined`가 반환된다

```javascript
obj.prototype; // → undefined
strObj.prototype; // → undefined
arr.prototype; // → undefined
```

이는 `prototype` 객체가 생성되었을 때 바인딩되는 주체가 다르기 때문이며, 이는 추후에 다뤄볼 예정이다

### constructor 프로퍼티(feat. 생성자 함수)

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

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/06cf01e9-07e2-42c2-b1e4-1a4165008ac3" width="100%">

### constructor 프로퍼티(feat. 객체 리터럴)

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

### proto 접근자 프로퍼티

모든 객체, 프로토타입과 생성자 함수는 서로 연결되어 있는 것을 살펴보았다. `prototype` 객체의 `constructor` 프로퍼티를 통해서 생성자 함수와 연결되고, 인스턴스는 `[[Prototype]]` 내부 슬롯에 저장되어 `prototype` 객체를 가리키는 참조 값을 통해 `prototype` 객체와 연결된다

그리고 인스턴스는 `prototype` 객체의 프로퍼티를 상속받아 자유롭게 사용이 가능하다. 여기서 `prototype` 객체의 프로퍼티 중 하나인 `__proto__` 접근자 프로퍼티를 인스턴스가 사용하면 인스턴스가 가진 `[[Prototype]]` 내부 슬롯에 간접적으로 접근해 역으로  `prototype` 객체의 정보를 취득할 수 있다. 

> [접근자 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#3-1-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)는 직접적으로 값을 갖지 않고, 데이터 프로퍼티의 값을 참조하여 값을 반환해주는 프로퍼티다. `__proto__` 접근자 프로퍼티는 `getter`/`setter` 접근자 함수를 통해 `[[Prototype]]` 내부 슬롯에 저장된 객체의 프로토타입 객체 값을 간접적으로 취득하여 그 값(정보)을 반환해주는 것이다

아래 예제의 경우, [앞서 언급했듯이](#3-1-2-생성자-함수) 일반 함수 정의 방식으로 정의된 생성자 함수이기 때문에 `Square` 생성자 함수의 `prototype` 객체(`Square.prototype`)는 `constructor` 프로퍼티와 `[[Prototype]]` 내부 슬롯만을 갖는다. 따라서, `Square.prototype`은 부모 `prototype` 객체인 `Object.prototype`으로부터 `__proto__` 접근자 프로퍼티를 상속받고, 이는 `Square` 생성자 함수를 통해 생성된 인스턴스까지 상속이 이어지게 된다 

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/072aeeb9-3a64-4fe3-869a-cbbdb4cf20cc" width="100%">

아래 예제를 살펴보자.  `__proto__` 접근자 프로퍼티는 `user` 객체가 직접적으로 소유하는 프로퍼티가 아니다. `user` 객체의 생성자 함수인 `Object` 생성자 함수의 `prototype` 객체가 `__proto__` 접근자 프로퍼티를 소유하는 것이다

```javascript
const user = { name: "Jace" };

console.log(user.hasOwnProperty("__proto__")); // → false
console.log(Object.prototype.hasOwnProperty("__proto__"));  // → true
```

따라서 객체 리터럴에 의해 생성한 `user` 객체는 `prototype` 객체의 프로퍼티 중 하나인 `__proto__` 접근자 프로퍼티를 상속 받아 사용할 수 있다. 그리고 이를 통해 `prototype` 객체의 정보를 간접적으로 참조할 수 있다

```javascript
const user = { name: "Jace" };

console.log(user.__proto__); // → {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
```

`Object` 생성자 함수의 `prototype` 객체 정보가 `user.__proto__`로 `prototype` 객체에 접근했을 때의 정보와 같은지 확인해보면 아래와 같다

```javascript
const user = { name: "Jace" };

console.log(Object.prototype); // → {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}

console.log(user.__proto__ === Object.prototype); // → true
```

그러나 실제 코드 작성 시 프로토타입에 대한 정보를 참조하고 싶은 경우 `__proto__` 접근자 프로퍼티를 사용하지 않는다. `prototype` 객체의 프로퍼티를 상속받지 않는 객체(인스턴스)를 생성할 수 있기에 `__proto__` 접근자 프로퍼티를 사용할 수 없는 경우가 존재하기 때문이다

따라서, `Object.getPrototypeOf` 메서드를 통해 `__proto__` 접근자 프로퍼티의 역할을 대체하고, 프로토타입을 교체하고 싶은 경우 `Object.setPrototypeOf` 메서드를 사용하는 것이 좋다

```javascript
const user1 = {};
const user2 = { name: "Jace" };

Object.getPrototypeOf(user1);
Object.setPrototypeOf(user1, user2);

console.log(user1.name); // → Jace
```

<br>

## 함수 객체의 프로토타입

### 생성자 함수와 프로토타입

앞서 모든 객체는 `[[Prototype]]` 내부 슬롯을 가지며, 이 내부 슬롯 내부에는 `prototype` 객체를 가리키는 참조 값이 저장되어 있다 했다. 즉, 객체는 `prototype` 객체를 프로퍼티로서 직접적으로 소유하지 않는다

```javascript
const user = { name: "Jace" };

console.log(user.hasOwnProperty("prototype")); // → false
```

앞서 언급한대로, 리터럴 표기법에 의해 생성된 객체는 가상의 생성자 함수와 연결되며  `prototype` 객체는 가상의 생성자 함수를 구성하는 프로퍼티 중 하나가 된다. 즉, 객체와 연결된 가상의 생성자 함수가 `prototype` 객체를 직접적으로 소유하게 된다

```javascript
const user = { name: "Jace" };

console.log(user.hasOwnProperty("prototype")); // → false
console.log(Object.hasOwnProperty("prototype")); // → true
```

반면, 아래 예제에서 알 수 있듯이 신기하게도 함수 객체는 `prototype` 객체를 소유한다. 즉, 함수 객체를 구성하는 프로퍼티 중 하나로 `prototype` 객체가 존재한다

```javascript
// 함수 객체
function sayHello() {
  console.log("Hello");
}

console.log(sayHello.prototype); // → { constructor: ƒ }
sayHello.hasOwnProperty("prototype"); // → true
```

이는 함수 객체가 객체이면서도 생성자 함수로서도 동작이 가능하기 때문이다. 앞서 [프로퍼티 어트리뷰트](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md)와 [생성자 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/constructor%20function.md) 파트에서 살펴봤듯이, 일반적인 함수 정의 방식인 함수 선언문, 함수 표현식으로 정의된 함수는 일반 객체와는 다르게  `[[Call]]`과 `[[Construct]]` 내부 메서드를 가지기 때문이다. 다시 말해, 일반적인 함수 정의 방식으로 정의된 함수는 `new` 연산자와 함께 호출되면 언제든 생성자 함수로서 동작할 수 있다는 것이다

```javascript
function sayHello() {
  return "Hello";
}

const sayHello2 = sayHello();
const sayHello3 = new sayHello();
```

생성자 함수로서 동작하는 `sayHello` 함수는 `prototype` 객체를 갖는다. 그리고 이는 `sayHello` 생성자 함수에 의해 생성된 인스턴스의 `prototype` 객체가 된다는 것을 의미한다. 다시 말해,  `sayHello3` 인스턴스는 `prototype` 객체를 갖지 않고 `prototype` 객체를 가리키는 `[[Prototype]]` 내부 슬롯을 갖는다

```javascript
console.log(sayHello.hasOwnProperty("prototype")); // → true

// sayHello 생성자 함수에 의해 생성된 sayHello2, sayHello3는 프로토타입 객체를 갖지 않는다
console.log(sayHello3.hasOwnProperty("prototype")); // → false
```

여기서 주의해야할 점으로 `sayHello` 함수를 할당받은 `sayHello2`는 변수로서 `sayHello` 함수의 반환값인 `"Hello"` 문자열 원시 타입의 값을 갖기 때문에 객체가 아니므로 `prototype` 객체가 생성되지 않는다

```javascript
// sayHello2 변수에 담긴 값은 원시 타입이므로 prototype와 연결되지 않는다 
console.log(sayHello2.hasOwnProperty("prototype")); // → false
console.log(sayHello2 === "Hello"); // → true
```

### Non-constructor 함수와 프로토타입

앞서 [생성자 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/constructor%20function.md#6-2-constructor%EC%99%80-non-constructor)에서 살펴봤듯이, Non-constructor 함수는 호출할 수 있는 객체이지만 `[[Construct]]`  내부 메서드를 갖지 않는 함수로서, 생성자 함수로 생성할 수 없는 함수다. 즉, non-constructor 함수인 메서드 축약 표현과 화살표 함수는 생성자 함수와 연결되지 않는 함수이기 때문에 `prototype` 객체와도 연결될 수 없다

> 생성자 함수와 `prototype` 객체는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다

```javascript
// 메서드 축약 표현
const User = {
  name: "Jace",
  sayHello() {
    console.log(`Hello, ${this.name}`);
  }
}

console.log(User.hasOwnProperty("prototype")); // → false
```

```javascript
// 화살표 함수
const User = (name) => {
  this.name = name;
}

console.log(User.hasOwnProperty("prototype"));
```

<br>

## 참고

- [모던 자바스크립트 Deep Dive]()
- [함수의 프로토타입 핵심 정리](https://webclub.tistory.com/509)
- [우아한테크 - 크리스의 prototoype](https://youtu.be/RYxgNZW3wl0)
