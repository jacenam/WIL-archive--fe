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

<img width="100%" src="https://github.com/jacenam/WIL-archive/assets/92138751/92cefd62-6bdd-4c71-b353-d17401b81aae">



***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)