# 프로토타입

### 목차

- [1 프로토타입이란](#1-프로토타입이란)
- [2 프로토타입의 중요성(feat. 상속)](#2-프로토타입의-중요성(feat.-상속))
  - [2-1 생성자 함수의 단점](#2-1-생성자-함수의-단점)
  - [2-2 상속](#2-2-상속)
- [3 proto 접근자 프로퍼티](#3-proto-접근자-프로퍼티)
- [4 함수 객체의 prototype 프로퍼티](#4-함수-객체의-prototype-프로퍼티)

<br>

## 1 프로토타입이란 

아래 예제와 같이 브라우저 환경에서 특정 객체를 참조하면 할당한 프로퍼티가 포함된 객체가 반환된다. 그러나  할당하지 않은 `[[Prototype]]` 내부 슬롯 또한 반환되는 것을 확인할 수 있다. JS의 모든 객체는 디폴트로 `[[Prototype]]` 내부 슬롯을  갖게 된다. 심지어 빈 객체(`{}`)도 `[[Prototype]]` 내부 슬롯을 갖는다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/06bf9b6d-e62d-40ed-bb2d-b6c95f120166" width="100%">

JS는 프로토타입 기반의 [객체지향 언어](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/object%20oriented%20programming.md)다. JS를 이루고 있는 값 중 원시 타입을 제외한 모든 값이 객체다. 따라서 모든 객체가 갖는 프로토타입은 JS에서 가장 중요한 개념 중 하나이며, JS를 C++, Java 같은 클래스 기반의 객체지향 언어와 구분하는 가장 핵심적인 요소라고 볼 수 있다. 

프로토타입(Prototype)의 사전적 의미는 '원형(原型)'으로 "다양한 모습으로 바뀌기 이전의 원래의 모습"을 뜻한다.  그렇다면 JS에서 프로토타입(원형)은 왜 중요한 것일까?

<br>

## 2 프로토타입의 중요성(feat. 상속)

### 2-1 생성자 함수의 단점

앞서 [생성자 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/constructor%20function.md) 파트에서 살펴봤듯이, 객체 리터럴은 하나의 리터럴로 단 하나의 객체만을 생성할 수 있다. 따라서 유사 구조를 가진 객체를 여러 개 생성할 시 생성자 함수를 통해 객체 리터럴보다 편한 방식으로 다수의 객체를 생성 및 관리할 수 있게 된다. 생성자 함수에 대한 아래 예제를 살펴보자:

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

> 메서드는 객체에 할당되어 있는 함수다. 따라서 객체와 동일하게 [참조에 의한 저장 방식](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/primitive%20%26%20object%20type.md#3-2-%EA%B0%9D%EC%B2%B4-%EA%B0%92%EC%9D%98-%EC%A0%80%EC%9E%A5)을 통해 메모리에 객체 값이 저장된다. 위 예제에서 /각 인스턴스의 메서드가 모두 동일해보이지만 저장되는 메모리 공간이 모두 다르다. 즉, 각 메서드에 대한 메모리 참조 값이 다르다

```javascript
console.log(square1.getArea === square2.getArea === square3.getArea); // → false
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/d6b2e87b-0645-403f-b4ec-dd5c7de1ef6d" width="100%">

### 2-2 상속

생성자 함수를 통해 생성된 인스턴스 내의 불필요한 중복을 줄이기 위해서는 프로토타입이 필요하다. JS는 프로토타입을 기반으로 원형으로부터 변화된 상태의 인스턴스(원형 → 인스턴스)들로 특정 요소들을 상속시킬 수 있다. 예를 들어, 위 예제에서 각 인스턴스에 중복으로 할당된 메서드 `getArea`를 프로토타입 상속으로 제거해보자:

```javascript 
function Square(sideLength) {
  this.sideLength = sideLength;
}

// 생성자 함수 Sqaure의 프로퍼티 prototype에, 내부 프로퍼티 getArea에 함수를 할당한 것
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

> 생성자 함수 `Sqaure`의 프로퍼티 `prototype` 내부에, 프로퍼티 `getArea`에 함수를 할당한 것이다. 이는 [마침표 프로퍼티 접근 연산자](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#32-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%A0%91%EA%B7%BC)를 통해 [프로퍼티를 동적 생성](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#34-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%9D%98-%EB%8F%99%EC%A0%81-%EC%83%9D%EC%84%B1-%EC%82%AD%EC%A0%9C-%EB%B0%8F-%ED%99%95%EC%9D%B8)한 것이다. 객체의 프로퍼티 값에는 어떠한 값이 올 수 있기 때문에, 프로퍼티 내부에 객체와 프로퍼티를 할당할 수 있다. 앞서 언급했듯이, 모든 객체는 디폴트로 `[[Prototype]]` 내부 슬롯을 가지기 때문에 프로퍼티 `prototype`을 호출할 수 있다
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

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/d5fb71b2-6f34-4c93-8c9d-9229ddf453ce" width="100%">

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

<br>

## 3 proto 접근자 프로퍼티

모든 객체는 `[[Prototype]]` 내부 슬롯을 하나씩 가지며, 객체 생성 방식에 따라 프로토타입 객체의 형태가 결정되고 이는 `[[Prototype]]` 내부 슬롯에 저장된다. 아래 예제에서 볼 수 있듯이, 객체를 참조하면 객체의 프로퍼티 외에도 해당 객체의 프로토타입 값을 확인해볼 수 있다:

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b9586957-2fb6-4ae6-8ff7-d68f913290e9" width="100%">

이때 내부 슬롯에 저장된 프로토타입 객체는 [프로퍼티 어트리뷰트](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md) 파트에서 언급했듯이 개발자가 직접적으로 접근해서 제어할 수 없다. 그러나 `__proto__` 접근자 프로퍼티를 통해 객체의 `[[Prototype]]` 내부 슬롯에 저장된 프로토타입에 간접적으로 접근해서 객체 정보를 참조할 수 있다: 

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

앞서 모든 객체는 디폴트로  `[[Prototype]]` 내부 슬롯을 가진다 했다. 그리고 `[[Prototype]]` 내부 슬롯은 프로토타입(`prototype` 프로퍼티)이 저장되어 있는 객체를 가리킨다. 일반 객체는 최상위 객체인 `Object.prototype`에 `prototype` 프로퍼티가 귀속되어 있으므로, 객체에서 직접으로 `prototype`의 유무를 확인할 시 `false`가 반환되는 것이다. 반면 모든 객체 중에 함수 객체만이 프로토타입(`prototype` 프로퍼티)을 직접적으로 소유한다

```javascript
// 함수 객체
function sayHello() {
  console.log("Hello");
}
sayHello.hasOwnProperty("prototype"); // → true

// 일반 객체
const user = { name: "Jace" }
user.hasOwnProperty("prototype"); // → false
```





### 추가할 내용

**객체, 프로토타입, 생성자 함수의 상호 연결성**, **`__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유**



***

### 참고

- [모던 자바스크립트 Deep Dive]()
- [함수의 프로토타입 핵심 정리](https://webclub.tistory.com/509)
