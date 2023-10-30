# 배열이란

### 목차 

* [1 배열과 배열의 생성](#1-배열과-배열의-생성)
* [2 인덱스와 배열 길이](#2-인덱스와-배열-길이)
  * [2-1 인덱스](#2-1-인덱스)
  * [2-2 배열 요소의 동적 추가 및 삭제](#2-2-배열-요소의-동적-추가-및-삭제)
  * [2-3 배열 길이](#2-3-배열-길이)
* [3 배열과 일반 객체](#3-배열과-일반-객체)


***

<br>

## 1 배열과 배열의 생성

JS에서 배열(Array)은 객체 타입으로 여러 개의 값을 순서대로 나열한 자료구조다. 배열이 가지고 있는 값을 요소(Element)라고 부르며, JS의 모든 타입의 값([원시 값](www.naver.com)과 [객체 값](https://velog.io/@jacenam/%EA%B0%9D%EC%B2%B4-%ED%83%80%EC%9E%85))은 배열의 요소가 될 수 있다. 다시 말해, 배열은 JS의 모든 타입을 담을 수 있는 자료구조라는 것이다 

배열은 배열 [리터럴](www.naver.com)(`[ ]`), `Array` [생성자 함수](www.naver.com), `Array.of`, `Array.from` [메서드](www.naver.com)로 생성할 수 있다. 일반적으로 배열 리터럴을 통해 배열을 생성한다. `"a"`, `"b"`, `"c"`는 배열 `arr`의 요소다

```javascript
// 배열 리터럴
const arr = ["a", "b", "c"];  // 배열(Array, arr)

// 배열에는 모든 타입의 값이 담길 수 있다 
const arr = [ // 배열(Array, arr) 
  "apple", 
  10, 
  NaN, 
  undefined,
  [ ], // 배열
  { }, // 객체
  function () {} // 함수
]; 
```


앞서 배열에는 JS의 모든 타입의 값이 배열의 요소가 될 수 있다 했다. 따라서 `Array` 생성자 함수의 매개변수에는 모든 타입의 값이 인수로 전달될 수 있으나, 하나의 숫자 타입만이 인수로 전달되면 배열의 요소로 추가되는 것이 아닌 (바로 뒤에 나올) '배열의 길이'가 추가된다. 숫자 타입 여러 개가 매개변수로 전달될 경우, 배열의 요소로서 추가된다

```javascript
// Array 생성자 함수 
const arr1 = new Array("a", "b", "c");

console.log(arr1); // → ["a", "b", "c"]

// 숫자 타입 하나만(단일) Array 생성자 함수의 매개변수로 전달될 경우
const arr2 = new Array(2); 

console.log(arr2.length); // → 2
console.log(arr2); // → [<2 empty items>]

// 숫자 타입 여러 개(복수)가 Array 생성자 함수의 매개변수로 전달될 경우 
const arr3 = new Array(4, 5, 6); 

console.log(arr3); // → [1, 2, 3]
```

<br>

## 2 인덱스와 배열 길이
### 2-1 인덱스

배열의 요소는 인덱스(Index, 색인)를 통해 배열에서 각 요소의 위치를 나타낼 수 있다. 인덱스는 배열의 요소에 접근할 때 사용되며, 인덱스는 `0`부터 시작한다. 요소에 접근할 때는 대괄호 표기법(`[]`) 내에 요소의 인덱스를 지정한다

```javascript
arr[index]; // 0부터 정수 숫자 1, 2, 3...를 인덱스로 지정

const fruitArr = ["apple", "grape", "pear"]; 

// "apple" => 0
// "grape" => 1
// "pear" => 2 

console.log(arrFruit[0]); // → apple
console.log(arrFruit[1]); // → grape
console.log(arrFruit[2]); // → pear
console.log(arrFruit[3]); // → undefined (인덱스 3의 위치에는 요소가 없다)
```

인덱스로 지정한 숫자에 따라 그 위치에 해당하는 배열의 요소를 타입 그대로 반환한다

```javascript
const mixedArr = ["apple", 456, undefined, function () {}, {}];

console.log(mixedArr[0], typeof mixedArr[0]); // → apple string
console.log(mixedArr[1], typeof mixedArr[1]); // → 456 number
console.log(mixedArr[2], typeof mixedArr[2]); // → undefined undefined
console.log(mixedArr[3], typeof mixedArr[3]); // → f() {} function
console.log(mixedArr[4], typeof mixedArr[4]); // → {} object
```

일반 객체 타입에서 [프로퍼티 접근]()의 내용을 복기하자면, 객체의 프로퍼티에 접근하기 위해서 마침표 프로퍼티 접근 연산자와 대괄호 프로퍼티 접근 연산자 두 가지 방법이 있었다. 이 중 대괄호 연산자를 통해 프로퍼티에 접근하는 방법이 배열의 인덱스와 유사하다

> 일반 객체 프로퍼티에 접근하기 위한 대괄호 연산자 내에 문자열로 이루어진 프로퍼티 키를 지정해야 한다고 하지 않았나? 문자열이 아닌 타입으로 프로퍼티 키를 지정할 시, 문자열로 암묵적 타입 변환이 가능한 타입이라면 사용이 가능하다(e.g. `fruitObj[1]` => `fruitObj["1"]`). 이에 대해서는 [객체 타입]()을 참고하자

```javascript
obj["key"]; // 프로퍼티 키에 해당하는 문자열을 지정

const fruitObj = {
  1: "apple", 
  2: "grape", 
  3: "pear" 
}; 

//마침표 프로퍼티 접근 연산자 
console.log(fruitObj[1]); // → apple
```


### 2-2 배열 요소의 동적 추가 및 삭제

일반 [객체에서 프로퍼티를 동적으로 추가, 수정 및 삭제]()할 수 있었다.  아래 일반 객체의 예제 코드를 살펴보자. 변수 `obj`에는 `residence`로 지정된 프로퍼티 키가 없다. 그러나 마침표 프로퍼티 접근 연산자를 통해 존재하지 않는 `residence` 프로퍼티 키에 `"Seoul" `이라는 값을 할당했다 

```javascript
// 일반 객체에서 프로퍼티를 동적으로 추가, 갱신 및 삭제
const obj = {
  name: "Jace", 
  age: 30
}; 


obj.residence = "Seoul"; // 프로퍼티 추가
obj["age"] = 31; // 프로퍼티 수정(갱신) 
delete obj["age"]; // 프로퍼티 삭제  

console.log(obj); // → { name: "Jace", residence: "Seoul" }
```

배열에서도 일반 객체의 프로퍼티 변경처럼 요소를 동적으로 추가, 수정 및 삭제할 수도 있다. 아래 예제 코드의 배열은 요소가 3개, 즉 배열의 길이도 `3`이다(인덱스는 `arr[2]`다). 존재하지 않는 인덱스 `arr[3]`에 특정 값을 할당하면, `arr[3]`에 해당하는 위치에 할당한 값이 새로운 요소로 추가된다. 또한, 존재하는 인덱스에 값을 할당하면 인덱스에 해당하는 할당한 값으로 대체된다

```javascript
const arr = [0, 1, 2]; 
console.log(arr[2]); // → 2

// 배열에 동적으로 요소를 추가
arr[3] = 3; 
console.log(arr); // → [0, 1, 2, 3]

// 배열의 기존 요소를 갱신(수정)
arr[2] = 4; 
console.log(arr); // → [0, 1, 4, 3]
```

배열의 특정 요소를 지우기 위해서는 `delete` 연산자를 사용할 수 있다. 여기서 주의해야 할 부분이 있다. 앞서 배열은 객체 타입과 유사한 구조를 가지고 있다 했다.  `delete` 연산자는 객체의 프로퍼티를 삭제한다. 따라서  `delete arr[0]`은 `arr`에서 인덱스가 `0`인, 즉 프로퍼티 키가 `0`인 프로퍼티를 삭제하는 것이다. 배열에서 프로퍼티 키에 해당하는 인덱스와 인덱스가 가리키는 값이 삭제되는 것이다. 따라서 `arr`는 인덱스 `[0]`의 자리가 비어있는 [희소 배열]()이 된다. 희소 배열을 만들지 않으면서 배열의 요소를 삭제하려면 `Array.prototype.splice` 메서드를 사용해야 한다. 이에 대해서는 [배열 요소의 관리](https://velog.io/@jacenam/%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B4%80%EB%A6%AC)를 참고하자

```javascript
const arr = [0, 1, 2]; 

delete arr[0]; 
console.log(arr); // → [empty, 1, 2]
```

인덱스는 요소의 위치를 `0` 이상의 정수 형태로 나타내기 때문에 `0`과 양의 정수, 그리고 정수 형태의 문자열만 사용할 수 있다. 만약 정수 이외의 값을 인덱스로 사용하고 거기에 값을 할당하면 배열의 요소가 생성되는 것이 아니라 프로퍼티(프로퍼티 키와 값)가 생성된다. 이때 추가된 프로퍼티는 `length` 프로퍼티 값(배열 길이)에 영향을 주지 않는다

```javascript
const arr = [1, 2, 3];
console.log(arr.length); // → 3

arr["item3"] = "user3"; 
arr["item1"] = "user1"; 
arr[1.7] = 8; 
arr[-1] = "Jace"; 

console.log(arr); // → [1, 2, 3, item3: 'user3', item1: 'user1', 1.7: 8, -1: 'Jace']

// 배열 길이는 여전히 3이다
console.log(arr.length); // → 3
```

### 2-3 배열 길이

배열은 요소의 개수에 따라 배열의 길이가 정해지며, 이를 `length` 프로퍼티로 나타낼 수 있다. `length` 프로퍼티는 배열의 길이를 숫자 타입으로 반환한다

```javascript
arr.length; // 배열의 길이를 숫자 타입으로 반환한다

const fruitArr = ["apple", "grape", "pear"]; 
console.log(fruitArr.length, typeof fruitArr.length); // → 3 number

const nameArr = ["Jace", "Ju Hyung"]; 
console.log(nameArr.length, typeof nameArr.length); // → 2 number

const emptyArr = []; 
console.log(emptyArr.length, typeof emtpyArr.length); // → 0 number
```

자세히 살펴보면 배열 요소의 위치를 나타내는 인덱스는 `0`에서 시작한다. 반면, 배열의 길이를 나타내는 `length` 프로퍼티는 요소가 있을 시(없을 시에는 `0`) `1`로 시작한다. 따라서 배열의 첫 번째 요소는 보통  `0`을 인덱스로 지정하지만, 배열 내 마지막 요소가 몇 번째 인덱스에 위치하는지 모르거나 직관적으로 알 수 없다면 배열의 길이, 즉 요소의 개수를 나타내는 배열 길이에서 `1`을 빼면 마지막 요소의 인덱스를 알 수 있다. 앞서 `length` 프로퍼티는 배열의 길이를 숫자 타입으로 반환한다 했다. 따라서 배열의 길이에서 `2`, `3`...을 빼면, 배열의 마지막 요소뿐만 아니라 마지막에서 2번 째, 3번 째 등 뒷 부분의 위치한 요소까지도 바로 확인할 수 있다

```javascript
const arrNum = [1, 5, 6, 8, 10, 123, 34, 56, 98, 541, 1203, 2]; // 마지막 요소가 몇 번쨰 인덱스인지 일일히 확인할 필요가 있을까?

console.log(arrNum[0]); // → 1 (배열의 첫 번째 요소가 위치한 자리는 항상 0이다)
console.log(arrNum[3]); // → 8
console.log(arrNum[arrNum.length - 1]); // → 2
```

`length` 프로퍼티의 값은 배열에 요소를 추가하거나 삭제하면 자동적으로 갱신된다 

> `push()`, `pop()` 메서드에 대한 내용은 [배열 요소의 관리]()를 참고하자

```javascript
const numArr = [1, 2, 3, 4, 5]; 
console.log(numArr.length); // → 5 

// push() 메서드를 통해 입력된 인수는 요소의 맨 뒤에  추가된다
numArr.push(1, "apple"); 
console.log(numArr, numArr.length); // → [1, 2, 3, 4, 5, 1, "apple"] 7

// pop() 메서드를 통해 맨 뒤의 요소가 하나 삭제된다
numArr.pop(); 
console.log(numArr, numArr.length); // → [1, 2, 3, 4, 5, 1] 6
```

`length` 프로퍼티의 값에 임의적으로 특정 숫자 값을 할당할 수도 있으며, 현재의 `length` 프로퍼티 값보다 작은 숫자 값을 할당하면 배열의 길이가 줄어든다

```javascript
const numArr = [1, 2, 3, 4, 5]; 

// 현재 변수 numArr의 length 프로퍼티는 5다.
console.log(numArr.length); // → 5 

// 만약 numArr.length의 값보다 작은 값을 할당하는 경우 배열의 길이는 줄어든다
numArr.length = 3; 
console.log(numArr); // → [1, 2, 3]
 
```

반대로, 현재의 `length` 프로퍼티 값보다 큰 숫자 값을 할당하면 어떻게 될까? 이때는 `length` 프로퍼티 값은 변경된다(`5` → `8`). 다시 말해, 배열의 길이는 늘어나지만 배열의 요소는 변하지 않는다. `<3 empty items>`는 실제로 추가된 배열의 요소가 아니기 때문에 인덱스로 참조해도 `undefined`가 반환된다. 따라서 값 없이 비어있는 요소를 위해 메모리 공간을 따로 확보하지도 않는다 

```Javascript
const numArr = [1, 2, 3, 4, 5]; 

// 현재 변수 numArr의 length 프로퍼티는 5다.
console.log(numArr.length); // → 5

// 만약 numArr.length의 값보다 큰 값을 할당하는 경우 배열의 길이는 늘지만 요소는 추가되지 않는다 
numArr.length = 8;
console.log(numArr.length); // → 8
console.log(numArr); //  → [1, 2, 3, 4, 5, <3 empty items>]
console.log(numArr[5], numArr[6], numArr[7]); // → undefined undefined undefined
```

배열은 요소의 위치는 인덱스를 통해, 배열의 길이는 `length` 프로퍼티를 통해 알 수 있기 때문에 두 가지 특성과 반복문을 활용해 배열의 값 전체에 접근할 수 있다

> 배열 요소의 위치를 나타내는 인덱스 `i`는 배열의 길이 `fruitArr.length`의 값보다 항상 작다

```javascript
const fruitArr = ["apple", "grape", "pear"]; 

for (let i = 0; i < fruitArr.length; i++ ) {
  console.log(fruitArr[i]); // → apple grape pear
}
```

위와 같은 반복문을 활용해 특정 조건에 부합하는 배열의 요소에만 접근해 반환할 수도 있다 

> `switch`문에 대해서는 [제어문의 종류]()를 참고해보자

```javascript
// 배열 내에서 두 번째, 네 번째, 다섯 번째 과일만 출력
const fruitArr = ["apple", "grape", "pear", "banana", "watermelon", "pineapple"];

for (let i = 0; i < fruitArr.length; i++) {
  switch (i) { 
    case 1: case 3: case 4: console.log(fruitArr[i]); // grape banana watermelon
  }
}
```


<br>

## 3 배열과 일반 객체

배열의 요소와 길이에 대해 살펴보았다. 배열의 구조를 살펴보았을 때 배열은 일반 객체와 구별되는 특징은 값의 순서를 나타내는 `index`와 값의 길이를 나타내는 `length` 프로퍼티다

| 구분            | 객체                      | 배열                      |
| --------------- | :------------------------ | ------------------------- |
| 구조            | 프로퍼티 키와 프로퍼티 값 | 인덱스와 요소             |
| 값의 참조(접근) | 프로퍼티 키               | 인덱스                    |
| 값의 순서       | **X**                     | **O**                     |
| 값의 길이       | **X**                     | **O** (`length` 프로퍼티) |

다시 말해, 값(요소)의 순서가 지정되어 있다는 것과 값의 길이라는 특징을 제외하고 배열은 객체와 구조가 동일하다. 이는 배열이 객체 타입이기 때문이다. 일반 객체에서는 문자열 혹은 심벌 타입으로 이루어진 프로퍼티 키를 통해 프로퍼티 값에 접근할 수 있다. 배열의 요소는 프로퍼티 값이며, 인덱스는 사실 프로퍼티 키로서 배열의 프로퍼티 값에 접근할 수 있게 해준다 

```javascript
// 일반 객체
const obj = {
  name: "Jace", 
  age: 30, 
  residence: "Seoul, South Korea"
}; 

console.log(obj["name"]); // → Jace

// 배열 
const arr = [
  "Jace", 
  30, 
  "Seoul, South Korea"
]; 

console.log(arr[1]); // → Jace
```

"어? 근데 일반 객체의 프로퍼티 키는 문자열 혹은 심벌 타입만 가능한거 아니야? 그럼 배열의 인덱스(프로퍼티 키)도 숫자 타입이 아닌 문자열 혹은 심벌 타입이어야 하는거 아니야?"라고 의문이 들 수도 있다. 배열의 요소(프로퍼티 값)를 참조하기 위한 인덱스는 숫자 타입처럼 보이는 것이지, 사실은 일반 객체와 같이 문자열로 이루어져 있다

> `Object.getOwnPropertyDescriptors()`에 대해서는 [프로퍼티 어트리뷰트](www.naver.com)와 [프로퍼티 디스크립터](www.naver.com)를 참고하자. 그러나 지금 당장 이 부분에 대해서 명확히 이해할 필요는 없다. **JS에서 배열은 객체 타입이기 때문에 전체적인 구조와 프로퍼티 키, 프로퍼티 값의 구성도 동일하다는 것만 알고 넘어가자**

```javascript
console.log(Object.getOwnPropertyDescriptors([1, 2, 3])); 

/* → 
{
  '0': { value: 1, writable: true, enumerable: true, configurable: true },
  '1': { value: 2, writable: true, enumerable: true, configurable: true },
  '2': { value: 3, writable: true, enumerable: true, configurable: true },
  length: { value: 3, writable: true, enumerable: false, configurable: false }
} */
```

<br>

***
### 참고 

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [JavaScript MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [JavaScript에서는 배열(Array)도 객체(Object)다](https://preamtree.tistory.com/115)
- [배열의 요소를 추가하는 방법](https://developer-talk.tistory.com/154)
- [JavaScript Array Method 정리](http://blog.302chanwoo.com/2017/08/javascript-array-method/)
- [JS의 배열 내장함수들](https://velog.io/@won-developer/JS%EC%9D%98-%EB%B0%B0%EC%97%B4-%EB%82%B4%EC%9E%A5%ED%95%A8%EC%88%98%EB%93%A4)