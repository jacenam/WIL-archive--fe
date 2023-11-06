# this

**Table of Contents**

- [this란](#this란)
- [this 바인딩](#this-바인딩)
- [this의 참조](#this의-참조)

<br>

## this란

앞서 [객체의 구성 요소](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#3-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C) 파트에서 객체는 객체의 상태를 나타내는 정적 데이터 프로퍼티와 동작을 나타내는 동적 데이터 메서드를 하나의 단위로 묶은 자료구조라 했다

객체의 동작을 나타내는 메서드는 자신이 속한 객체의 상태(프로퍼티)값을 참조할 수 있어야 한다. 일반적으로 메서드는 프로퍼티를 활용해 메서드의 결과값을 반환하기 때문이다

이때 메서드가 자신이 속한 객체의 프로퍼티를 활용하려면 자신이 속한 객체를 가리키는 식별자(객체 이름)를 통해 프로퍼티에 접근할 수 있어야 한다

권장하는 방법은 아니지만 객체 리터럴 방식을 통해 생성된 객체의 경우, 메서드 자신이 속한 객체를 가리키는 식별자를 통해 자체적으로 프로퍼티에 접근할 수 있다

```javascript
const square = {
  sideLength: 5, 
  getArea() {
    // 메서드 자신이 속한 객체의 식별자인 square를 통해 프로퍼티에 접근할 수 있다
    return Math.pow(square.sideLength, 2);
  }
};

console.log(square.getArea()); // → 25
```

그렇다면 위 예제의 `getArea` 메서드 내부에서 어떻게 자기 자신이 속한 객체의 식별자를 참조할 수 있는것일까? 

1. 런타임 이전 - 객체 리터럴을 담을 변수 선언문의 호이스팅

   > JS는 인터프리터 언어로 코드가 위에서 아래로 순차적으로 실행된다. 소스코드가 순차적으로 실행되는 시점을 런타임이라 부른다

   > [`let`, `const` 키워드로 선언한 변수는 호이스팅](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Scope/var%2C%20let%2C%20const.md#2-3-%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)이 이뤄지지 않는것처럼 보이지만, 실제로 호이스팅이 이뤄진다

   <img src="https://github.com/jacenam/WIL-archive/assets/92138751/9a2c2310-0ed7-4c59-863f-b99f626c2b02" width="100%">

2. 런타임 이후 - 객체 리터럴의 평가(객체의 생성) 및 `square` 변수에 객체 할당, 메서드 호출

   <img src="https://github.com/jacenam/WIL-archive/assets/92138751/bdd2f90d-d1d2-44ea-ab8f-e1e401052b4e" width="100%">

3. `getArea` 메서드가 호출되는 시점에는 이미 객체가 생성되어 `square` 변수에 할당된 시점이므로, `getArea` 메서드 내부에서 자신이 속한 `square` 객체의 식별자를 재귀적으로 참조할 수 있게 된다

   <img src="https://github.com/jacenam/WIL-archive/assets/92138751/7868b3b9-7a0e-4250-89ef-75cdd2590fcb" width="100%">

객체 리터럴 방식으로 생성한 객체는 메서드 자신이 속한 객체로부터 참조할 수 있는 식별자가 있다 치자. 그렇다면 생성자 함수를 통해 생성되는 인스턴스(객체)의 경우는 어떠할까? 먼저 생성자 함수, `prototype` 객체, 인스턴스의 생성 과정을 살펴보자: 

1. 런타임 이전 - 생성자 함수에 대한 정의와 프로토타입 생성

   > [함수 선언문으로 정의한 생성자 함수는 런타임 이전에 평가되어 함수가 생성](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#4-2-%ED%95%A8%EC%88%98-%EC%83%9D%EC%84%B1-%EC%8B%9C%EC%A0%90%EA%B3%BC-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)된다. 즉, 호이스팅이 발생한다

   ```javascript
   // 함수 선언문이 호이스팅되어 참조문이 먼저 와도 Square 함수에 대한 참조가 가능하다
   console.log(Square); // → ƒ Square(sideLength) {}
   
   function Square(sideLength) {} 
   ```

   > `prototype` 객체는 언제나 생성자 함수와 쌍으로 존재하며, [생성자 함수가 정의되어 생성되는 시점에 함께 생성](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype%20creation.md)된다

   ```javascript
   console.log(Square); // → ƒ Square(sideLength) {}
   // 프로토타입 객체는 생성자 함수 객체와 생성되지만 런타임 이전에는 값의 할당이 이뤄지지 않는다. 따라서 참조 시 에러가 발생하지 않고 undefined가 반환된다
   console.log(Square.prototype.getArea); // → undefined
   
   function Square(sideLength) {}
   
   // 런타임 이전에는 할당이 이뤄지지 않는다
   Square.prototype.getArea = function() {};
   ```

2. 런타임 이후 - `Square.prototype` 객체의 `getArea` 프로퍼티에 함수표현식을 할당, `new` 연산자와 함께 생성자 함수 호출, 인스턴스 생성

   ```javascript
   console.log(Square); // → ƒ Square(sideLength) {}
   console.log(Square.prototype.getArea); // → undefined
   
   function Square(sideLength) {}
   
   Square.prototype.getArea = function() {}
   
   const square = new Square();
   
   // 런타임 이후 값의 할당으로 getArea에 함수표현식 할당 완료
   console.log(Square.prototype.getArea); // → ƒ () {}
   console.log(square); // → Square {}
   ```

위 `Square` 생성자 함수 예제에서는 생성자 함수와 `Square.prototype` 객체에 프로퍼티 혹은 메서드를 추가하지 않았다. 만약 프로퍼티 혹은 메서드를 추가하려면 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자가 있어야만 한다. 다시 말해, 생성자 함수에 프로퍼티 혹은 메서드를 추가하려면 그 프로퍼티 혹은 메서드가 활용되는 대상이 식별되어야만 한다는 것이다

```javascript
function Square(sideLength) {
  // 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다
  ????.sideLength = sideLength;
}

Square.prototype.getArea = function () {
  // 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다
  return Math.pow(????.sideLength, 2);
};

const square = new Square(5);
```

또한 생성자 함수가 호출되어 인스턴스를 생성하려면 생성자 함수가 먼저 정의되어야 한다. 그러나 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없는 상태에서는 생성자 함수를 정의할 수 없다

```javascript
function Square(sideLength) {
  ????.sideLength = sideLength;
} // → Uncaught SyntaxError: Unexpected token '??'
```

따라서 메서드 자신이 속한 객체 혹은 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 위해 JS는 `this` 라는 특수한 식별자를 제공하는 것이다. `this`는 메서드 자신이 속한 객체 또는 생성자 함수 자신이 생성할 인스턴스를 가리키는 자기 참조 변수(Self-referencing Variable)다

<br>

## this 바인딩

바인딩(Binding)이란 식별자와 값을 연결하는 것을 의미한다. 이미 앞서서 바인딩 현상을 살펴본 적이 있다. 바로 변수의 선언을 통해 변수 이름과 값을 저장할 메모리 공간의 주소를 연결하는 것이다. 따라서 `this` 바인딩은 `this` 식별자와 `this`가 가리킬 객체를 연결하는 것이다

아래 에제를 살펴보자. 위 `this` 소개에서 살펴본 예제다. 아래 예제의 객체는 객체 리터럴 방식을 통해 생성된 객체이기 때문에 메서드 자신이 속한 객체를 가리키는 식별자 `square`를 통해 재귀적으로 프로퍼티에 접근할 수 있었다

```javascript
// 객체 리터럴 방식에 의해 생성된 객체
const square = {
  sideLength: 5, 
  getArea() {
    // 메서드 자신이 속한 객체를 가리키는 식별자를 통해 프로퍼티에 접근
    return Math.pow(square.sideLength, 2);
  }
};

console.log(square.getArea()); // → 25
```

`this`를 통해 위 예제를 수정하면 아래와 같다. 여기서 `this` 특수 식별자는 객체 리터럴 방식에 의해 생성된 객체의 메서드를 호출한 객체(즉 `square`)를 가리킨다

```javascript
const square = {
  sideLength: 5, 
  getArea() {
    return Math.pow(this.sideLength, 2);
  }
}

// this 식별자는 메서드를 호출한 객체를 가리킨다
console.log(square.getArea()); // → 25
```

위 `this` 소개에서 살펴본 생성자 함수 예제도 살펴보자. 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자가 없었기 때문에 생성자 함수에 프로퍼티나 메서드를 추가해 사용할 수 없었다 

```javascript
function Square(sideLength) {
  // 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다
  ????.sideLength = sideLength;
}

Square.prototype.getArea = function () {
  // 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다
  return Math.pow(????.sideLength, 2);
};

const square = new Square(5);
```

`this`를 통해 위 예제를 수정하면 아래와 같다. 여기서 생성자 함수 내부의 `this` 식별자는 생성자 함수 자신이 생성할 인스턴스를 가리킨다

```javascript
function Square(sideLength) {
  // 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자가 생겼다
  this.sideLength = sideLength;
}

Square.prototype.getArea = function () {
  // 메서드를 호출할 객체를 가리키는 식별자가 생겼다
  return Math.pow(this.sideLength, 2);
};

// 생성자 함수 호출
const square = new Square(5);
// this 식별자는 메서드를 호출한 객체를 가리킨다
console.log(square.getArea()); // → 25
```

이처럼 `this` 식별자는 상황에 따라 가리키는 대상이 다르다는 것을 알 수 있다. 이는 `this`가 가리키는 값, 즉 `this` 바인딩은 함수 호출 방식에 의해 동적으로 결정된다. 따라서 함수 호출 방식에 따라 상이한다

<br>

## this의 참조

`this` 식별자는 전역, 함수 내부(지역) 등 코드 어디에서든 참조가 가능하다. 전역과 지역에서의 참조에 따라 반환되는 참조값도 다르다. 아래 예제를 살펴보자

```javascript
// 전역에서 this를 참조하면 전역 객체 window를 가리킨다
console.log(this); // → Window {window: Window, self: Window, document: document, name: '', location: Location, …}

function triangle(base, height) {
  // 함수 선언문 내부에서 this를 참조하면 전역 객체 window를 가리킨다
  console.log(this); // → Window {window: Window, self: Window, document: document, name: '', location: Location, …}
  return (base * height) / 2
}
triangle(10, 5); 

const circle = function(radius) {
  // 함수 표현식 내부에서 this를 참조하면 전역 객체 window를 가리킨다
  console.log(this); // → Window {window: Window, self: Window, document: document, name: '', location: Location, …}
  return Math.pow(radius, 2) * Math.PI;
}
circle(5);

const user = {
  name: "Jace",
  getName() {
    // 객체의 메서드 내부에서 this를 참조하면 메서드를 호출한 객체를 가리킨다
    console.log(this); // → {name: 'Jace', getName: ƒ}
    return this.name;
  }
}
console.log(user.getName()); // → Jace

function Square(sideLength) {
  // 생성자 함수 내부에서 this를 참조하면 생성자 함수 자신이 생성할 인스턴스를 가리킨다
  this.sideLength = sideLength;
  console.log(this); // → Square {sideLength: 5}
}
const square = new Square(5);
```

예제에 대한 결과를 정리하자면 아래와 같다:

- 전역에서 `this`를 참조하면 전역 객체 `window`를 가리키고 참조한다
- 일반 함수(함수 선언문, 함수 표현식)내부에서 `this`를 참조하면 전역 객체 `window`를 가리키고 참조한다
- 객체의 메서드 내부에서 `this`를 참조하면 메서드를 호출한 객체를 가리키고 참조한다
- 생성자 함수 내부에서 `this`를 참조하면 생성자 함수 자신이 생성할 인스턴스를 가리키고 참조한다

그러나 `this`는 객체의 프로퍼티 혹은 메서드를 참조하기 위한 자기 참조 변수이므로 일반적으로 객체의 메서드 내부 또는 생성자 함수 내부에서만 사용하는 것이 의미가 있다 

<br>

## 참고

- [모던 자바스크립트 Deep Dive]()
- [[JavaScript] this란 무엇일까](https://hanamon.kr/javascript-this%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C/)

