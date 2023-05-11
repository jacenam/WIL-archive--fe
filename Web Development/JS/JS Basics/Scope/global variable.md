# 전역 변수의 문제점

### 목차

- 

***

<br>

JS에서의 호이스팅은 변수 선언이 각 스코프의 선두로 끌어 올려진 것처럼 동작한다

```javascript
function foo() {
  var x = "local"; 
  console.log(x); // ?
  return x; 
}

foo(); 
console.log(x); 
```
```javascript
var x = "global"; 

function foo() {
  console.log(x); // ?
  var x = "local"; 
  console.log(x); // ?
}

foo(); 
console.log(x); // ?
```

전역 코드는 함수 호출과 같이 실행되어야 하는 특별한 조건이 없고 코드가 로드되자마자 해석되서 실행된다. 

전역 객체는 무엇일까?

전역 변수를 선언했다는 뜻은 변수가 전역에서, 즉 코드 어디서든 참조하고 할당할 수 있는 변수를 사용하겠다는 것이다 

- 전역 변수
  - 긴 생명 주기 
  - 소멸하지 않는 만큼 메모리 리소스도 오랜 기간 소비
  - 전역 변수의 의도치 않은 상태 변경이 발생할 가능성이 높음
  - 스코프 체인 상에서 종점에 존재(JS 엔진이 변수를 검색할 때 전역 변수는 가장 마지막에 검색되므로 속도 차이가 발생)
  - 파일이 분리되어 있다해도 하나의 전역 스코프를 공유. 다른 파일 내에서 동일한 이름으로 지정된 전역 변수나 전역 함수가 같은 스코프 내에 존재할 경우 파일끼리 네임스페이스가 오염될 가능성이 큼

- 반면, 지역 변수는 
  - 짧은 생명 주기 
  - 메모리 리소스도 짧은 기간만 소비
  - 전역 변수보다 상태 변경에 의한 오류가 발생할 확률이 적음
  

따라서 반드시 전역 변수를 사용해야 하는 것이 아니라면 지역 변수를 사용해야 함

<br>

***
### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [모던 JavaScript 튜토리얼 - 전역 객체](https://ko.javascript.info/global-object) 
- [전역 객체 `window`](https://opentutorials.org/course/1375/6633)
- [Oject Model](https://opentutorials.org/course/1375/6622)