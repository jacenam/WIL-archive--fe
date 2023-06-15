# 프로퍼티 어트리뷰트

프로퍼티 어트리뷰트(Property Attritube)는 단어 의미 그대로 "[객체 프로퍼티](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Data%20Type/object%20type.md#3-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C)가 가지는 속성"이라는 뜻으로, 객체의 프로퍼티가 생성될 때 프로퍼티의 상태를 나타내는 속성 값이라 이해하면 된다. 프로퍼티 어트리뷰트 관련 상세 내용을 살펴보자

### 목차

- [1 내부 슬롯과 내부 메서드](#1-내부-슬롯과-내부-메서드)

<br>

## 1 내부 슬롯과 내부 메서드

프로퍼티 어트리뷰트를 이해하기 앞서 먼저 내부 슬롯(Internal Slot)과 내부 메서드(Internal Method)의 개념에 대해 살펴보자

JS에서는 객체가 생성될 때 JS 엔진 내부의 구현 알고리즘이 동작하여 객체와 프로퍼티를 생성하게 된다. 이러한 JS 엔진의 객체 생성 동작에 대한 설명을 인간이 이해할 수 있는 언어인 '의사(Pseudo)'로 [ECMAScript]()에 정의한 내용이 바로 내부 슬롯과 내부 메서드다

> 의사(Pseudo)란 컴퓨터 프로그램이나 알고리즘이 수행해야 할 명령과 내용들을 인간이 사용하는 언어로 간략히 서술하는 것을 의미한다. 의사 코드에 대한 상세 내용은 [여기](https://medium.com/djangogirlsseoul-codecamp/%EC%9D%98%EC%82%AC%EC%BD%94%EB%93%9C-pseudo-code-%EB%9E%80-d892a3479b1d)를 참고하자

ECMAScript에서는 내부 슬롯과 내부 메서드를 가리키기 위해 내부 슬롯과 내부 메서드의 이름들을 이중 대괄호(`[[...]]`)로 감싸는 형태로 표현되어 있다

```javascript
// 예제
[[getPrototypeOf]]
[[SetPrototypeOf]]
[[IsExtensible]]
...
```

