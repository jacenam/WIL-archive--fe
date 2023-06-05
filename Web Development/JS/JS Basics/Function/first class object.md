# 함수와 일급 객체

### 목차

- [1 자바스크립트 함수와 일급 객체](#1-자바스크립트-함수와-일급-객체)
- [2 함수 객체](#2-함수-객체)

<br>

## 1 자바스크립트 함수와 일급 객체

앞서 함수는 객체이면서 일급 객체라 했다. 그렇다면 일반 객체와 일급 객체는 무엇이 다를까? 일급 객체는 아래 조건들을 모두 만족하는 객체여야 한다:

- 무명의 리터럴로 생성할 수 있어야 한다

  ```javascript
  const sayHello = function(name) {
    return `Hello, ${name}`
  }
  ```

- 변수나 자료구조(객체, 배열 등)에 저장할 수 있어야 한다

  ```javascript
  const msg = { sayHello }
  ```

- 함수의 매개변수에 전달할 수 있어야 한다

  ```javascript
  function printMsg(msg) {
    console.log()
  }
  ```

  

  