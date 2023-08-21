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

console.log(square instanceof Square); // → true
console.log(square instanceof Object); // → true
```

