# this

### 목차

- [1 this란](#1-this란)

<br>

## 1 this란

앞서 [객체의 구성 요소](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#3-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C) 파트에서 객체는 객체의 상태를 나타내는 정적 데이터 프로퍼티와 동작을 나타내는 동적 데이터 메서드를 하나의 단위로 묶은 자료구조라 했다

객체의 동작을 나타내는 메서드는 자신이 속한 객체의 상태(프로퍼티)값을 참조하고 변경할 수 있어야 한다. 만약 객체의 상태값이 변경되면 객체의 동작에 대한 결과는 바뀔 것이기 때문이다

```javascript
const square = {
  // 만약 sideLength가 10으로 변경된다면? 
  sideLength: 5, 
  getArea() {
    return Math.pow(square.sideLength, 2);
  }
};

console.log(square.getArea()); // → 25
```

다시 말해 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 메서드 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다. 만약 바로 위 예제에서처럼 객체 리터럴 방식으로 생성된 객체의 경우 메서드 자신이 속한 객체의 식별자(e.g. `square`)를 활용해서 객체의 상태값(프로퍼티)을 참조할 수 있게 된다

1. 런타임 이전 - 객체 리터럴의 변수 선언문 호이스팅
2. 런타임 이후 - 객체 리터럴의 평가(객체의 생성) 및 `square` 변수에 할당 
3.  `console.log(square.getArea());` 참조문 및 메서드 호출
4. `square` 변수에 값의 할당 완료, `sideLength` 프로퍼티 생성 완료. 따라서 `square.sideLength`로 식별자 참조 가능

객체 리터럴 방식으로 생성한 객체는 메서드가 참조할 수 있는 식별자가 있다 치자. 그렇다면 생성자 함수 방식으로 생성된 객체(인스턴스)의 경우는 메서드가 참조할 수 있는 식별자는 무엇일까?

```javascript
function Square(sideLength) {
  ????.sideLength = sideLength;
}

Square.prototype.getArea = function () {
  return Math.pow(????.sideLength, 2);
}

const square = new Square(5);
```

1. 런타임 이전 - 생성자 함수 정의, 생성자 함수 프로토타입 생성
2. 런타임 이후 - `Square.prototype` 객체의 `getArea` 프로퍼티에 함수 표현식을 할당
3. 런타임 이후 - 생성자 함수 호출(`new` 연산자), 인스턴스 생성

Square.prototype.getArea = function ~~ 는 prototype 객체의 프로퍼티(메서드)에 값을 할당하는 것이기 때문에 undefined로 나올 수 밖에 없음 

그렇다면 생성자 함수가 정의되는 시점과 프로토타입의 메서드가 생성되어 할당되는 시점 모두 생성자 함수 호출과 인스턴스 생성 시점보다 앞서 있다. 이런 경우 생성자 함수 내부의 `????.sideLength = sideLength;` 코드 문을 실행할 때 할당할 대상(식별자)이 없는 것이다



