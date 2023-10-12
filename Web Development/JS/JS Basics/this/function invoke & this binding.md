# 함수 호출 방식과 this 바인딩 결정 방식

### 목차

- [1 함수 호출과 this 바인딩](#1-함수-호출과-this-바인딩)
- [2 this 바인딩 결정 방식](#2-this-바인딩-결정-방식)
  - [2-1 일반 함수 호출](#2-1-일반-함수-호출)
  - [2-2 메서드 호출](#2-2-메서드-호출)
  - [2-3 생성자 함수 호출](#2-3-생성자-함수-호출)
  - [2-4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출](#2-4-Function.prototype.apply/call/bind-메서드에-의한-간접-호출)
    - [2-4-1 apply와 call 메서드](#2-4-1-apply와-call-메서드)
    - [2-4-2 bind 메서드](#2-4-2-bind-메서드)

<br>

## 1 함수 호출과 this 바인딩

앞서 `this` 바인딩은 함수 호출 방식에 따라 동적으로 결정된다고 했다. 아래의 함수 호출 방식에 따라 `this` 바인딩 방식이 상이한다(위 `this` 참조와 유사하다): 

1. 일반 함수(함수 선언문, 함수 표현식) 호출:

   일반적인 함수 호출 시 `this`는 전역 객체 `window`에 바인딩된다. `this`가 전역 객체 `window`에 바인딩되는 이유는 평가되어 객체를 생성하지 않는 일반 함수에서 자기 참조 변수가 필요없기 때문이다

   ```javascript
   // 함수 선언문
   function foo() {
     console.log(this); // → Window
   foo(); 
   
   // 함수 표현식
   const bar = function() {
     console.log(this); // → Window
   };
   bar();
   ```

   다만 `strict mode` 상태에서 일반 함수 내부의 `this`에는 `undefined`가 바인딩된다

   ```javascript
   function foo() {
     "use strict";
     
     console.log(this); // → undefined
   }
   foo();
   ```

2. 메서드 호출:

   메서드 호출 시 `this`는 메서드를 호출한 객체에 바인딩된다

   ```javascript
   const obj = { 
     foo() {}, 
     bar() {
       console.log(this); // → {foo: ƒ, bar: ƒ}
     }
   };
   obj.bar(); 
   ```

3. 생성자 함수 호출

   `new` 연산자와 함께 생성자 함수 호출 시 `this`는 생성자 함수 자신이 생성한 인스턴스를 가리킨다

   ```javascript
   function Foo(number) {
     this.number = number;
     console.log(this); // → Foo {number: 5}
   }
   
   const foo = new Foo(5);
   ```

4. `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출

   > `Function.prototype.apply/call/bind`에 대해서는 [여기](#2-4-Function.prototype.apply/call/bind-메서드에-의한-간접-호출)를 참고하자

   ```javascript
   const foo = function() {
     console.log(this);
   };
   
   const bar = { name: "bar" };
   
   foo.call(bar); // → { name: "bar" };
   foo.apply(bar); // → { name: "bar" };
   foo.bind(bar); // → ƒ () { console.log(this); }
   ```

<br>

## 2 this 바인딩 결정 방식

앞서 함수 호출 방식에 따라 `this` 바인딩의 방식이 상이한다고 했다. 그렇다면 `this` 바인딩이 어떻게 결정되는지 살펴보자

### 2-1 일반 함수 호출

일반 함수(함수 선언문, 함수 표현식)를 일반적인 함수 호출 방식으로 호출할 경우 `this`는 전역 객체에 바인딩된다. 이는 [중첩함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/nested%20function.md), [콜백함수](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/callback%20function.md) 등에도 해당된다. 다시 말해, 어떠한 함수라도 일반 함수 호출 방식으로 함수를 호출하면 `this`는 전역 객체 `window`에 바인딩되는 것이다

```javascript
// 함수 표현식
const foo = function() {
  console.log(`foo: ${this}`); // → foo: [object window] 
}
foo();

// 함수 선언문으로 정의한 중첩 함수
function bar() {
  console.log(`foo: ${this}`); // → bar: [object window] 
  function baz() {
    console.log(`bar: ${this}`); // → baz: [object window]
  }
  baz();
}
bar();
```

여기서 주의해야할 것은 메서드 내에서 정의한 중첩 함수도 일반적인 함수 호출 방식으로 호출되면 중첩 함수 내부의 `this`는 전역 객체 `window`에 바인딩된다

```javascript
// var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다
// const 키워드로 전역 변수를 선언하면 전역 객체의 프로퍼티가 되지 않는다
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 메서드를 호출하면 this는 자신을 호출한 객체에 바인딩된다
    console.log(`foo: ${this}`); // → foo: {value: 100, foo: ƒ }
    console.log(`foo this.value: ${this.value}`); // → foo this.value: 100
    
    // 메서드 내부에서 정의한 중첩 함수
    function bar() {
      // 메서드 내부에서 정의한 중첩 함수도 일반적인 함수 호출 방식으로 호출되면 this는 전역 객체 window에 바인딩 된다
      console.log(`bar: ${this}`); // → bar: [object window]
      // 따라서 메서드 내부의 중첩함수의 this는 전역 객체의 프로퍼티인 var 키워드로 선언한 변수의 값을 참조한다
      console.log(`bar this.value: ${this.value}`); // → 1 
    }
    bar();
  },
};

obj.foo();
```

위에서 언급했듯이 콜백 함수도 일반적인 함수 호출 방식으로 호출된다면 콜백 함수 내부의 `this`에도 전역 객체 `window`가 바인딩된다

> `setTimeout` 메서드는 콜백 함수를 활용하는 대표적인 예다. 콜백 함수란 특정 함수의 매개변수를 통해 내부로 전달되는 함수로서 헬퍼 함수라고 부르기도 한다. `setTimeout` 메서드의 첫 번째 매개변수에는 실행시킬 콜백 함수, 두 번째 매개변수에는 콜백 함수 실행의 지연 시간을 밀리초 단위로 전달한다
>
> ```javascript
> // setTimeout 메서드 사용법
> setTimeout(콜백 함수, 지연시간); 
> 
> // setTimeout 메서드 예제
> // setTimeout 메서드의 매개변수에 전달된 콜백 함수를 2초 뒤에 실행한다
> setTimeout(function(){ console.log("콜백 함수") }, 2000); 
> 
> // 위 예제를 화살표 함수로 단순화
> setTimeout(() => console.log("콜백 함수"), 2000);
> ```

```javascript
const obj = {
  value: 100,
  foo() {
    // 메서드를 호출하면 this는 자신을 호출한 객체에 바인딩된다
    console.log(`foo: ${this}`); // → foo: {value: 100, foo: ƒ }
    console.log(`foo this.value: ${this.value}`); // → foo this.value: 100
    // 메서드 내부에서 정의한 콜백 함수
  	setTimeout(function() {
      // 메서드 내부에서 정의한 콜백 함수도 일반적인 함수 호출 방식으로 호출되면 this는 전역 객체 window에 바인딩 된다
      console.log(`callback: ${this}`); // → callback: [object window]
      // 따라서 메서드 내부의 중첩함수의 this는 전역 객체의 프로퍼티인 var 키워드로 선언한 변수의 값을 참조한다
      console.log(`callback this.value: ${this.value}`); // → callback this.value: undefined
    }, 2000);
  }
};

obj.foo();
```

앞선 예제에서의 중첩 함수와 콜백 함수는 모두 메서드 내에서 정의되거나 전달된다. 즉 예제의 메서드는 중첩 함수와 콜백 함수의 외부 함수다. 따라서 예제의 중첩 함수와 콜백 함수는 외부 함수인 메서드의 일부 로직을 대신하는 헬퍼 함수인 것이다. 여기서 외부 함수인 메서드와 헬퍼 함수인 중첩 함수 또는 콜백 함수의 `this`가 서로 다른 대상에 바인딩되는 것은 바람직하지 않다(메서드의 `this`는 자신을 호출한 객체, 헬퍼 함수의 `this`는 전역 객체 `window`에 바인딩된다)

> 외부 함수와 헬퍼 함수의 `this`가 서로 다른 대상에 바인딩되었을 때 함수상 동작에 어떠한 어려움이 생기는 것일까? 

이러한 문제를 해결하기 위해 외부 함수인 메서드 `this`와 헬퍼 함수인 중첩 함수 혹은 콜백 함수의 `this`의 바인딩을 일치시키는 방법은 다음과 같다: 

- `foo` 메서드 내부의  `this`(1)는 메서드 자신을 호출한 객체에 바인딩된다

  ```javascript
  console.log(this); // → { value: 100, foo: ƒ }
  ```

- `this`(2) 바인딩을 `that` 변수에 할당한다

  ```javascript
  console.log(that); // → { value: 100, foo: ƒ }
  ```

- `foo` 메서드 내부의 `callback` 콜백 함수는 일반적인 방식으로 호출되면 `callback` 함수 내부의 `this`(3)는 전역 객체 `window`에 바인딩된다. 따라서 `this.value`를 참조하면 `var` 키워드로 선언한 전역 객체의 프로퍼티 `value`를 참조한다

  ```javascript
  console.log(this.value); // → 1
  ```

- 그렇기에 `obj` 객체에 바인딩된 `this`(2)를 할당받은 `that`(4)을 기준으로 `value` 프로퍼티를 참조한다. 그러면 `obj` 객체의 `value` 프로퍼티를 참조하게 된다

  ```javascript
  console.log(that.value); // → 100
  ```

```javascript
// var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다
// const 키워드로 전역 변수를 선언하면 전역 객체의 프로퍼티가 되지 않는다
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 메서드를 호출하면 this(1)는 자신을 호출한 객체에 바인딩된다
    console.log(this); // → { value: 100, foo: ƒ }
		// this(2) 바인딩을 that 변수에 할당한다
    const that = this;
    console.log(that); // → { value: 100, foo: ƒ }
    
    setTimeout(function() {
      // 콜백 함수가 일반적인 방식으로 호출되면 this(3)는 전역 객체에 바인딩 된다
      console.log(this.value); // → 1
      // 따라서 obj 객체에 바인딩된 this(2)를 할당받은 that(4)을 참조한다
      console.log(that.value); // → 100
    }, 2000);
  }
};


obj.foo(); 
```

`this`를 명시적으로 대상을 지정해 바인딩할 수 있는 방법은 위 예제에서의 방법 외에도 `Function.prototype.apply/call/bind` 메서드와 화살표 함수가 존재한다. 이에 대해서는 [Function.prototype.apply/call/bind 메서드](#2-4-Function.prototype.apply/call/bind-메서드에-의한-간접-호출)에 의한 간접 호출, [화살표 함수에 의한 `this` 바인딩]() 파트에서 살펴보자

### 2-2 메서드 호출

앞서 메서드 내부의 `this`는 메서드를 호출한 객체에 바인딩된다 했다. 다시 말해 메서드(`getName`)를 호출할 때 마침표 연산자(`.`) 앞에 기술한 객체(`user`)에 바인딩 되는 것이다

```javascript
const user = {
    name: "Jace", 
    getName() {
        console.log(this); // → {name: 'Jace', getName: ƒ}
        console.log(this.name); // → Jace
    }
};

user.getName(); 
```

여기서 주의할 것은 메서드 내부의 `this`는 메서드를 소유한 객체가 아니라 메서드를 호출한 객체에 바인딩된다는 것이다. 아래 예제를 살펴보면 `user` 객체에 속하는 `getName` 메서드를 또 따른 객체인 `anotherUser` 객체의 메서드로 할당했다. 그리고 `anotherUser` 객체에서 `getName` 메서드를 호출했을 때 `this`는 `anotherUser` 객체에 바인딩되는 것을 확인할 수 있다. 앞서 언급한대로 메서드 내부의 `this`는 메서드를 본래 소유한 객체(`user`)에 바인딩되는 것이 아니라 메서드를 할당 받은 다른 객체(`anotherUser`)가 메서드를 호출했을 때 바인딩이 결정된다는 의미다

```javascript
const user = {
    name: "Jace", 
    getName() {
        console.log(this); // → {name: 'Jace', getName: ƒ}
        console.log(this.name); // → Jace
    }
};

user.getName(); 

// 또 다른 객체 생성
const anotherUser = {
    name: "Ju Hyung", 
};

// 마침표 접근 연산자를 통해 getName 메서드를 anotherUser 객체의 메서드로 할당
anotherUser.getName = user.getName;

// 다른 객체에서 getName 메서드를 호출했을 때 this는 해당 객체에 바인딩된다
anotherUser.getName(); 
/*
→ {name: 'Ju Hyung', getName: ƒ}
→ Ju Hyung 
*/
```

`this`가 메서드를 소유하고 있는 객체가 아닌 자신을 호출한 객체에 바인딩되는 것은 메서드의 동작 원리를 살펴보면 이해할 수 있다. 메서드는 객체의 프로퍼티다. 엄밀히 따지면 메서드는 특정 객체의 프로퍼티에 바인딩된 함수라고 볼 수 있다. 아래는 앞선 예제에 대한 설명으로, `user` 객체는 `name` 프로퍼티와 `getName` 프로퍼티(메서드)를 소유한다. 그러나 `getName` 프로퍼티의 값은 독립적인 함수 객체로서 존재한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/87a1912a-997e-4ceb-a8d8-d2ac70507e1b" width="100%">

따라서 `getName` 프로퍼티가 가리키는 함수 객체인 `getName` 메서드는 다른 객체의 프로퍼티에 할당하여 다른 객체의 메서드가 될 수도 있고 일반 변수에 할당하여 일반 함수로서 호출될 수도 있다

```javascript
// 위에서 살펴본 예제
const user = {
  name: "Jace",
  getName() {
    console.log(this);
    console.log(this.name);
  }
}

// 다른 객체에 getName 프로퍼티(메서드) 할당
const anotherUser = {
  name: "Ju Hyung",
}
anotherUser.getName = user.getName; 

// 일반 변수에 할당, 일반 함수로서 호출
const getUserName = user.getName;
getUserName();
/* window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티로서 기본값은 빈 문자열("")이다
→ Window
→ ""
*/
```

아래 그림을 보면 이해가 더 쉽다. `user` 객체의 `getName` 프로퍼티는 함수 객체를 가리키고 있고, `anotherUser`의 `getName` 프로퍼티에는 `user` 객체의 `getName` 메서드를 할당했기 때문에 `anotherUser`의 `getName` 프로퍼티는 같은 함수 객체를 공유하게 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/647a1496-edd9-4ae4-9974-93fc482fe5cc" width="100%">

따라서 `getName` 프로퍼티가 가리키는 함수 객체는 `user` 객체에서 `getName` 메서드를 호출한 것인지, `anotherUser` 객체에서 `getName` 메서드를 호출한 것인지 메서드 호출 이전까지 알 수 없기 때문에 자기 참조 변수인 `this` 정보를 가진다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a66332d0-4b25-4c3c-8a9c-3e1fd1997174" width="100%">

그리고 `this`는 메서드가 호출된 시점에 바인딩이 결정된다

```javascript
user.getName(); // → Jace
```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/83ff06c8-eb39-4e1a-a764-1605f2ec1836" width="100%">

프로토타입 메서드 내부의 `this` 바인딩도 일반 객체의 메서드와 마찬가지로 메서드를 호출한 객체에 바인딩 된다. 아래 예제를 살펴보자

```javascript
function Square(sideLength) {
  this.sideLength = sideLength; 
}

Square.prototype.getArea = function() {
  console.log(Math.pow(this.sideLength, 2));
};

const square = new Square(5);

// getArea 메서드를 호출한 객체는 인스턴스다
square.getArea(); // → 25

// Square 생성자 함수에 sideLength 프로퍼티 추가
Square.prototype.sideLength = 10;

// getArea 메서드를 호출한 객체는 Square 생성자 함수다
Square.prototype.getArea(); // → 100
```

`Square` 생성자 함수에 의해 `square` 인스턴스를 생성했고, `square` 인스턴스가  `Square.prototype` 객체의 `getArea` 메서드를 호출했다. 이때 함수 객체의  `this`는 `square` 인스턴스에 바인딩된다. 따라서 `this.sideLength`는 `25`다

 그리고 `Square` 생성자 함수도 객체이기 때문에 직접 `getArea` 메서드를 호출할 수 있다. 이런 경우 함수 객체의 `this`는 `Square` 생성자 함수에 바인딩 된다. 따라서 `this.sideLength`는 `100`이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a192716b-df52-4957-b66e-a5b40ff2d678" width="100%">

### 2-3 생성자 함수 호출

앞서 [함수 호출 방식과 this 바인딩](#1-함수-호출-방식과-this-바인딩)에서 살펴봤듯이 생성자 함수 내부에 정의된 `this`는 생성자 함수가 미래에 생성할 인스턴스에 바인딩된다

```javascript
// 생성자 함수 정의
function Square(sideLength) {
  this.sideLength = sideLength;
  // 프로로타입이 아닌 생성자 함수에서 메서드 정의
  // 모든 인스턴스가 프로퍼티와 메서드를 갖게 된다
  this.getArea = function() {
    return Math.pow(this.sideLength, 2);
  };
}

// 따라서 Square.prototype 객체에는 메서드가 없다
console.log(Square.prototype); // → {constructor: ƒ}

const square1 = new Square(5);
const square2 = new Square(10);

console.log(square1, square2); 
/* 
→ Square {sideLength: 5, getArea: ƒ} 
→ Square {sideLength: 10, getArea: ƒ}
*/

console.log(square1.getArea()); // → 25
console.log(square2.getArea()); // → 100
```

만약 `new` 연산자와 함께 생성자 함수를 호출하지 않으면 일반 함수로 동작한다. 앞서 [함수 호출 방식과 this 바인딩](#1-함수-호출-방식과-this-바인딩)에서 살펴봤듯이 일반적인 방식으로 함수를 호출할 시 `this`는 전역 객체 `window`에 바인딩된다. 따라서 `getArea` 메서드는 전역 객체의 프로퍼티가 되기 때문에 `square.getArea()` 호출문은 에러가 발생한다 

```javascript
// 생성자 함수 정의
function Square(sideLength) {
  this.sideLength = sideLength;
  this.getArea = function() {
    return Math.pow(this.sideLength, 2);
  };
  // 일반 함수 호출 시 this는 전역 객체에 바인딩 된다
  console.log(this); // → Window
}

const square = Square(5); 

// getArea 메서드는 전역 객체의 프로퍼티가 된다
console.log(window.getArea()); // → 25
// 따라서 square 변수 식별자를 통해 getArea 메서드를 호출할 수 없다
console.log(square.getArea()); // → Uncaught TypeError: Cannot read properties of undefined (reading 'getArea')
```

### 2-4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출

`apply`, `call`, `bind` 메서드는 `Function` 생성자 함수에서 비롯된 `Function.prototype`의 빌트인 메서드다. 즉 모든 함수가 `Function.prototype` 객체로부터 상속받아 사용할 수 있다

### 2-4-1 apply와 call 메서드

먼저 `apply`와 `call` 메서드부터 살펴보자

> [`constructor` 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-2-constructor-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0feat-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98), [`__proto__` 접근자 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-4-proto-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0), [`getPrototypeOf` 메서드](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype.md#3-4-proto-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)에 대해서는 각 링크를 참고하자

```javascript
// 함수 정의문
function sum(a, b, c) {
  console.log(a + b + c);
}

// sum 함수는 Function.prototype 객체의 constructor 프로퍼티를 통해 생성자 함수와 연결된다
console.log(sum.constructor === Function); // → true

// sum 함수는 Object.prototype 객체의 __proto__ 접근자 프로퍼티를 통해 Function.prototype 객체에 접근할 수 있다 
console.log(sum.__proto__ === Function.prototype); // → true 

// getPrototypeOf 메서드는 __proto__ 접근자 프로퍼티를 대신해서 사용한다
console.log(Object.getPrototypeOf(sum) === Function.prototype); // → true

// Function.prototype 객체로부터 상속 받은 apply/call 메서드를 사용할 수 있다
console.log(Function.prototype.hasOwnProperty("apply")); // → true
console.lop(Function.prototype.hasOwnProperty("call")); // → true
sum.apply(null, [1, 2, 3]); // → 6
sum.call(null, 1, 2, 3); // → 6
```

함수는 호출되어야 실행할 수 있다. [함수 호출](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Function/function%20invoke.md)의 가장 기본적인 방식은 함수 호출 연산자(`()`)를 활용하는 것이다. 그러나 함수 호출 파트에서 언급하지 않은 함수 호출 방법이 더 존재한다. 바로 `Function.prototype`의 `apply/call` 메서드에 의한 함수 호출이다

```javascript
function sum(a, b, c) {
	console.log(a + b + c);
}

// 일반적인 함수 호출
sum(1, 2, 3); // → 6

// apply 메서드에 의한 함수 호출
sum.apply(null, [1, 2, 3]); // → 6

// call 메서드에 의한 함수 호출
sum.call(null, 1, 2, 3); // → 6
```

앞서 일반적인 함수 호출 시 `this`는 전역 객체 `window`에 바인딩된다고 했다. `apply`와 `call` 메서드의 첫 번째 매개변수에 `null` 값을 인수로 전달할 시 일반적인 함수 호출 방식과 동일한 결과를 반환하는 것을 확인할 수 있다

```javascript
function sum(a, b, c) {
  console.log(this); // → Window
	console.log(a + b + c); // → 6
}

sum(1, 2, 3);
sum.apply(null, [1, 2, 3]); 
sum.call(null, 1, 2, 3);
```

`apply`와 `call` 메서드의 사용법은 아래와 같다. 첫 번째 매개변수에 `null` 값이 아닌 객체(`thisArg`)를 전달할 시 `this`는 전역 객체 `window`가 아닌 인수로 전달된 객체(`thisArg`)에 바인딩된다. 다시 말해 `this`의 바인딩을 명시적으로 제어할 수 있는 것이다

```javascript
/**
apply 메서드
* thisArg - this가 대체될 객체
* argsArr - 함수에 전달할 인수(배열 또는 유사 배열 객체 형태)
**/

Function.prototype.apply(thisArg,[argsArr]); 
함수명.(thisArg,[argsArr]); 

/**
call 메서드
* thisArg - this가 대체될 객체
* arg1, arg2, ... - 함수에 전달될 인수
**/

Function.prototype.call(thisArg, arg1, arg2, ...);
함수명.call(thisArg, arg1, arg2, ...);
```

`apply`와 `call` 메서드 사용법에 따라 앞선 `sum` 함수 예제를 다시 살펴보자. `obj` 객체를 `apply`와 `call`의 첫 번째 매개변수의 인수로서 전달했고 `sum` 함수 내부의 `this`는 `obj`에 바인딩되었다

```javascript
function sum(a, b, c) {
  console.log(this, a + b + c);
}

console.log(sum(1, 2, 3)); // → Window 6

const obj = { label: "object" };

sum.apply(obj, [1, 2, 3]); // → { label: "object" } 6
sum.call(obj, 1, 2, 3); // → { label: "object" } 6
```

`apply`와 `call` 메서드를 활용한 예제를 살펴보자. 앞선 [메서드 호출](#2-2-메서드-호출) 파트처럼 `user` 객체의 `getName` 메서드를 다른 객체에 할당할 필요 없이, `anotherUser` 객체는 `apply` 혹은 `call` 메서드를 통해 `this`의 바인딩을 명시적으로 대체해  `user` 객체의 `getName` 메서드를 가져다 사용할 수 있게 된다. 원래 메서드 내부의 `this`는 메서드를 호출한 객체에 바인딩되지만, `apply`와 `call` 메서드는 `user` 객체에 바인딩되어야 할 ` this`를 인수로 전달된 `anotherUser` 객체에 바인딩한다

```javascript
const user = {
  name: "Jace",
  getName() {
    console.log(this, this.name);
  },
};

const anotherUser = {
  name: "Ju Hyung"
};

user.getName(); // → { name: "Jace", getName: ƒ } Jace
user.getName.apply(anotherUser); // → { name: "Ju Hyung"} Ju Hyung
user.getName.call(anotherUser); // → { name: "Ju Hyung"} Ju Hyung
```

아래 예제처럼 `apply`와 `call` 메서드를 활용하면 프로토타입 없이 일반 함수를 호출해도 `this` 바인딩을 특정 객체에 명시적으로 지정할 수 있게 된다

```javascript
function User(name) {
	this.name = name;
}

const user1 = new User("Jace");
const user2 = new User("Ju Hyung");

function defineUser(age, residence) {
  console.log(`${this.name}은 ${age}세이며 ${residence}에 거주합니다`);
}

// 일반 함수 호출의 this는 전역 객체 window에 바인딩된다
// window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티로서 기본값은 빈 문자열("")이다
defineUser(30, "no where");

// 특정 함수를 호출할 때 매개변수의 인수로 배열을 전달해야한다면 apply 메서드를 활용
defineUser.apply(user1, [30, "United States"]);
defineUser.apply(user2, [31, "South Korea"]);

// 특정 함수를 호출할 때 매개변수의 인수를 지정해서 전달해야한다면 call 메서드를 활용
defineUser.call(user1, 30, "United States");
defineUser.call(user2, 31, "South Korea");
```

### 2-4-2 bind 메서드

`bind` 메서드는 `apply` 및 `call` 메서드와 동일하게 `this` 바인딩을 명시적으로 지정할 수 있다. 다만, 함수를 호출하지는 않는다. 따라서 `bind` 메서드를 호출하고 결과 값을 반환하려면 변수에 담아 변수를 함수로서 호출하면 된다

```javascript
const user = {
  name: "Jace",
  getName() {
    console.log(this, this.name);
  },
};

const anotherUser = {
  name: "Ju Hyung"
};

user.getName.apply(anotherUser); // → { name: "Ju Hyung"} Ju Hyung
user.getName.call(anotherUser); // → { name: "Ju Hyung"} Ju Hyung
// 함수를 호출하지는 않기 때문에 함수가 실행되지 않는다
user.getName.bind(anotherUser);  

// bind 메서드를 호출하여 this가 바인딩된 객체와 호출된 메서드를 변수에 담아서 사용해야 한다
const bindMethod = user.getName.bind(anotherUser);
bindMethod(); // → {name: "Ju Hyung"} Ju Hyung
```

`bind` 메서드는 앞서 [일반 함수 호출](2-1 일반 함수 호출) 파트에서 언급한 메서드의  `this`와 중첩 함수 또는 콜백 함수의 `this` 불일치 문제를 해결하는데 사용된다. 먼저 중첩 함수의 예제를 살펴보자 

```javascript
// 일반 함수 호출 파트의 중첩 함수 예제
var value = 1;

const obj = {
  value: 100, 
  foo() {
    // 중첩 함수의 외부 함수인 메서드 내의 this는 메서드를 호출한 객체에 바인딩된다 
    console.log(this, this.value); // → {value: 100, foo: ƒ } 100
    
    // 메서드 내부의 중첩 함수는 헬퍼 함수지만, 메서드의 this와 중첩 함수의 this가 불일치한다
    function bar() {
      console.log(this, this.value); // → Window 1
    }
    bar();
  },
};

obj.foo();
```

```javascript
// bind 메서드로 외부 함수와 헬퍼 함수의 this 불일치 문제 해결
var value = 1;

const obj = {
  value: 100, 
  foo() {
    console.log(this, this.value); // → {value: 100, foo: ƒ } 100
    
    function bar() {
      console.log(this, this.value); // → {value: 100, foo: ƒ } 100
    }

    // bind 메서드의 매개변수로 전달되는 this(객체)는 foo 메서드의 this 바인딩과 동일한 obj 객체다
    const bindMethod = bar.bind(this);
    bindMethod(); 
  },
}

obj.foo();
```

콜백 함수의 예제도 살펴보자

```javascript
// 일반 힘수 호출 파트의 콜백 함수 예제
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 콜백 함수의 외부 함수인 메서드 내의 this는 메서드를 호출한 객체에 바인딩된다
    console.log(this, this.value); // → {value: 100, foo: ƒ } 100
  	
    // 메서드 내부의 콜백 함수는 헬퍼 함수지만, 메서드의 this와 콜백 함수의 this가 불일치한다
    setTimeout(function() {
      console.log(this, this.value); // → Window 1
    }, 2000);
  }
};

obj.foo();
```

```javascript
var value = 1;

const obj = {
  value: 100, 
  foo() {
    console.log(this, this.value); // → {value: 100, foo: ƒ } 100
    
    setTimeout(function() { 
      console.log(this, this.value); // → {value: 100, foo: ƒ } 100
    }.bind(this), 2000); 
  },
};

obj.foo();
```

위 `bind` 메서드 활용 예제는 아래처럼 조금 더 깔끔하게 변경할 수 있다

```javascript
var value = 1;

const obj = {
  value: 100, 
  foo(callback) {
    console.log(this, this.value); // → {value: 100, foo: ƒ } 100
    
    setTimeout(callback.bind(this), 2000); // → {value: 100, foo: ƒ } 100
  },
};

obj.foo(function() { console.log(this, this.value) });
```

<br>

***

### 참고

- [모던 자바스크립트 Deep Dive]()
- [함수의 메서드와 arguments](https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd)
- [함수의 기본 메서드 call(), apply(), bind()](https://mingoogle.tistory.com/11)