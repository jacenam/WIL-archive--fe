# 함수 정의

**Table of Contents**

- [함수 정의 방법](#함수-정의-방법)
- [함수 선언문](#함수-선언문)
  - [기명 함수](#기명-함수)
  - [함수 리터럴의 단독 사용](#함수-리터럴의-단독-사용)
- [함수 표현식](#함수-표현식)
- [함수 선언문과 함수 표현식의 차이](#함수-선언문과-함수-표현식의-차이)
  - [함수 정의 방법에 따른 함수 생성의 내부 동작](#함수-정의-방법에-따른-함수-생성의-내부-동작)
    - [함수 선언문의 내부 동작 ](#함수-선언문의-내부-동작)
    - [함수 표현식의 내부 동작](#함수-표현식의-내부-동작)
  - [함수 생성 시점과 호이스팅](#함수-생성-시점과-호이스팅)
- [Function 생성자 함수](#Function-생성자-함수)
- [화살표 함수](#화살표-함수)

<br>

## 함수 정의 방법

앞서 함수는 [함수 정의(Function Definition)]()를 통해 생성된다 했다. 함수를 호출하기 이전에 인수를 전달받을 매개변수, 인수가 전달되었을 때 실행될 문, 문들이 실행되고 출력될 반환 값을 지정하는 것을 의미한다. 함수를 정의하는 방법은 4가지가 있다:

1. 함수 선언문

   > 함수 선언문은 블록문이다. 블록문은 언제나 문의 종료를 의미하는 자체 종결성을 갖기 때문에 블록문의 끝에는 세미콜론(`;`)을 붙이지 않는다([제어문의 블록문]()과 동일하다)

   ```javascript
   function sayHello(name) {
     console.log(`Hello, ${name}`); 
   }
   ```

2. 함수 표현식

   > 함수는 객체 타입의 값이지만 값처럼 변수에 할당이 가능하다. 위 함수 표현식은 함수 표현식이자 변수 선언문(할당문)이라고 할 수 있다. 변수 선언문이기 때문에 문의 끝에 세미콜론(`;`)을 붙여야 한다. 또한, 함수 표현식에서는 일반적으로 함수 이름을 사용하지 않는다(그 이유에 대해서는 뒤에 나올 내용을 참고하자)

   ```javascript
   var msg = function(name) {
     console.log(`Hello, ${name}`); 
   };
   ```

3. `Function` 생성자 함수

   ```javascript
   var msg = new Function('name', 'y', 'console.log(`Hello, ${name}`)');
   ```

4. 화살표 함수(ES6에서 추가됨)

   ```javascript
   var msg = (name) => console.log(`Hello, ${name}`); 
   ```


<br>

## 함수 선언문

일반적으로 함수 리터럴로 함수를 나타내면 JS 엔진은 이를 평가해 코드 문맥에 따라 함수 선언문 또는 함수 표현식으로 인식한다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207480222-b8039705-e574-46f2-912b-8b41ddf8f7cc.png">

함수 리터럴이 JS 엔진에 의해 함수 선언문으로 인식되기 위해서는 아래 두 가지 조건이 모두 충족되어야 한다: 

- 함수 리터럴이 [함수 이름이 있는 기명 함수]()여야 하며
- 함수 리터럴을 단독으로 사용해야 한다

### 기명 함수 

[가장 기본적인 형태의 함수 리터럴]()의 구성 요소와 같이 함수 선언문의 구성 요소는 함수 키워드 `function`, 함수 이름, 매개변수, 함수 몸체로 동일한 형태다

```javascript
// 함수 리터럴과 함수 선언문의 구성
function 함수 이름(매개변수) {
  함수 몸체 
}
```

단, 함수 리터럴은 함수 이름을 생략할 수 있으나 함수 선언문은 함수 이름을 생략할 수 없다. 다시 말해, 이름이 없는 익명 함수는 함수 선언문이 될 수 없다

```javascript
// 함수 선언문
function sayHello(name) {
  console.log(`Hello, ${name}`); 
}

function (name) {
  console.log(`Hello, ${name}); 
} // → Uncaught SyntaxError: Function statements require a function name
```

### 함수 리터럴의 단독 사용

함수 선언문은 값을 따로 생성하지 않는 [표현식이 아닌 문]()이기 때문에 선언 시 언제나 `undefined`가 반환된다. 즉, 함수 선언문은 값으로 평가될 수 없는 ‘문’이다

```javascript
// 함수 선언문(으로 평가되는 함수 리터럴)
function sayHello(name) {
  console.log(`Hello, ${name}`); // → undefined
}
```

(함수 선언문과 동일한 구성요소를 가진) 함수 리터럴을 변수에 할당하는 것과 같이, 함수 리터럴을 단독으로 사용하지 않을 경우 JS 엔진은 이를 함수 선언문이 아닌 함수 표현식으로 인식하게 된다. 함수 선언문은 표현식이 아닌 문이기 때문에 변수에 값처럼 할당할 수 없으므로, JS 엔진은 아래 함수 리터럴을 코드 문맥에 따라 함수 표현식으로 인식해 변수에 할당이 가능하도록 하는 것이다 

```javascript
var msg = function sayHello(name) {
  console.log(`Hello, ${name}`); 
}; 
```

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207481217-9ed0f4dd-d599-4ad7-ad0e-cc7060c528c9.png">

따라서 함수 리터럴이 JS 엔진에 의해 함수 선언문으로 인식되려면 이름이 있는 **기명 함수 리터럴을 단독으로 사용**해야 하는 것이다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207481379-9518fbde-dd15-499a-8241-36bcdb970c2e.png">

<br>

## 함수 표현식

JS의 함수는 [객체 타입]()의 값이다. 그리고 JS의 함수는 [일급 객체]()로서 값처럼 변수에 할당할 수도 있고 [프로퍼티 값]()이 될 수도 있으며 [배열의 요소]()가 될 수도 있다. 즉, 함수가 일급 객체라는 것은 함수를 값처럼 자유롭게 사용할 수 있다는 의미다

함수 리터럴로 생성한 함수 객체는 변수에 할당할 수 있으며, 이렇게 변수에 할당 가능한 함수 객체를 생성(정의)하는 방식을 함수 표현식(Function Expression)이라 한다. 함수 리터럴은 JS 엔진에 의해 평가되어 객체 타입의 함수 값을 생성하므로 ‘[표현식인 문]()’이며, 이를 함수 표현식(또는 함수 리터럴 표현식)이라고 부르는 것이다

```javascript
var sayHello = function (name) {
  console.log(`Hello, ${name}`); 
}

sayHello("Jace"); // → Hello, Jace
```

함수 선언문과는 달리, 앞서 [함수 리터럴의 함수 이름(익명 함수)은 생략]()할 수 있다 했다. 또한, 함수 이름이 있는 기명 함수 리터럴이 단독으로 쓰일 경우 JS 엔진에 의해 함수 선언문으로 인식된다 했다. 그렇기에 함수 선언문과 함수 표현식의 차이를 두기 위해 함수 표현식에서는 일반적으로 함수 이름을 지정하지 않는다. 함수 이름이 없는 함수 리터럴일 경우 JS 엔진에 의해 곧바로 함수 선언문으로 인식되지 않는다

```javascript
var add = function (x, y) {
  return x + y; 
}
```

함수 표현식을 표현하기 위한 함수 리터럴에서 대개 함수 이름을 생략하는 이유는 한 가지 더 있다. 함수를 호출할 때는 함수 이름이 아니라 함수 객체를 가리키는 식별자를 사용해야 한다는 것이다. 함수 이름은 함수 몸체 내부에서만 유효한 식별자이기 때문이다. 함수 객체를 가리키는 식별자와 함수 몸체 내부에 관련된 내용은 바로 뒤에 나올 내용을 참고하자

```javascript
var add = function sum(x, y) {
  return x + y;
}

// 함수 표현식에서 함수 이름을 이용한 함수 호출
sum(2, 5); // → Uncaught ReferenceError: sum is not defined

// 함수 표현식에서 함수 객체 식별자를 이용한 함수 호출
add(2, 5); // → 7
```

<br>

## 함수 선언문과 함수 표현식의 차이 

함수 선언문이든, 함수 표현식이든 ‘함수’를 생성한다는 사실에 있어서는 모두 동일하다. 그러나 함수를 생성하는 내부 동작(메모리)의 차이와 그로 인한 함수 호출에서의 차이가 있다

### 함수 정의 방법에 따른 함수 생성의 내부 동작

아래 예제에서 함수 선언문으로 생성된 `sayHello` 함수는 호출할 수 있으나 함수 표현식을 통해 생성된 `greetings`는 호출할 수 없다. 왜 그런 것일까?

```javascript
// 함수 선언문(기명 함수 리터럴을 단독으로 사용) 
function sayHello(name) {
  console.log(`Hello, ${name}`); 
} 

sayHello("Jace"); // → Hello, Jace

// 함수 표현식(기명 함수 리터럴을 그룹 연산자의 피연산자로서 사용)
(function greetings(name) {
  console.log(`Hello, ${name}`); 
}); 

greetings("Jace"); // → ReferenceError: greetings is not defined
```

> [그룹 연산자]()의 기능인 '우선 연산'을 위해 피연산자는 값으로 평가될 수 있는 표현식어야 한다

#### 함수 선언문의 내부 동작

먼저 함수 선언문을 통해 함수가 생성되었을 때의 메모리의 동작을 살펴보자. 함수는 객체 타입이기 때문에 [객체 값의 저장]()과 동일한 방식(참조 값을 통해 실제 값에 접근)으로 메모리에 값이 저장된다. 하지만 저장된 값을 불러들이는 참조(객체) 혹은 호출(함수)에서 차이가 있다

아래 예제에서 일반 객체는 변수 `user`라는 식별자를 통해 참조 값에 접근하고 객체 값을 참조할 수 있다. 그러나 아래 함수에는 참조 값에 접근하고 함수를 호출할 식별자가 없다. 함수 이름인 `sayHello`는 함수 몸체 내부에서만 유효한 식별자로 `0x00000003`에 저장된 함수(객체 값)을 가리키는 식별자로서 동작하지만, 함수(실제 값)에 접근할 수 있게 해주는 ‘참조 값이 저장된’ `0x00000002`에 대한 식별자가 없기 때문에 함수를 호출하여 실제 함수에 접근할 수 없다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207483449-7a9bac7e-1589-4d87-acbc-4d1d51a87d19.png">

이러한 함수 선언문의 식별자 문제를 해결하기 위해 JS 엔진은 암묵적으로 함수 이름과 동일한 식별자를 암묵적으로 생성해서 함수의 호출과 참조 값을 통한 실제 값에 접근이 가능하도록 해준다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207483498-2ee120ce-7d09-4365-a07e-72034b24c8c5.png">

따라서 위 함수 선언문 예제에서는 함수 호출이 정상적으로 이루어지는 것이다

```javascript
function sayHello(name) {
  console.log(`Hello, ${name}`); 
}

sayHello("Jace"); // → Hello, Jace
```

#### 함수 표현식의 내부 동작

앞서 함수 표현식의 함수 리터럴은 JS 엔진에 의해 평가되어 값을 생성하기 때문에 생성된 함수(객체 값)를 변수에 할당하거나 피연산자로서 사용이 가능하다 했다. 다시 말해, 함수 표현식에서 함수 리터럴을 피연산자로서 기능하도록 의도적으로 명시할 수 있다는 의미다. 아래 예제에서는 함수 리터럴이 피연산자로서 기능하도록 명시적으로(의도적으로) 그룹 연산자로 감싸주었다

```javascript
(function greetings(name) {
  console.log(`Hello, ${name}`); 
}); 
```

그렇다면 함수 표현식을 통한 함수 생성의 내부 동작은 어떠할까? 먼저 리터럴과 값의 저장의 내용을 다시 복기해보자. [변수](), [메모리]()와 [표현식]()의 내용을 복기하자면 숫자 리터럴 `30`은 JS 엔진에 의해 평가되어 숫자 값 `30`을 생성한다. 그리고 생성된 값은 메모리에 저장된다(만약 생성된 숫자 값 `30`을 재사용해야 한다면 변수에 할당에 변수 식별자를 생성해야 한다). 이처럼 숫자 리터럴 `30`은 평가되어 값을 생성하기 때문에 표현식(리터럴 표현식)이기도 하다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207484456-1d2f5806-fc3f-4d16-98b2-7163f5c89779.png">

함수 리터럴은 객체 값(참조 값과 실제 값의 저장)인 함수를 생성한다는 것 외에는 **위 숫자 리터럴 예제와 동일**하다

그룹 연산자로 감싸진 함수 리터럴은 평가되어 `greetings`라는 함수 이름을 가진 함수(객체 값)을 생성하기 때문에 표현식(함수 리터럴 표현식)이기도 하며 생성된 값은 메모리에 저장된다. 즉, 그룹 연산자의 피연산자로서 `greetings` 함수는 JS 엔진에 의해 ‘표현식’으로서 인식되도록 의도된 것이다. 함수 선언문에서 JS 엔진에 의해 암묵적으로 식별자가 생성되는 것과는 달리, 함수 표현식에서는 식별자가 생성되지 않는다. 따라서 함수 표현식에서는 함수 이름 `greetings`로 함수 호출 시 에러가 발생하는 것이다

```javascript
(function greetings(name) {
  console.log(`Hello, ${name}`); 
}); 

greetings("Jace"); // → ReferenceError: greetings is not defined
```

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207484697-79c673b3-0dc3-4890-bef3-10cc85644704.png">

다른 함수 표현식의 예제도 살펴보자. 아래 예제는 함수 리터럴이 평가되어 함수 이름이 `greetings`인 함수(객체 값)를 생성하고 그 값을 변수 `msg`에 할당하는 표현식인 문이다. 다시 말해, 함수 표현식을 변수에 할당하도록 명시했다 

```javascript
var msg = function greetings(name) {
   console.log(`Hello, ${name}`);
}

// 1단계 
var msg; 

// 2단계
msg = function greetings(name) {
   console.log(`Hello, ${name}`);
}

// 함수 호출
greetings('Jace'); // → ReferenceError: greetings is not defined
msg('Jace'); // → Hello, Jace
```

함수 이름인 `greetings`로 함수 호출을 했을 때 함수 이름은 함수 몸체 내부에서만 유효한 식별자이기 때문에 에러가 난다. 하지만 위 그룹 연산자 함수 예제와는 달리, 함수 리터럴이 평가되어 생성된 함수(객체 값)를 변수 `msg`라는 식별자를 통해 참조 값과 실제 값에 접근할 수 있게 된다. 따라서, 실제 값인 함수에 접근하기 위해서는 식별자 `msg`로 함수 호출을 하면 된다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207492846-0b992aeb-7942-4819-9238-0c18d7415b69.png">

함수 표현식은 함수 자신을 변수에 할당이 가능하기 때문에 [`setTimeout()`](), [`Array.prototype.map()`]() 등 각종 [메서드]()와 [배열 고차함수]()에서 사용되는 [콜백 함수]()에 자주 활용된다

### 함수 생성 시점과 호이스팅

JS는 [인터프리터 언어]()이기 때문에 JS에서의 소스코드는 한 줄씩 순차적으로 실행된다. 아래 예제를 살펴보면 함수 참조와 함수 호출이 함수 선언문과 함수 표현식보다 위에 위치한다. 함수 선언문으로 생성된 함수는 정상적으로 참조와 호출이 가능한 것에 반해 함수 표현식으로 생성된 함수의 참조와 호출은 에러가 발생한다. 이는 함수 선언문으로 정의한 함수와 함수 표현식으로 정의한 함수의 생성 시점은 다르기 때문이다

```javascript
// 함수 참조
console.log(sayHello); // → f sayHello(name)
console.log(msg); // → undefined

// 함수 호출
sayHello("Jace"); // → Hello, Jace
msg("Jace"); // → TypeError: msg is not a function

// 함수 선언문 
function sayHello(name) {
  console.log(`Hello, ${name}`);
}

var msg = function(name) {
  console.log(`Hello, ${name}`); 
};
```

모든 선언문(`var`, `let`, `const`, `function`, `class` 등을 사용한 식별자의 선언)과 동일하게, 함수 선언문도 코드가 한 줄씩 순차적으로 실행되는 시점인 런타임 이전에 JS 엔진에 의해 먼저 실행되는 호이스팅이 이뤄지며 함수 객체가 미리 생성된다. 즉, 런타임 이전에 이미 함수(객체 값)가 생성되어 있고 함수 이름과 동일한 식별자가 암묵적으로 생성되어 함수의 할당까지 모두 완료된 상태다. 따라서 함수 선언문 위에 위치한 함수 참조와 함수 호출이 정상적으로 실행되는 것이다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207493716-b4779dc4-e797-4789-9005-3cb7bdc2f35b.png">

여기서 헷갈리는 부분이 있다. 앞서 `var`, `let`, `const` 등을 사용한 모든 식별자의 선언도 호이스팅된다 하지 않았나? 그럼 위 함수 표현식 예제도 변수 선언문이기 때문에 호이스팅되어서 함수 참조와 함수 호출이 정상적으로 실행되어야 하는거 아닌가? `var` 키워드를 사용한 변수 선언문과 함수 `sayHello`의 함수 선언문은 런타임 이전에 호이스팅되어 식별자를 생성하는 점에서 동일하다. 그러나 [값의 할당은 런타임 이후에 실행]()된다. 위 예제의 함수 표현식은 JS 엔진에 의해 평가되어 값을 생성하고, 생성된 값을 변수 `msg`에 할당하는 것이다. 따라서 변수 선언문은 런타임 이전에 호이스팅되고 값은 ‘아직 정해지지 않은 상태’로서 `undefined`가 할당된다

<img width="100%" src="https://user-images.githubusercontent.com/92138751/207493905-e53fdc66-51a7-4c19-a056-883ea116fb9c.png">

이렇듯, 함수의 정의 방법에 따라 함수의 생성 시점과 호이스팅 여부가 달리하기 때문에 위 예제에서 함수 표현식의 함수 참조는 `undefined`를, 함수 호출은 함수가 아닌 `undefined`를 호출하는 것이기 때문에 `TypeError`가 발생한다. 따라서 함수 표현식으로 정의한 함수는 반드시 함수 표현식 이후에 참조 또는 호출해야 한다


<img width="100%" src="https://user-images.githubusercontent.com/92138751/207494028-cb08d76a-4d25-41c4-8158-f12f918c0539.png">

<br>

## Function 생성자 함수

보통 객체는 [객체 리터럴]()을 통해 생성한다. 그러나 객체 리터럴 이외에 다양한 방법으로 객체를 생성할 수도 있다.   그 중, [생성자 함수]()를 통해 함수 객체를 생성할 수 있다. `Function` 생성자 함수는 JS에서 기본 제공하는 [빌트인 함수]()로서, 매개변수와 함수 몸체를 문자열 형태로 전달하면서 [`new` 연산자]()와 함께 호출하면 함수 객체를 생성한다

> 생성자 함수에 대해 더 자세한 내용은 [생성자 함수에 의한 객체 생성]() 파트를 참고하자

```javascript
// 함수 표현식(으로 평가되는 함수 리터럴)을 통한 sum 함수의 생성
var sum = function (x, y) {
  return x + y; 
}

// Function 생성자 함수를 통한 sum 함수의 생성
var sum = new Function("x", "y", "return x +y"); 

sum(2, 7); // → 7
```

`Function` 생성자 함수로 함수를 생성하는 방식은 일반적이지 않고 바람직하지도 않다. `Function` 생성자 함수로 생성한 함수는 [클로저(Closure)]()를 생성하지 않는 등, 함수 선언문이나 함수 표현식을 통해 생성된 함수와는 다르게 동작한다

```javascript
// 함수 표현식을 통한 sum 함수의 생성 
var sum = ( function() {
  var z = 10; 
  return function(x, y) {
    return x + y + z; 
  };
}()); 

sum(1, 2); // → 13

// Function 생성자 함수를 통한 sum 함수의 생성
var sum = ( function() {
  var z = 10; 
  return new Function("x", "y", "return x + y + z"); 
}()); 

sum(1, 2); // → ReferenceError: z is not defined
```

클로저는 아직 살펴보지 않은 개념이므로, 지금은 `Function` 생성자 함수로 생성한 함수와 함수 선언문/함수 표현식으로 생성한 함수가 동일하게 동작하지 않는다는 것에만 주목하자

<br>

## 화살표 함수 

화살표 함수는 ES6(2015)부터 도입된 기능으로, `function` 키워드 대신 화살표(Fat Arrow, `=>`)를 사용해 함수 선언을 간소화할 수 있다. 화살표 함수는 항상 익명 함수를 사용해야 한다는 점을 기억하자 

```javascript
// 함수 표현식(으로 평가되는 함수 리터럴)을 통한 sum 함수의 생성
var sum = function(x, y) {
  return x + y; 
}

// 화살표 함수를 통한 sum 함수의 생성 
var sum = (x, y) => x + y; 

sum(2, 5); // → 7
```

화살표 함수는 함수 선언문, 함수 표현식을 완전히 대체하는 기능으로 디자인된 것이 아니다. 화살표 함수는 기존 함수 정의 방법보다 표현만 간소화한 것이 아니라 내부 동작 또한 간소화되어 있다. 

화살표 함수는 생성자 함수로 사용할 수 없으며, 기존 함수와 `this` 바인딩 방식이 다르고,`prototype` 프로퍼티가 없으며 `arguments` 객체를 생성하지 않는다. 지금 위 내용들이 무슨 의미인지 이해가 되지 않을 것이다. 추후 `this`, `prototype`, `arguments` 객체를 살펴본 후 화살표 함수에 대한 내용을 추가할 예정이다

<br>

## 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [함수 선언문에 대해 알고 싶나](https://basemenks.tistory.com/37)
- [매개변수와 인자의 차이점은 무엇일까](https://7942yongdae.tistory.com/155)
- [드림코딩 - 자바스크립트 함수 기본편](https://www.youtube.com/watch?v=PuG2VW18O1E)
- [드림코딩 - 자바스크립트 함수 정의, 호출, 그리고 콜백 함수](https://www.youtube.com/watch?v=-cAPq25P-68)
- [코딩앙마 - 함수의 기초](https://www.youtube.com/watch?v=HQGkiD-dXFI)
- [함수란 무엇일까](https://hanamon.kr/javascript-%ED%95%A8%EC%88%98-%EA%B8%B0%EC%B4%88/)
- [자바스크립트 함수 이해하기](https://bigtop.tistory.com/34)
- [함수에 대해 알아보자](https://velog.io/@surim014/%EC%9B%B9%EC%9D%84-%EC%9B%80%EC%A7%81%EC%9D%B4%EB%8A%94-%EA%B7%BC%EC%9C%A1-JavaScript%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-part-6-Function)
- [자바스크립트 익명 함수의 구조와 간단한 예제](https://dasima.xyz/javascript-anonymous-function/)

