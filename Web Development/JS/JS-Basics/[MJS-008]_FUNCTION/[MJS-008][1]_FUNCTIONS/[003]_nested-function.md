# 중첩 함수

### 목차

- [1 중첩 함수란](#1-중첩-함수란)

***

<br>

## 1 중첩 함수란

JS에서는 함수 내부에 또 다른 함수를 정의(선언)할 수 있다. 함수 내부에 정의된 함수를 중첩 함수(Nested Function) 또는 내부 함수(Inner Function)이라 부른다. 그리고 내부에 있는 함수를 감싸는 함수를 외부 함수(Outer Function)이라 부른다
```javascript
function outerFunc() { 
  // 외부(outer) 함수의 몸체
  function nestedFunc() {
   // 중첩(Nested, Inner) 함수의 몸체
  }
}
```
중첩 함수는 외부 함수 내부에서만 호출할 수 있다. 다시 말해, 중첩 함수는 외부 [함수 몸체 내에서만 유효]()하다는 의미다. 
```javascript
function outerFunc() {
  
  function nestedFunc() {
    console.log("외부 함수 몸체 안에서만 호출해줘!");
  }
  
  nestedFunc(); 
} 

nestedFunc(); // → Uncaught ReferenceError: nestedFunc is not defined
outerFunc(); // → 외부 함수 몸체 안에서만 호출해줘!
```
중첩 함수는 외부 함수 몸체 내에서만 유효하다면 중첩 함수 밖에, 그리고 외부 함수 내의 있는 식별자는 참조할 수 있을까? 가능하다.
```javascript
function outerFunc() {
  var a = 10; 
  
  function nestedFunc() {
    var b = 20; 
    console.log(a + b); // → 30
  }
  
  nestedFunc(); 
}

outerFunc();

```

<br>

***
### 참고 
- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [자바스크립트의 중첩 함수(Nested Fuctions)는 언제 사용해야 할까](https://siyoon210.tistory.com/162)
- [[JS] 클로저란? 중첩함수?](https://velog.io/@hahaha/JS-%ED%81%B4%EB%A1%9C%EC%A0%80)
- [JavaScript 함수(2) 중첩 함수와 재귀 함수](https://absolutelyfullycapable.github.io/2020-12/javascript-function-2-nested-function-recursive-function)