# 제네레이터와 async/await

### 목차

- 

***

<br>

ES8(2017)에서는 제네레이터(`function*`)보다 더 간단하고 가독성 좋게 비동기 처리를 구현할 수 있게 하는 `async`/`await`이 도입되었다. 이에 보통 `async`/`await`를 사용하지만, 제네레이터의 동작 원리를 이해하면 `async`/`await`의 개념을 이해하는데 도움이 되기에 먼저 제네레이터에 대해 살펴보겠다

<br>

***
### 참고
- [모던 자바스크립트 Deep Dive]()
- [JavaScript MDN - async function](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function)
- [모던 JavaScript 튜토리얼 - async/await](https://ko.javascript.info/async-await)
- [자바스크립트 async와 await](https://joshua1988.github.io/web-development/javascript/js-async-await/)
- [자바스크립트에서 비동기 처리 다루기 02. async/await](https://learnjs.vlpt.us/async/02-async-await.html)