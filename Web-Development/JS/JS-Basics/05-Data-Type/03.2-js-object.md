# JS 객체의 관리 방식

**Table of Contents**

- [JS의 객체](#JS의-객체)

<br>

## JS의 객체

**참고사항: 자바스크립트 객체의 관리 방식**

자바스크립토 객체는 프로퍼티 키를 인덱스로 사용하는 해시 테이블 hash table(해시 테이블 은 연관 배열 associalive array, map, dictionary, lookup table이라 부르기도 한다)이라고 생각할 수 있다. 대부분의 자바스크립트 엔진은 해시 테이블과 유사하지만 높은 성능을 위해 일반적인 해시 테이블보다 나은 방법으로 객체를 구현한다

<img src="https://ifh.cc/g/q7TanP.jpg" style="max-width: 100%" align="center">

자바, C++ 같은 클래스 기반 객체지향 프로그래밍 언어는 사전에 정의된 클래스를 기반으로 객체(인스턴스)를 생성한다. 다시 말해 객체를 생성하기 이전에 이미 프로퍼티와 메서드가 정해져 있으며 그대로 객체를 생성한다 객체가 생성된 이후에는 프로퍼티를 삭제하거나 추가할 수 없다. 하지만 자바스크립트는 클래스 없이 객체를 생성할 수 있으며 객체가 생성된 이후라도 동적으로 프로퍼티와 메서드를 추가할 수 있다. 이는 사용하기 매우 편리하지만 성능 면에서는 이론적으로 클래스 기반 객체지향 프로그래밍 언어의 객체보다 생성과 프로퍼티 접근에 비용이 더 많이 드는 비효율적인 방식이다. 따라서 V8 자바스크립트 엔진에서는 프로퍼티에 접근하기 위해 동적 탐색 dynamic lookup 대신 히든 클래스 hidden class 라는 방식을 사용해 C++ 객체의 프로퍼티에 접근하는 정도의 성능을 보장한다. 히든 클래스는 자바와 같이 고정된 객체 레이아웃(클래스)과 유사하게 동작한다. 크롬 V8 자바스크립트 엔진이 객체를 어떻게 관리하는지에 대해 관심이 있다면 아래의 글을 참고하기 바란다.

## 참고

- [Fast properties in V8](https://v8.dev/blog/fast-properties)
- [V8 히든 클래스 이야기](https://engineering.linecorp.com/ko/blog/v8-hidden-class)
- [자바스크립트 엔진의 최적화 기법(2)- Hidden class, lnline Caching](https://meetup.nhncloud.com/posts/78)
- [How the VS engine works](http://thibaultlaurens.github.io/javascript/2013/04/29/how-the-v8-engine-works)
- [How JavaScript works: inside the V8 engine + 5 tips on how to write optimized code](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)
- [Breaking the JavaScript Speed Limit with V8](https://www.youtube.com/watch?v=UJPdhx5zTaw)