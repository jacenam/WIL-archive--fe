# 함수란

함수(Function)는 JS를 정확히 이해하고 사용하기 위해 JS에서 가장 중요한 핵심 중의 핵심 개념이다. JS 뿐만 아니라 프로그래밍 언어의 함수도 수학의 함수와 같은 개념이니, 프로그래밍 함수에 대한 이해를 돕기 위해 수학에서의 함수 개념을 먼저 살펴보고 함수의 기본 개념에 대해 알아보자

<br>

### 목차

- [1 수학에서의 함수](#1-수학에서의-함수)
- [2 프로그래밍에서의 함수](#2-프로그래밍에서의-함수) 
  - [2-1 함수의 생성](#2-1-함수의-생성)
  - [2-2 함수의 사용](#2-2-함수의-사용)
  - [2-3 함수를 사용하는 이유](#2-3-함수를-사용하는-이유)
- [3 함수 리터럴](#3-함수-리터럴)
  - [3-1 함수 이름](#3-1-함수-이름)
  - [3-2 매개변수](#3-2-매개변수) 
  - [3-3 함수 몸체](#3-3-함수-몸체)
  - [3-4 일급 객체인 함수](#3-4-일급-객체인-함수)

***

<br>

## 1 수학에서의 함수

함수란 입력 값(Input) 하나를 넣었을 때 특정 규칙을 통해 하나의 출력 값(Output)을 결과로 내보내는 일련의 과정을 의미한다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207314966-b4c7c52e-6c2f-40a2-af7d-72affed0b405.png">

입력 값이 들어갔을 때 위 그림의 ‘상자'안에서 어떤 과정을 통해 출력 값이 나오는지 알 수 없기 때문에 이 ‘상자'를 블랙박스(Black Box)라고 부른다. 하지만 입력 값과 출력 값을 봤을 때 상자 안에서 이뤄지는 규칙을 유추해볼 수 있다( $output = (input \times 2) + 1$ ). 이러한 특성을 가졌기 때문에 ‘함수’(지닐 함/상자 함 函, 셈 수/숫자 수 数)라고 부르는 것이다

<br>

함수는 “집합 `input`의 원소들이 집합 `output`의 원소들과 하나씩만 대응할 때 집합 `input`에서 집합 `output`로의 함수”라고 정의할 수 있다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207316708-cc57d1c1-2c7c-448d-a906-6d9f84ca21cb.png">

집합 `input`의 원소인 입력 값 하나에 대해서 집합 `output`의 원소인 출력 값 하나가 나오는 것을 ‘대응’이라고 부른다("`input` 값 `1`은 `output` 값 `3`에 대응한다"). 집합들의 이름은 꼭 `input`, `output`이 임의로 정할 수 있다(대체로 알파벳 대문자 한 글자를 사용한다)

이러한 대응 관계가 기본인 함수를 수학적 기호로 다음과 같이 나타낸다: $f: X → Y$

그리고 위 함수 예제를 수학적 기호로 다음과 같이 나타낸다( $X$와 $Y$의 원소들을 각각 $x$, $y$라고 가정했을 때 ): $y = f(x), f(x) = 2x + 1$

<br>

따라서 함수에는 아래와 같은 조건이 따른다:

1. 집합 `input`의 모든 원소들이
2. 집합 `output`의 원소들과 하나씩만 대응해야 한다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207322147-d37011bc-05b9-4f7f-9bf4-4bd762d914b8.png">

<br>

집합 `input`의 원소들은 집합 `output`의 원소들과 하나씩만 대응하고 있다. 위 함수의 조건을 다시 살펴보면, 집합 `output`의 원소들은 모두 대응될 필요가 없다. 그렇다면 집합 `output`의 원소 중 집합 `input`과 대응되지 않는 원소들은 무엇일까? 

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207322520-d02b8b85-0663-4d18-a688-3dee56d77609.png">

집합 `input`의 원소들은 언제나 집합 `output`의 원소와 대응된다는 의미에서 정의역(Domain)이라 부른다. 집합 `output`의 모든 원소들을 공역(Codomain)이라 부르며 정의역 원소들과 대응되는 원소, 대응되지 않는 원소들은 모두 공역에 해당된다. 그리고 공역 내에서 대응된 원소들을 치역(Range)이라 부른다. 따라서 치역은 항상 공역의 부분 집합이기도 하다( $Range \subset Codomain$ )

<br> 

정의역의 원소들이 유한할 때 위 대응 그래프 외에도 우리가 흔히 아는 '함수 그래프( $x$ 축, $y$ 축 )'를 통해 `input`(정의역)과 `output`(치역)의 관계를 표현하는 것이 가능하다. 예를 들어, 정의역(집합 input)의 원소가 ${1, 2, 3, 4}$ 로서 유한할 때: 

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207323451-0ae6d0f6-1429-44f9-b892-75f5c86424c5.png">


그렇다면 정의역의 원소들이 무한적일 때는 어떻게 나타낼 수 있을까? 이 또한 '함수 그래프'를 통해 정의역의 무한한 원소( $x$ )들과 이에 대응하는 치역( $y$ )들의 관계를 나타낼 수 있다. 정의역과 치역은 무한하기 때문에 점들이 모여 '선'이 된다. 예를 들어, 정의역의 원소가 모든 실수로 무한할 때:


<img width="100%" src="https://user-images.githubusercontent.com/92138751/207324698-42fd1853-8cc9-4bcf-bfa8-2a05e4989d46.png">

<br> 

## 2 프로그래밍에서의 함수

그렇다면 수학에서의 함수는 프로그래밍에서 어떻게 표현되고 쓰일까? 위 수학 함수에서의 예제 $f(x) = 2x + 1$ 를 JS 코드로 표현해보자

```javascript
function formula(x) {
	return 2 * x + 1; 
}

formula(3); // → 7

var result = formula(3);
```

<br>

함수의 구성 요소를 살펴보면 수학에서의 함수 개념을 프로그래밍에서 활용했다는 것을 알 수 있다

| **수학**                | **예제**                 | **프로그래밍**                    | **예제**                                   |
| ----------------------- | ------------------------ | --------------------------------- | ------------------------------------------ |
| **함수**                | $y = f(x)$               | **함수**                          | `function (x)`                             |
| **블랙박스 이름**       | (없음)                   | **함수 이름**                     | `function formula(x)`                      |
| **블랙박스 내부**       | (알 수 없으나 유추 가능) | **코드블록(블록문, 함수 몸체)**   | `function formula(x) { return 2 * x + 1 }` |
| **정의역(집합`input`)** | $X$                      | **매개변수(Parameter, 인자)**     | `function formula(x)`                      |
| **정의역 원소**         | $x: {1, 2, 3, 4}$        | **인수(Argument)**                | `formula(3)`                               |
| **치역(집합`output`)**  | $Y$                      | 함수의 연산 결과 값에 대한 식별자 | `var result = formula(3)`                  |
| **치역 원소**           | $y: {3, 5, 7, 9}$        | **코드블록 내부의 작업 결과**     | `return 2 * x + 1`를 연산을 통한 결과      |

프로그래밍(JS)에서 함수는 특정 목적을 가진 작업 또는 연산을 수행하는 코드들이 모인 코드블록(블록문)이라 할 수 있다. 입력 값(수학: input, 프로그래밍: 인수)을 입력해 코드블록 내부에 정해진 규칙대로 작업 또는 연산을 수행하고 결과를 출력(수학: output, 프로그래밍: 반환)하는 것이다

<br>

### 2-1 함수의 생성

함수는 함수 정의(Function Definition)를 통해 생성해야 한다. 함수 정의란 함수를 사용하기 이전에 미리 작성해서 JS 엔진에 인식시키는 작업으로 함수에 어떤 값을 입력할지, 입력 값이 전달되었을 때 어떤 작업과 연산을 수행할지, 그리고 그 작업과 연산의 결과물을 어떻게 출력할지를 정의하는 것이다

```javascript
function formula(x) {
	return 2 * x + 1;
} 
```

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207333851-c54775cc-613a-4f46-a0a1-cfff78b9daeb.png">

`formula`라는 이름을 가진 함수가 특정 값(인수)을 매개변수(인자)  `x`를 통해 입력(전달)받았을 때 함수 몸체에 있는 `return 2 * x + 1`이라는 작업과 연산을 수행해 `2 * x + 1`에 해당하는 결과물을 반환(출력)하라”라는 함수를 정의한 것이다

> 함수는 다양한 방법으로 정의할 수 있다. 이에 대해서는 [함수 정의 방법]()을 참고하자

<br> 

### 2-2 함수의 사용

함수를 정의했다고만 해서 함수를 바로 사용할 수 있는 것은 아니다. ‘함수 정의’는 말 그대로 어떤 함수인지 JS 엔진에 인식시키기 위한 설명의 과정일 뿐이다. 정의된 함수를 사용하기 위해서는 필요한 입력, 즉 인수를 매개변수를 통해 함수에 전달하고 함수의 실행을 명시적(의도적)으로 지시해야한다. 이러한 지시를 함수 호출(Function Call/Invoke)이라 부른다

```javascript
// 함수 정의 
function formula(x) {
	return 2 * x + 1;
} 

// 함수 호출
formula(3); // → 7
```

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207336221-bfee6450-ce07-41c2-ba8d-926e9c542352.png">

함수 이름(`formula`)의 괄호(`()`)안에 인수를 입력하면 함수 호출이 이뤄진다. 인수 `3`을 입력하면 매개변수 `x`에  `3`이 전달되며, 인수가 전달되었으니 코드블록(함수 몸체) 내부의 `return 2 * x + 1`이라는 코드가 실행되어 결과 `7`를 반환한다

> 함수 호출은 JS 함수에서 중요한 개념이다. 이에 대한 더 자세한 내용은 [함수 호출]()을 참고하자

<br>

### 2-3 함수를 사용하는 이유

함수는 한번 정의해두면 여러 번 호출해서 재사용이 가능하다. 이는 곧: 

1. 동일한 반복 작업이 필요할 시 코드를 줄여 가독성을 향상시킨다 
2. 함수 코드블록 내부의 코드만 수정하면 함수가 재사용된 코드 부분까지 수정할 수 있어 유지보수가 쉽다

```javascript
// 함수 없이 동일 작업의 반복
var name; // → undefined
var notify = 'Hello';  

name = 'Jace';  
if (name) { // 변수 name은 빈 문자열이 아니므로 Truthy로 분류되어 true
	notify = `Hello, ${name}`;	
	console.log(notify); // → Hello, Jace
}

name = 'Ju Hyung'; 
if (name) { 
	notify = `Hello, ${name}`;	
	console.log(notify); // → Hello, Ju Hyung
}

name = 'Zhou Heng'; 
if (name) { 
	notify = `Hello, ${name}`;	
	console.log(notify); // → Hello, Zhou Heng
}


// 함수를 통한 동일 작업의 반복
var notify = 'Hello'; 

function sayHello(name) {
	if(name) { // 인수로 빈 문자가 아닌 문자열이 나오면 Truthy로 분류되어 true 
		notify = `Hello, ${name}`; 
		console.log(notify); 
	}
}

sayHello('Jace'); // → Hello, Jace
sayHello('Ju Hyung'); // → Hello, Ju Hyung
sayHello('Zhou Heng'); // → Hello, Zhou Heng
```

<br>

## 3 함수 리터럴

[리터럴]()이란 값을 나타내기 위한 표기법이라 했다. 함수 리터럴은 함수를 함수라고 JS 엔진에 인식시키기 위한 리터럴이다. 함수는 객체이므로 함수 리터럴로 나타낸 값은 객체 타입이다

```javascript
function 함수이름(매개변수) { 
	함수 몸체
}
```

기본적인 형태의 함수 리터럴은 함수 키워드인 `function`, 함수 이름, 매개변수, 함수 몸체로 구성된다. 함수 정의 방식인 함수 선언문, 함수를 값으로 생성하는 함수 표현식은 모두 함수 리터럴 방식을 사용한다

> 함수 정의 방식인 함수 선언문과 함수 표현식에 대해서는 [여기]()를 참고하자

<br>

### 3-1 함수 이름

함수 이름은 식별자이므로 변수 네이밍과 마찬가지로 [식별자 네이밍 규칙]()을 준수해야한다

```javascript
// 카멜 케이스
function sayHello(name) {
}
```

함수 이름은 함수 몸체 내에서만 참조할 수 있는 식별자다. 함수 이름은 함수 몸체 내에서만 참조할 수 있다는 사실만 알고 넘어가자

> 이에 대해서는 뒤에 나올 [함수 선언문과 함수 표현식의 차이]()의 내용을 참고하자

```javascript
var msg = function sayHello(name) {
	console.log(`Hello, ${name}`); 
} 

sayHello('Jace'); // → Uncaught ReferenceError: sayHello is not defined
msg('Jace'); // → Hello, Jace
```

함수 이름은 생략할 수 있다. 이름이 있는 함수를 기명 함수(Named Function), 이름이 없는 함수를 무명/익명 함수(Anonymous Function)라 한다

```javascript
// 기명 함수
function sayHello(name) {
	console.log(`Hello, ${name}`); 
} 
```

```javascript
// 익명 함수
function(name) {
	console.log(`Hello, ${name}`); 
} // → Uncaught SyntaxError: Function statements require a function name

var msg = function(name) {
	console.log(`Hello, ${name}`); 
} 

msg('Jace'); // → Hello, Jace
```

익명 함수는 단독의 형태로 사용하면 `SyntaxError`가 나온다. 따라서 변수에 함수를 할당하는 대신 함수 이름을 사용하지 않아도 된다. 대신 변수 이름을 함수 이름처럼 사용해서 함수를 호출하거나 값(함수)을 복사할 수 있다. 이에 대해서는 [함수 표현식]()을 참고하자

<br>

### 3-2 매개변수

매개변수에 대한 내용은 [함수 호출]()에서 더 다뤄볼 예정이나, 함수 리터럴의 구성 요소이기 때문에 여기서는 간략하게만 정리했다. 매개변수는 없을 수도 있다. 매개변수가 한 개 이상일 때는 쉼표(`,`)로 구분한다

```javascript
// 매개변수가 없을 때
function add() {
	return 1 + 2; 
}

// 매갭변수가 한 개 이상일 때
function add(x, y, z) {
	return x + y + z; 
}
```

매개변수가 한 개 이상일 때 각 매개변수에는 함수 호출 시 지정한 인수가 순서대로 할당된다 

```javascript
function add(x, y, z) {
	return x + y + z; // x에 1, y에 3, z에 2가 순서대로 할당된다
} 

add(1, 3, 2); // → 6
```

매개변수도 변수, 함수 이름과 마찬가지로 식별자 네이밍 규칙을 준수해야한다(매개변수는 함수 몸체 내에서 변수와 동일하게 취급되기 때문이다) 

```javascript
// 카멜케이스
function add(firstArgu, secondArgu) {
	return firstArgu + secondArgu; 
} 

add(1, 2); // → 3
```

<br> 

### 3-3 함수 몸체

함수 몸체는 함수가 호출되었을 때 실행될 코드블록(블록문)이다. 중요한 것은 함수가 호출되었을 때만 함수 몸체의 코드블록들이 실행된다

```javascript
// 함수 호출 없이 함수 정의만 이뤄진 경우
function add(x, y, z) {
	return x + y + z; 
} // → undefined
```

함수 정의만 이뤄졌을 때는 함수가 호출되지 않으므로 인수가 매개변수(인자)로 전달되지 않았기 때문에 언제나 `undefined`가 반환된다. 매개변수 `x`, `y`, `z`에 들어올 인수 값이 없는 `undefined` 상태이기 때문이다 

<br>

### 3-4 일급 객체인 함수 

JS에서 함수는 객체 타입의 값이라 했다. 그러나 함수는 일반 객체와는 다르다

|                 | **일반 객체**      | **함수**                               |
| --------------- | ------------------ | -------------------------------------- |
| **데이터 타입** | 객체               | 객체(더 정확히 표현하자면 일급 객체다) |
| **호출**        | 호출 불가능        | 호출을 통해 함수를 실행                |
| **프로퍼티**    | 일반 프로퍼티 소유 | 고유 프로퍼티 소유                     |

함수가 객체라는 사실은 다른 프로그래밍 언어와 구별되는 JS만의 중요한 특징이다. 이 특징을 제대로 이해하지 않으면 JS의 함수를 제대로 이해하기 어렵다. JS 함수만의 특징과 일반 객체와의 차이에 대해서는 [함수와 일급 객체]()를 참고하자

<br>

***

### 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [자바스크립트에서 함수란? (객체/함수 선언문/함수 표현식)](https://basemenks.tistory.com/36)
- [수학 함수 기초](https://www.youtube.com/watch?v=b6t_MhwOtDg)
- [TCP School - 함수](http://www.tcpschool.com/codingmath/function)

