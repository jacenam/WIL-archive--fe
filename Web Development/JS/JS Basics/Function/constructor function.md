# 생성자 함수(feat. 객체 생성 방식)

생성자 함수는 함수의 종류에 포함되면서도 객체 생성에도 중요한 개념이다. 객체 리터럴 이외에도 객체를 생성할 수 있는 여러가지 방법들이 존재하는데 대부분 함수와 관련이 깊다. 따라서 생성자 함수에 대한 개념을 먼저 학습하고 다른 함수와 관련된 객체 생성 방식들을 살펴볼 예정이다

### 목차

- [1 객체 리터럴 외의 객체 생성 방식](#1-객체-리터럴-외의-객체-생성-방식) 
- [2 생성자 함수란](#2-생성자-함수란)
  - [2-1 생성자 함수의 종류](#2-1-생성자-함수의-종류)
- [3 객체 리터럴에 의한 객체 생성 방식의 장단점](#3-객체-리터럴에-의한-객체-생성-방식의-장단점)
- [4 생성자 함수에 의한 객체 생성 방식의 장점](#4-생성자-함수에-의한-객체-생성-방식의-장점)
- [5 생성자 함수의 인스턴스 생성 과정](#5-생성자-함수의-인스턴스-생성-과정) 
  - [5-1 인스턴스 생성과 this 바인딩](#5-1-인스턴스-생성과-this-바인딩)
  - [5-2 인스턴스 초기화](#5-2-인스턴스-초기화)
  - [5-3 인스턴스 반환](#5-3-인스턴스-반환)
- [6 함수의 내부 메서드 Call과 Construct](#6-함수의-내부-메서드-Call과-Construct)
  - [6-1 Call과 Construct](#6-1-Call과-Construct)
  - [6-2 Constructor와 Non-Constructor](#6-2-Constructor와-Non-Constructor)
***

<br>

## 1 객체 리터럴 외의 객체 생성 방식

 JS는 [프로토타입]() 기반의 [객체지향 언어]()인만큼 객체 리터럴 이외에도 객체를 생성할 수 있는 여러가지 방법을 지원한다. 그 방법들은 아래와 같다(이번 파트는 생성자 함수만을 살펴볼 예정이다): 
- 생성자 함수 
- `Object.creat` 메서드 
- 클래스 

<br>


## 2 생성자 함수란
생성자 함수(Constructor Function)란 `new` 연산자와 함께 호출하여 객체를 생성하는 빌트인 함수다. JS 자체적으로 내장된 함수이기 때문에 호출만 해서 사용하면 되는 형태다

> 빌트인 함수(Built-in Function)란 프로그래밍 언어 설계 과정에서 미리 만들어놓고 개발자가 사용할 수 있도록 제공되는 자체 내장 함수를 의미한다. 이에 대해서는 [빌트인 객체]()를 참고하자

생성자 함수는 일반 함수와 정의하는 방식이 동일하다. 또한 `new` 연산자 뒤에 생성자 함수 또는 일반 함수를 호출했을 때 객체를 생성하게 되는 매커니즘은 동일하기에, 생성자 함수와 일반 함수의 구별을 위해 생성자 함수는 일반적으로 대문자 알파벳으로 함수 이름을 짓는다

```javascript
function Greetings(name) {
  this.name = name;
  this.sayHello = function() {
    console.log(`Hello, ${this.name}`);
  };
}

const welcomeMsg = new Greetings("Jace");
welcomeMsg.sayHello();
```

생성자 함수의 가장 기본적인 사용 방식은 '객체(Object)를 생성한다'는 의미로 빈 객체를 생성해주는 빌트인 생성자 함수를 `Object()`의 형식으로 호출하는 것이다

```javascript
// 빌트인 생성자 함수를 new 연산자 뒤에 호출한다(빈 객체가 생성된다)
const user = new Object();
console.log(user); // → {}

// 프로퍼티를 추가해서 필요한 객체 형태로 채워나가면 된다
user.name = "Jace";
user.sayHello = function() {
  console.log(`Hello, ${this.name}`); // → Hello, Jace
}

user.sayHello(); 
```

`Object()` 호출문에 매개변수를 지정해주면 지정한 프로퍼티가 자동으로 추가된 객체가 생성된다. 이렇게 생성자 함수를 통해 생성된 객체를 인스턴스(Instance)라고 부른다. 인스턴스라는 용어는 추후 자주 쓰이니 익숙해지자

```javascript
const user = new Object({
  name: "Jace",
  sayHello: function() {
    console.log(`Hello, ${this.name}`);
  },
});

user.sayHello();
```

### 2-1 생성자 함수의 종류

JS에서는 `Object` 생성자 함수 이외에도 `String`, `Number`, `Boolean`, `Function`, `Array`, `Date`, `RegExp`, `Promise` 등의 빌트인 생성자 함수를 제공한다. 몇 가지 생성자 함수의 예제를 살펴보자(추후 각각의 빌트인 생성자 함수와 그들의 필요성에 대해 자세히 살펴볼 것이다. 현재 시점에서는 간단하게 어떤 빌트인 생성자 함수가 존재하는지만 알아두자)

> 원시타입인 문자열, 숫자, 불리언 타입이 어째서 생성자 함수를 통해 객체로 생성되는지 추후 각각의 [빌트인 생성자 함수]()를 참고하자

- `String` 생성자 함수

  ```javascript
  const strObj = new String("Jace");
  console.log(typeof strObj); // → object
  console.log(strObj); // → [String: "Jace"]
  ```

- `Number` 생성자 함수

  ```javascript
  const numObj = new Number(123);
  console.log(typeof numObj); // → object
  console.log(numObj); // → [Number: 123]
  ```

- `Array` 생성자 함수

  ```javascript
  const arr = new Array(1, 2, 3);
  console.log(typeof arr); // → object
  console.log(arr); // → [1, 2, 3]
  
  const emptyArr = new Array(10);
  console.log(typeof emptyArr); // → object
  console.log(emptyArr); // → [<10 empty items>]
  ```

`Object` 생성자 함수를 비롯해 `String`, `Number`와 같은 생성자 함수를 사용해 굳이 객체를 생성할 필요는 없다. 오히려 객체 리터럴을 통해 문자열, 숫자 타입의 프로퍼티 값을 갖는 객체를 생성하는 것이 더 간편하다. 그러나 객체 리터럴에 의한 객체 생성 방식은 몇 가지 문제가 존재한다. 따라서 상황과 필요에 따라 생성자 함수를 사용하는 것이 바람직하다

<br>

## 3 객체 리터럴에 의한 객체 생성 방식의 장단점

객체 리터럴을 활용한 객체 생성 방식은 직관적이기에 간결하고 간편하다는 장점이 있다

```javascript
// 정사격형의 한 변의 길이와 넓이를 구하는 함수를 객체 값으로 갖는 객체
const square = {
  sideLength: 5,
  getArea() {
    console.log(Math.pow(this.sideLength, 2)); // → 25
  },
};

square.getArea();
```

그러나 객체 리터럴은 아래와 같이 단 하나의 객체만 생성이 가능하다. 만약 정사격형 두 개를 객체 리터럴로 생성하고자 하면 아래와 같이 한 변의 길이와 넓이를 구하는 함수를 값으로 갖는 객체를 두 번 반복해야 한다

> `Math.pow()`는 [지수 연산](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Operator/exponentiation%20operator.md) 시 사용하는 메서드다. 첫 번째 매개변수에는 밑을, 두 번째 매개변수에는 지수를 입력하여 지수 연산을 실행한다. 정사격형의 넓이는 한 변의 길이의 제곱이기 때문에 아래 예제와 같이 매개변수들을 입력했다

```javascript
const square1 = {
  sideLength: 5,
  getArea() {
    console.log(Math.pow(this.sideLength, 2)); // → 25
  },
};

const square2 = {
  sideLength: 10,
  getArea() {
    console.log(Math.pow(this.sideLength, 2)); // → 100
  },
};

square1.getArea();
square2.getArea();
```

앞서 [객체 타입](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#4-%EB%A9%94%EC%84%9C%EB%93%9C)에서 객체의 고유 상태를 표현하는 정적 데이터는 일반 프로퍼티이며, 메서드는 이러한 정적 데이터(`sideLength`) 조작하고 동적으로 표현해주는 동적 데이터라고 했다. 위 예제에서처럼, 일반적으로 객체마다 정적 데이터(`sideLength`)인 프로퍼티의 값은 다를 수 있지만 메서드(`getArea`)의 내용이 동일한 경우가 많다

객체 리터럴을 통해 객체를 생성하는 경우 위 예제처럼 객체의 구조가 동일하더라도 매번 같은 프로퍼티와 메서드를 기술해야하며, 이러한 객체 형태의 객체를 수 십개 생성해야 한다면 매우 번거로운 작업이 될 것이다

<br>

## 4 생성자 함수에 의한 객체 생성 방식의 장점

생성자 함수는 다수의 유사 구조를 가진 객체 생성 시 객체 리터럴보다 간편한 방식으로 객체를 생성할 수 있다

앞서 생성자 함수에 의해 생성된 객체를 인스턴스라고 부른다 했다. 생성자 함수를 통해 인스턴스를 생성하는 방식은 마치 인스턴스를 생성하기 위해 [클래스]()를 사용한 것과 같이 간편하게 동일 구조의 객체 여러 개를 생성할 수 있다

> `this`는 객체 자신의 프로퍼티 혹은 메서드를 참조하기 위한 자기 참조 변수(Self-referencing Variable)다. `this`에 대해서는 [`this` 바인딩]() 파트를 참고하자

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
  this.getArea = function() {
    return Math.pow(this.sideLength, 2); 
  };
}

const square1 = new Square(5);
const square2 = new Square(10);

console.log(square1.getArea()); // → 25
console.log(square2.getArea()); // → 100
```

즉, 일반 함수와 동일한 방법으로 생성자 함수를 정의하고 `new` 연산자와 함께 함수를 호출하면 해당 함수는 생성자 함수로서 동작한다. 바로 위 예제를 생성자 함수 동작 과정에 따라 단계별로 나누면 아래와 같다

```javascript
// (1) 생성자 함수를 정의한다
function Square(sideLength) {
  this.sideLength = sideLength;
  this.getArea = function() {
    return Math.pow(this.sideLength, 2); 
  };
}

// (2) 생성자 함수로 동작하기 위해 new 연산자와 함께 정의한 생성자 함수를 호출한다
const square = new Square(5); 
console.log(square) // → Square { sideLength: 5, getArea: [Function] }
```

<br>

## 5 생성자 함수의 인스턴스 생성 과정

앞서 생성자 함수는 유사 프로퍼티 구조를 가진 다수의 인스턴스(객체)를 생성하기 위해 일종의 재사용 가능한 템플릿(클래스)으로서의 역할을 수행한다 했다. 생성자 함수에 의한 인스턴스는 아래와 같은 과정을 통해 생성된다:

1. 인스턴스 생성과 `this` 바인딩
2. 인스턴스 초기화
3. 인스턴스 반환

앞서 언급했던 생성자 함수의 예제를 살펴보면 실제로 입력한 코드와 다르게, `new` 연산자를 생성자 함수 호출 앞에 붙여 실행하는 순간 JS 엔진은 생성자 함수 안에 빈 객체를 생성하며 없었던 최종 반환문도 생성하여 코드가 동작하게 된다

<img width="100%" src="https://github.com/jacenam/WIL-archive/assets/92138751/61c7d0b1-0aea-4fea-99f8-a7f935d109fc">

 이에 대해서 인스턴스의 단계별 생성 과정을 살펴보자

### 5-1 인스턴스 생성과 this 바인딩

생성자 함수에 `new` 연산자를 붙여 호출하면 먼저 암묵적으로 빈 인스턴스(객체)가 생성된다. 이때 `this = {};` 코드는 실제로 개발자가 기술한 코드가 아니라 JS 엔진에 의해 눈에 보이진 않지만 암묵적으로 실행되는 코드인 것이다

<img width="100%" src="https://github.com/jacenam/WIL-archive/assets/92138751/92cefd62-6bdd-4c71-b353-d17401b81aae">

이렇게 생성된 인스턴스는 `this`에 바인딩 된다. 아래와 같이 생성자 함수를 실행(호출)했을 때 참조문의 `this` 식별자는 생성자 함수가 생성할 인스턴스를 가리킨다

> 바인딩(Binding) 또는 식별자 바인딩이란 식별자와 값을 연결하는 과정을 의미한다. 이는 마치 변수를 선언할 시 변수 이름과 변수 값을 저장할 메모리 공간의 주소를 연결(바인딩)하는 것과 같은 개념이다. `this` 바인딩이란 `this`가 가리킬 객체와 바인딩하는 것이다. `this`는 상황에 따라 가리키는 값(객체)이 다른데, 이에 대해서는 [`this` 바인딩]()을 참고하자

```javascript
function Square(sideLength) {
  // (1) 빈 인스턴스를 암묵적으로 생성하며, this와 바인딩된다
  this = {};
  console.log(this); // (this는 Square 인스턴스를 가리킨다) → Square {} 

  this.sideLength = sideLength; 
  this.getArea = function () {
    return Math.pow(this.sideLength, 2);
  };
}

const square = new Square(5);
```

### 5-2 인스턴스 초기화

앞서 생성자 함수에 `new` 연산자를 호출하면 JS 엔진은 암묵적으로 빈 인스턴스(객체)를 생성하여 `this` 식별자에 바인딩한다고 했다. 이때 암묵적으로 생성된 빈 인스턴스는 프로퍼티가 추가되지 않은 '빈' 상태이므로, 개발자의 코드 기술에 따라 생성자 함수가 인수(Parameter)로 전달받은 값을 빈 인스턴스의 프로퍼티에 할당하는 단계를 '인스턴스 초기화'라고 한다

```javascript
function Square(sideLength) {
  this = {};
  console.log(this); // → Square {}
	
  // (2) Square 인스턴스에 아래와 같은 프로퍼티를 추가하게 된다. 빈 인스턴스 프로퍼티에 값을 할당하는 것을 인스턴스 초기화라고 부른다
  this.sideLength = sideLength; 
  this.getArea = function () {
    return Math.pow(this.sideLength, 2);
  };
}

const square = new Square(5);
```

### 5-3 인스턴스 반환

생성자 함수 몸체 내부에서 개발자가 기술한 코드에 따라 인스턴스 생성의 모두 완료되면 인스턴스가 바인딩된 `this`의 값을 암묵적으로 함수 몸체 밖으로 반환한다. 이때 `return this` 코드 떠또한 실제로 개발자가 기술한 코드가 아니라 JS 엔진에 의해 눈에 보이진 않지만 암묵적으로 실행되는 코드인 것이다 

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/68d8d279-bd3e-46a5-9963-3f753aa41a62" width="100%">

```javascript
function Square(sideLength) {
  this = {};
  console.log(this); // → Square {}
	
  this.sideLength = sideLength; 
  this.getArea = function () {
    return Math.pow(this.sideLength, 2);
  };
  // (3) 프로퍼티가 추가된 인스턴스가 암묵적으로 함수 몸체 밖으로 반환된다
  return this;
}

// Square 생성자 함수가 호출되면 위 과정들이 모두 실행되어 최종 인스턴스를 가진 this가 반환된다
const square = new Square(5);
console.log(square); // Square {sideLength: 5, getArea: f}
```

여기서 주의해야 할 것은 JS 엔진에 의해 반환문은 암묵적으로 실행되기 때문에, 생성자 함수 몸체 내부에 명시적으로 기술한 `return` 반환문을 반드시 생략해야 한다. 그렇지 않을 시 원치 않는 오류가 발생할 수도 있다. 예를 들어: 

- 명시적으로 다른 객체 타입의 값을 반환하는 반환문을 기술한 경우

  ```javascript
  function Square(sideLength) {
    this.sideLength = sideLength; 
    this.getArea = function () {
      return Math.pow(this.sideLength, 2);
    };
    return {radius: 10, getDiameter: function() {} };
  }
  
  const square = new Square(5);
  // 의도치 않은 결과를 초래한다
  console.log(square); // Square {radius: 10, getDiameter: f}
  ```

- 명시적으로 원시 값을 반환하는 반환문을 기술한 경우

  ```javascript
  function Square(sideLength) {
    this.sideLength = sideLength; 
    this.getArea = function () {
      return Math.pow(this.sideLength, 2);
    };
    return 25;
  }
  
  const square = new Square(5);
  // 원시 값의 반환이 아닌 this가 가리키는 값이 반환된다
  console.log(square); // Square {sideLength: 5, getArea: f}
  ```

<br>

## 6 함수의 내부 메서드 Call과 Construct

함수 선언문, 함수 표현식과 같이 일반적인 방법으로 정의하는 함수와 생성자 함수를 통해 생성되는 함수는 모두 호출이 가능하다. 함수도 객체이지만, 일반 객체는 함수처럼 호출할 수 없다. 따라서 함수의 [내부 슬롯과 내부 메서드](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md)를 살펴보면 일반 객체가 지니는 내부 슬롯과 내부 메서드를 모두 가지면서도, 함수로서 동작하기 위해 함수 객체만 지니는 내부 슬롯과 내부 메서드를 가진다

> 아래 예제에서 [프로퍼티 디스크립터](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#2-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%96%B4%ED%8A%B8%EB%A6%AC%EB%B7%B0%ED%8A%B8%EC%99%80-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EB%94%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%84%B0-%EA%B0%9D%EC%B2%B4) 객체에 간접적으로 접근하는 메서드를 통해 내부 슬롯과 내부 메서드인 [프로퍼티 어트리뷰트](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/property%20attribute.md#2-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%96%B4%ED%8A%B8%EB%A6%AC%EB%B7%B0%ED%8A%B8%EC%99%80-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EB%94%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%84%B0-%EA%B0%9D%EC%B2%B4) 정보를 살펴볼 수 있다

```javascript
function foo() {}; 

// 일반 객체처럼 동적 프로퍼티 추가
foo.property1 = "Jace";
foo.property2 = 31;

// 함수도 객체이므로 메서드를 소유할 수 있다
foo.method1 = function() {
  console.log(this.property1);
}

foo.method2 = function() {
  console.log(this.property2);
}

foo.method1(); // → Jace
foo.method2(); // → 31

console.log(Object.getOwnPropertyDescriptors(foo)); 
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/8d07cca9-3647-4cdb-b38b-18b8db4bdb7d" width="100%">

위 예제에서의 프로퍼티 디스크립터 객체의 정보를 살펴보면 함수 `foo`는 일반 객체와 동일한 프로퍼티 디스크립터(내부 슬롯과 내부 메서드) `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`를 가진다는 것을 확인할 수 있다. 여기에 함수는 함수 객체로서 일반 객체가 갖지 않는  `[[Environment]]`, `[[FormalParameters]]` 등의 내부 슬롯과 `[[Call]]`, `[[Construct]]` 같은 내부 메서드를 추가로 가지고 있다

> 프로퍼티 어트리뷰트 파트에서 살펴봤듯이, 일반 객체와 함수 객체의 모든 내부 슬롯과 내부 메서드를 상세히 알아야 될 필요는 없다. 일반 객체와 함수 객체의 어떠한 내부 슬롯과 내부 메서드가 차이가 있는지만 인지해도 된다

### 6-1 Call과 Construct 

여기서 함수 객체의 내부 메서드인 `[[Call]]`과 `[[Construct]]`를 살펴보자

- `[[Call]]`: 함수가 일반 함수로서 호출되면 함수 객체의 내부 메서드인 `[[Call]]`이 호출된다

  ```javascript
  function foo() {}
  
  // JS 자체에서 함수 내부적으로 [[Call]] 내부 메서드가 호출된다
  foo();
  ```

- `[[Construct]]`: `new` 연산자와 함께 생성자 함수가 사용(호출)되면 내부 메서드인 `[[Construct]]`가 호출된다

  ```javascript
  function foo() {}
  
  // JS 자체에서 생성자 함수 내부적으로 [[Construct]] 내부 메서드가 호출된다
  new foo();
  ```

내부 메서드 `[[Call]]`을 갖는 함수 객체를 Callable이라 한다. 바로 위에서는 `[[Call]]`을 설명할 때 "함수 객체의 내부 메서드인 `[[Call]]`" 이라 표현했다. 이는 사실 함수 객체는 언제나 내부 메서드 `[[Call]]`을 갖기 때문이다. 호출할 수 없는 객체는 함수 객체가 아니므로, 함수라는 객체는 반드시 `[[Call]]` 내부 메서드를 가져야 하는 Callable 객체여야 한다

내부 메서드 `[[Construct]]`를 갖는 함수 객체를 Constructor, 내부 메서드 `[[Construct]]`를 갖지 않는 함수 객체는 Non-Constructor라고 부른다. 다시 말해, `new` 연산자와 함께 생성자 함수로서 사용(호출)할 수 있는 함수는 Constructor, 반대로 생성자 함수로 호출하지 못하는  함수는 Non-Constructor라고 부르는 것이다

### 6-2 Constructor와 Non-Constructor

호출할 수 없는 함수는 존재하지 않는다. 즉, 함수 객체는 반드시 Callable이어야 한다. 반면, 모든 함수 객체가 내부 메서드 `[[Construct]]`를 갖는 것은 아니다. 결론적으로 함수 객체는 아래와 같이 구분된다:

- Callable이면서 Constructor인 함수
- Callable이면서 Non-Constructor인 함수

함수를 생성할 때 JS 엔진은 함수의 정의 방식을 평가하여 함수를 Constructor와 Non-Constructor로 구분한다. JS 엔진은 아래의 함수 정의 방식에 따라 함수를 구분한다: 

- Constructor: [함수 선언문](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#2-%ED%95%A8%EC%88%98-%EC%84%A0%EC%96%B8%EB%AC%B8), [함수 표현식](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#3-%ED%95%A8%EC%88%98-%ED%91%9C%ED%98%84%EC%8B%9D), [클래스]()
- Non-Constructor: [메서드 축약 표현](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20literal.md), [화살표 함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/defining%20functions.md#6-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98)

아래 예제에서 일반 함수 정의 방식인 함수 선언문, 함수 표현식은 모두 JS 엔진에 의해 Constructor로 구분된다

```javascript
// 함수 선언문
function foo() {
  console.log("constructor");
}

// 함수 표현식
const bar = function() {
  console.log("constructor");
}

new foo(); // → constructor
new bar(); // → constructor
```

그렇다면 객체의 프로퍼티 값으로 할당된 함수의 경우는 어떠할까? 앞서 [객체 타입](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#4-%EB%A9%94%EC%84%9C%EB%93%9C) 파트에서 함수를 객체의 프로퍼티 값으로 사용하면 이를 일반 함수와 구분하기 위해 메서드라고 부른다 했다

```javascript
const obj = { 
	baz: function() {
  	console.log("constructor? non-constructor?");
  }
};

new obj.baz(); // → constructor? non-constructor?
```

그러나 ECMAScript에 따르면 ES6의 '메서드 축약 표현'만이 메서드로 인정된다

```javascript
// ES6 메서드 축약 표현
const obj = {
  shorten() {
    console.log("메서드 축약 표현으로 정의된 함수")
  }
}
```

즉, 메서드 축약 표현을 쓰지 않은 예제에서 함수는 프로퍼티 키(`baz`)라는 식별자에 프로퍼티 값으로 함수를  할당하는 함수 표현식과도 같이 취급되는 것이다

```javascript
// 함수 표현식과도 같은 메서드 정의 방식
baz: function() {
	console.log("constructor? non-constructor?");
}

=> const baz = function () {
  console.log("constructor? non-constructor?");
}
```

종합적으로, 함수 표현식과 같이 일반 함수 정의 방식으로 정의된 객체의 메서드와 메서드 축약 표현으로 정의된 객체의 메서드를 비교해보자. 아래 예제를 살펴보면 ES6 메서드 축약 표현은 Non-Construct 특성을 가진 함수로 `new` 연산자와 함께 생성자 함수로 호출하여 사용할 수 없음을 확인할 수 있다

```javascript
const obj = {
  foo: function () {
    console.log("constructor? non-constructor?");
  },
	bar() {
    console.log("constructor? non-constructor?");
  }
};

// 일반 함수(메서드)
console.log(obj.foo()); // → constructor? non-constructor?
// ES6 메서드 축약표현
console.log(obj.bar()); // → constructor? non-constructor?

// 일반 함수(메서드)
new obj.foo(); // → constructor? non-constructor?
// ES6 메서드 축약 표현
new obj.bar(); // → Uncaught TypeError: obj.bar is not a constructor
```

정리하자면, 모든 함수는 Callable이다. 그러나 일반 함수 정의 방식으로 생성한 함수는 `new` 연산자와 함께 생성자 함수로 호출 가능한 Construct 함수인 반면, 메서드 축약 표현와 화살표 함수와 같이 축약 형태로 생성한 함수는 생성자 함수로 호출이 불가능한 Non-Construct 함수다

### 6-3 new 연산자

앞서 함수의 내부 메서드 `[[Call]]`과 `[[Construct]]`, 그리고 Construch 함수와 Non-Construct 함수를 살펴보았다

모든 함수는 내부 메서드 `[[Call]]`을 가지고 있기 때문에 호출이 가능하다. 함수를 호출하면 내부 메서드 `[[Call]]`을 호출하여 함수가 호출되는 것이다

```javascript 
function foo() {}

// 함수를 호출하면 내부 메서드 [[Call]]이 호출되어 함수가 동작하는 것이다
foo(); 
```

그러나 `new` 연산자와 함께 함수를 호출하면 해당 함수는 내부 메서드 `[[Call]]`을 호출하지 않고 내부 메서드 `[[Construct]]`을 호출하게 되어 생성자 함수로서 동작하게 된다

```javascript
function foo() {}

new foo(); 
```

앞서 [Constructor와 Non-Constructor](#6-2-Constructor와-Non-Constructor)에서 살펴보았듯이, 중요한 것은`new` 연산자와 함께 호출하는 함수는 Construct 함수여야 생성자 함수로서 동작할 수 있다

```javascript
function sum(x, y) {
	console.log(x + y);
}
new sum(1, 2); // → 3

const add = function(x, y) {
  console.log(x + y);
}
new add(1, 2); // → 3

const obj = {
  multiply(x, y) {
    console.log(x * y);
  }
}
new obj.multiply(1, 2); // → Uncaught TypeError: obj.multiply is not a constructor

const divide = (x, y) => {
  console.log(x / y); 
}
new divide(1, 2); // → Uncaught TypeError: divide is not a constructor
```

<br>

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [코딩앙마 - 자바스크립트 생성자 함수](https://youtu.be/8hrSkOihmBI)