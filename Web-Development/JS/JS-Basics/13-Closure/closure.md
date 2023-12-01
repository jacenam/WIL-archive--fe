# 클로저란

클로저(Closure)는 하스킬(HasKell), 리스프(Lisp), 얼랭(Erinag), 스칼라(Scala) 등에서도 사용되는 개념이다. 그러나 JS에서의 클로저는 난해하기로 유명한 개념 중 하나이기 때문에 이해가 될 때까지 반복 학습할 예정이다

**Table of Contents**

- [클로저란](#클로저란)

<br>

## 클로저란

일단 예제를 살펴보자

```javascript
const x = 1;

function outerFunc() {
  const x = 10;
  
  function innerFunc() {
    console.log(x);
  }
  innerFunc();
}

outerFunc();
```



<br>

## 참고 

- [JavaScript MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
- [모던 JavaScript 튜토리얼 - 변수의 유효범위와 클로저](https://ko.javascript.info/closure)