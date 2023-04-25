# 재귀 함수

### 목차

- [1 재귀 함수란](#1-재귀-함수란)
- [2 팩토리얼 재귀 함수](#2-팩토리얼-재귀-함수)
- [3 재귀 함수와 반복문](#3-재귀-함수와-반복문)

***

<br>

## 1 재귀 함수란

함수 내부에서 자기 자신을 반복해서 호출하는 함수를 재귀 함수(Recursive Function)라고 부른다. 재귀 함수는 보통 반복되는 처리를 위해 사용한다

아래 함수 `loop`의 예제 코드는 반복문을 통해 함수 몸체를 반복해  `0`에서 `10`까지 출력한다
```javascript
// 반복문을 활용한 0에서 10까지 출력하는 함수
function loop(count) {
  for (var i = count; i <= 10; i++) {
    console.log(i); 
  }
} 
  
loop(0); // → 0 1 2 3 4 5 6 7 8 9 10
```
위 반복문을 활용한 함수를 재귀 함수를 통해 동일하게 구현할 수 있다. 반대로, 반복(Iterate)이 필요한 작업은 반복문으로도 구현이 가능하다는 의미다
```javascript
function recursiveFunc(count) { 
  if (count > 10) return;
  console.log(count);
  recursiveFunc(count + 1);
}

recursiveFunc(0); // → 0 1 2 3 4 5 6 7 8 9 10

/* 위 조건문을 풀어서 쓴 코드 */ 
=> if (count > 10) {
  return; 
	} // 조건식이 true일 경우 코드의 실행 흐름이 함수 내부를 탈출한다 
	  // 조건식이 false일 경우 함수 몸체의 다음 코드블럭을 실행한다 
```
<img src="https://ifh.cc/g/7hWATB.png" style="max-width: 100%" align="center"/>
<br>

## 2 팩토리얼 재귀 함수

재귀 함수의 활용 예제로 팩토리얼 재귀 함수가 가장 대표적이다 
```javascript
function factorial(number) {
  if (number <= 1) return 1;
	return number * factorial(number - 1); 
} 

console.log(factorial(0)); // → 1
console.log(factorial(1)); // → 1
console.log(factorial(2)); // → 2
console.log(factorial(3)); // → 6
console.log(factorial(4)); // → 24
console.log(factorial(5)); // → 120
```
팩토리얼(Factorial)이란 수학에서 자연수의 '계승'이라는 개념으로, **1부터 자신의 수까지 순서대로 양의 정수 1씩**을 늘려가며 곱한 값을 구하는 것이다. 

> 수학에서 팩토리얼의 기호는 `!`로 피연산자 뒤에 위치한다

예를 들어 `3!`은 `1 * 2 * 3`의 결과인 `6`이 된다. 그리고  0과 1의 팩토리얼은 1이다. 위 예제 코드는 팩토리얼의 수학적 공식(`n! = n * (n-1)`)과 결과를 프로그래밍으로 표현한 것이다

이해를 위해 위 예제 코드를 쪼개서 살펴보자. 함수 `factorial`은 매개변수 `number`를 통해 인수를 전달 받는다. 앞서 언급했듯이, `0!`과 `1!`은 `1`이기 때문에 조건식 `number <= 1`가 `true`일 때 `return` 값으로 `1`을 함수 외부로 반환한다

<img src="https://ifh.cc/g/KC4WdW.png" style="max-width: 100%" align="center"/>

그렇다면 매개변수가 `0` 혹은 `1`도 아닌 `4`일 때 재귀 함수가 어떻게 동작하는지 살펴보자. 매개변수 `number`에 인수 `4`가 전달되면 조건식 평가 결과는 `false`이므로, 첫 번째 반환문인 `return 1`은 실행되지 않는다. 따라서 인수 `4`는 두 번째 반환문인 `return number * factorial(number - 1)`의 매개변수에 전달된다

<img src="https://ifh.cc/g/dVOArD.png" style="max-width: 100%" align="center"/>

인수가 전달된 두 번째 반환문은 `return 4 * factorial(3)`이 된다. 이때 재귀 호출이 발생한다. `return` 값으로 `4 *`는 반환되지만 재귀 호출 `factorial(3)`으로 함수 `factorial`은 재실행된다. 그리고 조건식 평가 결과가 `true`일 때까지 반복해서 함수가 실행되며, 결과로 `24`가 최종적으로 `return`(반환)된다 

<img src="https://ifh.cc/g/WB8bba.jpg" style="max-width: 100%" align="center"/>
<br>

## 3 재귀 함수와 반복문

위 팩토리얼 재귀 함수 예제를 아래 예제 코드처럼 반복문을 통해 코드 길이를 최소화할 수도 있다
```javascript
function factorial(number) {
  if (number <= 1) return 1;
  return number * factorial(number - 1);
}

for (var i = 0; i <= 5; i++) {
  var result = factorial(i);
  console.log(result); // → 1 1 2 6 24 120 
}
```

재귀 함수는 자기 자신을 무한적으로 재귀 호출한다. 따라서 **재귀 함수 내에는 재귀 호출을 멈출 수 있는 탈출 조건을 반드시 생성**해놔야 한다. 그렇지 않을 시 함수가 무한 호출되어 [스택 오버 플로(Stack Overflow)]() 에러가 발생하기 때문이다. 따라서 재귀 함수는 일반적으로 조건식이 있는 제어문과 함께 쓰인다

대부분의 재귀 함수는 반복문(`for`문, `while`문)으로 구현이 가능하다. 재귀 함수는 반복이 필요한 처리를 반복문 없이 구현할 수 있다는 장점이 있지만 무한 반복/호출(스택 오버플로)에 빠질 위험이 있다. 따라서 재귀 함수는 사용하는 편이 더 직관적이고 간결할 때만 한정적으로 사용하고, 반복 작업이 필요한 처리는 최대한 반복문을 활용하는 것이 바람직하다
```javascript
// 팩토리얼 재귀 함수의 예제를 반복문으로만 처리하는 함수
function factorial(number) {
  if (number <= 1) return 1; // 함수가 반복되지 않게 탈출시키는 장치

  var result = number;
  while (--number) { // number = number - 1 (결과 값은 Truthy 값으로 분류되어 true를 반환)
    result *= number; // result = result * number
  }
  return result; // 함수가 반복되지 않게 탈출시키는 장치
}

// 0부터 5의 팩토리얼까지 반환하는 반복문
for (var i = 0; i <= 5; i++) {
  console.log(factorial(i)); // → 1 1 2 6 24 120
}
```
***
### 참고
- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [재귀(Recursion) 함수 사용 방법](https://koonsland.tistory.com/135)
- [재귀 패턴(Recursive Pattern)](https://window6kim.tistory.com/31)
- [재귀함수 1](https://velog.io/@jeongin/Javascript-%EC%9E%AC%EA%B7%80%ED%95%A8%EC%88%98)
- [재귀함수 2](https://spiritfestival.tistory.com/111)
- [자바스크립트, 재귀함수에 대하여(Recursion)](https://im-developer.tistory.com/102)
- [스택 오버 플로우(SOF)](https://incheol-jung.gitbook.io/docs/q-and-a/java/stw)