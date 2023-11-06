# 배열 요소의 관리

**Table of Contents**

- 

<br>

- **배열 메서드**

  - `Array.of`
  - `Array.from` 
  - `Array.isArray`
  - `Array.prototype.indexOf`
  - `Array.prototype.lastIndexOf`
  - `Array.prototype.includes`
  - `Array.prototype.push`
  - `Array.prototype.pop`
  - `Array.prototype.unshift`
  - `Array.prototype.shift`
  - `Array.prototype.concat`
  - `Array.prototype.splice`
  - `Array.prototype.slice`
  - `Array.prototype.join`
  - `Array.prototype.reverse`
  - `Array.prototype.fill`
  - `Array.prototype.includes`
  - `Array.prototype.flat`

- **배열 고차 함수**
  - `Array.prototype.sort`
  - `Array.prototype.forEach`
  - `Array.prototype.map`
  - `Array.prototype.filter`
  - `Array.prototype.reduce`
    ```javascript
    const array = [1, 2, 3, 4, 5];
    
    const sum = array.reduce((acc, cur, idx, arr) => {
      console.log(`누산: ${acc}`);
      console.log(`현재: ${cur}`);
      console.log(`콜백 순회: ${idx}`);
      console.log(arr);
      return acc + cur;
    }, 0);
    
    console.log("--------");
    console.log(`배열의 각 요소의 합: ${sum}`);
    
    0회차 콜백
    acc: 0;
    cur: 1000000001;
    idx: 0;
    
    1회차 콜백
    acc: 1000000001
    cur: 1000000002
    idx: 1
    
    2회차 콜백
    acc: 2000000003
    cur: 1000000003
    idx: 2
    
    3회차 콜백
    acc: 3000000006
    cur: 1000000004
    idx: 3
    
    4회차 콜백
    acc: 4000000010
    cur: 1000000005
    idx: 4
    
    acc + cur: 5000000015
    ```
  - `Array.prototype.some`
  - `Array.prototype.every`
  - `Array.prototype.find`
  - `Array.prototype.findIndex`
  - `Array.prototype.flatMap`

<br>

## 참고

- [모던 자바스크립트 Deep Dive](http://www.yes24.com/Product/Goods/92742567)
- [JavaScript MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript)
- [배열 제대로 알고 쓰자. 자바스크립트 배열 개념과 APIs 총정리](https://youtu.be/yOdAVDuHUKQ)
- [[JavaScript] 배열의 요소를 추가하는 방법](https://developer-talk.tistory.com/154)
- [자바스크립트 입문 09. 배열 내장 함수](https://learnjs.vlpt.us/basics/09-array-functions.html)
- [자바스크립트 reduce 메서드 이해하기](https://bigtop.tistory.com/69)
- [배열 함수 reduce [JavaScript]](https://velog.io/@loocia1910/%EB%B0%B0%EC%97%B4-reduce-%ED%95%A8%EC%88%98-JavaScript)