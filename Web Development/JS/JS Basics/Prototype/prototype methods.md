# 프로토타입 관련 메서드

### 목차 

- [1 instanceof 연산자](#1-instanceof-연산자)
- [2 직접 상속](#2-직접-상속)
  - [2-1 Object.create에 의한 직접 상속](#2-1-Object.create에-의한-직접-상속)
  - [2-2 proto 접근자 프로퍼티에 의한 직접 상속](#2-2-proto-접근자-프로퍼티에-의한-직접-상속)
- [3 프로퍼티 존재 확인](#3-프로퍼티-존재-확인)
  - [3-1 in 연산자](#3-1-in-연산자)
  - [3-2 Object.prototype.hasOwnProperty 메서드](#3-2-Object.prototype.hasOwnProperty-메서드)
- [4 프로퍼티 열거](#4-프로퍼티-열거)
  - [4-1 for...in 문](#4-1-for...in-문)
  - [4-2 Object.keys/values/entries 메서드](#4-1-Object.keys/values/entries-메서드)

<br>

## 1 instanceof 연산자

`instanceof` 연산자는 좌항에 객체(식별자 이름), 우항에 생성자 함수(식별자 이름)을 지정하여 우항에 놓인 생성자 함수의 `prototype` 객체가 좌항 객체의 프로토타입 체인 상 존재 여부에 따라 불리언 타입의 값을 반환한다

```javascript
function Square(sideLength) {
  this.sideLength = sideLength;
}

const square = new Square(5);

// square 인스턴스의 프로토타입 체인 상 Square 생성자 함수가 존재한다
console.log(square instanceof Square); // → true
// square 인스턴스의 프로토타입 체인 상 Object 생성자 함수가 존재한다
console.log(square instanceof Object); // → true
```

위 `instanceof` 예제를 아래 그림과 같이 표현할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/dabbc609-1823-4c2f-99f1-a9e19270e963" width="100%">

앞서 [프로토타입 교체](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Prototype/prototype%20chain.md#5-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%98-%EA%B5%90%EC%B2%B4) 파트에서 살펴본대로 `prototype` 객체를 다른 객체로 변경할 경우  프로토타입 체인의 구도도 변한다. 따라서 `instanceof` 연산자를 통해 피연산자들의 관계를 확인했을 때 결과 또한 바뀐다

```javascript
const 
```

`me` 객체의 `prototype` 객체가 `parent` 객체로 변경되었으므로 `Person` 생성자 함수, `Person.prototype` 객체, `me` 객체 간의 연결이 파괴된다. 그러나 `me` 객체는 `Person` 생성자 함수에 의해 생성된 인스턴스라는 사실은 변하지 않는다. 다만 `me instanceof Person`은 `false`로 평가된다. 이는 `Person.prototype`이 `me` 객체의 프로토타입 체인 상에 더 이상 존재하지 않기 때문이다





