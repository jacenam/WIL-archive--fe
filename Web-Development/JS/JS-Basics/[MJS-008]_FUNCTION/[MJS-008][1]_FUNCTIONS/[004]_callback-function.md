# 콜백 함수

**Table of Contents**

- [콜백 함수란](#콜백-함수란)
- [콜백 함수 예제](#콜백-함수-예제)
  - [콜백 함수 내부 동작](#콜백-함수-내부-동작)
  - [콜백 함수와 함수 표현식(feat. 일급 객체)](#콜백-함수와-함수-표현식(feat.-일급-객체))

<br>

## 콜백 함수란 

콜백 함수(Callback Funciton)는 쉽게 말해서 함수 안에서 또 다른 함수를 실행하는 것이다. [중첩 함수]()와의 차이점으로 중첩 함수는 외부 함수 내에서 내부 함수의 정의를 통해 중첩된 함수들을 유기적으로 실행하는 것이라면, 콜백 함수는 다른 함수 외부에서 함수를 정의하고 그 함수의 매개변수를 통해 내부로 전달되는 함수다

<br>

## 콜백 함수 예제

예제로, 매개변수에 인수로 전달받은 숫자보다 작고 0보다 크거나 같은 수를 모두 출력하는 함수를 정의해보자 

```javascript
// 모든 수를 출력하는 함수
function returnAll(number) {
	for (var i = 0; i < number; i++) {
      console.log(i); 
    }
}

returnAll(10); // → 0 1 2 3 4 5 6 7 8 9 
```
만약 인수로 전달받은 숫자보다 작고 0보다 크거나 같은 홀수 또는 짝수를 각각 출력하는 함수가 필요하다면 함수들을 새롭게 생성해야한다 

> 위 예제의 `if`문 조건식 `i % 2`는 `true`로 평가되는 Truthy 값, `false`로 평가되는 Falsy 값에 따라 뒤에 코드블록을 실행한다. 이해가 되지 않는다면 [암묵적 타입 변환]()을 먼저 참고하자

```javascript
// 홀수만 출력하는 함수
function returnOdd(number) {
  for (var i = 0; i < number; i++) {
 	if ( i % 2 ) console.log(i); 
  }
}

returnOdd(10); // → 1 3 5 7 9

// 짝수만 출력하는 함수 
function returnEven(number) {
  for (var i = 0; i < number; i++) {
      if ( i % 2 === 0 ) console.log(i); 
  }
}

returnEven(10); // → 0 2 4 6 8
```
위 예제들의 `returnAll`, `returnOdd`, `returnEven` 함수들을 살펴보면 `0`에서부터 인수로 전달받은 `10`까지 조건에 맞춰 반복하는 반복문 `for(var i = 0; i < number; i++)`을 모두 공통적으로 수행한다. 다만, '숫자 전체', '홀수', '짝수'만 출력하는 부분만 다르다

이런 경우에 콜백 함수가 유용하게 쓰일 수 있다. 위 함수들이 공통적으로 수행하는 로직을 생성해두고, 경우에 따라 변경되는 로직들을 생성해 함수 외부에서 함수 내부로 전달하는 것이다 

```javascript
// 공통의 로직들을 구현해낸 함수
function commonLogic(number, extraLogic) {
  for ( var i = 0; i < number; i++ ) {
    	extraLogic(i); 
    }
}

// 변경되는 로직들을 각각 구현해낸 함수 (함수 표현식 형태)
var returnAll = function(i) {
  console.log(i)
}

var returnOdd = function(i) {
  if ( i % 2 ) console.log(i);
}

var returnEven = function(i) {
  if (i % 2 === 0) console.log(i); 
}

commonLogic(10, returnAll); // → 0 1 2 3 4 5 6 7 8 9
commonLogic(10, returnOdd); // → 1 3 5 7 9
commonLogic(10, returnEven); // → 0 2 4 6 8
```
### 콜백 함수 내부 동작

콜백 함수가 어떻게 동작하는지 뜯어서 살펴보자. 함수 `returnAll`, `returnOdd`, `returnEven`의 콜백 함수는 모두 동일하게 동작하기 때문에, 함수 `returnAll`의 콜백 함수 예제만 작성했다

먼저 함수 `commonLogic`의 매개변수 `number`와 `extraLogic`에 `10`, `returnAll`을 인수로 각각 전달한다. `returnAll`의 값은 함수다
```javascript
var returnAll = function(i) {
  console.log(i);
} 

console.log(returnAll); // → f(i) { console.log(i); } 
```
따라서 매개변수 `extraLogic`에는 `returnAll`의 값인 함수가 전달된다. JS에서는 함수를 값처럼 활용하여 변수에 할당할 수 있다. 이는 JS에서 [함수는 일급 객체]()로 취급되기 때문이다
```javascript
function commonLogic(number, extraLogic) {
  console.log(extraLogic === returnAll); // → true
  for ( var i = 0; i < number; i++ ) {
    extraLogic(i); 
  }
}

var returnAll = function(i) {
  console.log(i)
}

commonLogic(10, returnAll);
```
### 콜백 함수와 함수 표현식

다음으로 [함수 호출]()과 [함수 정의]() 파트의 내용을 복기하자면,
- **매개변수는 함수 몸체 내부에서 변수와 동일하게 취급된다**
- **함수 표현식에서 익명 함수를 변수에 할당하면 변수 이름과 동일한 이름으로 함수 식별자(함수 이름)가 암묵적으로 생성된다**

함수 `commonLogic`의 매개변수인 `extraLogic`은 함수 `commonLogic`내에서 변수처럼 동작한다. 따라서 `function(i) { console.log(i) }`는 `extraLogic`과 동일한 함수 식별자가 암묵적으로 생성된다. 그렇기 때문에 함수 `commonLogic` 몸체 내부에 함수 `extraLogic`을 호출하는 함수 호출문 `extraLogic(i)`이 존재하는 것이다. 그리고 함수 `extraLogic`은 호출되어 인수 `i`를 매개변수 `i`에 전달하면서 결과 값이 출력되는 것이다

<img src="https://velog.velcdn.com/images/jacenam/post/13bd6306-5974-48e7-b281-5139bd703db9/image.png" style="max-width: 100%" align="center">

이처럼 콜백 함수는 특정 함수의 매개변수를 통해 그 함수의 내부로 전달될 수 있다. 이는 앞서 언급했듯이 JS의 함수가 일급 객체로서 값처럼 취급되어 함수 내부의 변수인 매개변수에 할당이 가능해서다. 콜백 함수를 활용해 외부에서 로직의 일부분을 재설계해 함수 `commonLogic`도 보다 유연한 구조를 갖을 수 있게 된다

> 함수와 일급 객체, 프로토타입, 고차 함수 학습 후 내용 추가할 예정

<br>

## 참고 

- [모던 자바스크립트 Deep Dive]()
- [JavaScript MDN]() 
- [[JS] 자바스크립트 콜백 함수 총 정리](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%BD%9C%EB%B0%B1-%ED%95%A8%EC%88%98)
- [[JavaScript] 고차 함수와 콜백(Callback) – 일급 객체란?](https://hanamon.kr/javascript-%EA%B3%A0%EC%B0%A8%ED%95%A8%EC%88%98%EC%99%80-%EC%BD%9C%EB%B0%B1-%EC%9D%BC%EA%B8%89%EA%B0%9D%EC%B2%B4%EB%9E%80/)
- [JavaScript - callback](https://www.youtube.com/watch?v=TAyLeIj1hMc)