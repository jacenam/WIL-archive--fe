# this

### 목차

- [1 this란](#1-this란)

<br>

## 1 this란

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





```javascript
function Square(sideLength) {
  ????.sideLength = sideLength;
}

Square.prototype.getArea = function () {
  return Math.pow(????.sideLength, 2);
}

const square = new Square(5);
```

그렇다면 생성자 함수가 정의되는 시점과 프로토타입의 메서드가 생성되어 할당되는 시점 모두 생성자 함수 호출과 인스턴스 생성 시점보다 앞서 있다. 이런 경우 생성자 함수 내부의 `????.sideLength = sideLength;` 코드 문을 실행할 때 할당할 대상(식별자)이 없는 것이다



```javascript
console.log(Square); 
console.log(Square.prototype.getArea);
console.log(square);

function Square(sideLength) {
  this.sideLength = sideLength;
}

Square.prototype.getArea = function () {
  return Math.pow(this.sideLength, 2);
}

const square = new Square(5);

console.log(Square.prototype.getArea);
console.log(square);
```



