# 실행 컨텍스트 스택과 렉시컬 환경

**Table of Contents**

- [실행 컨텍스트 스택](#실행-컨텍스트-스택)
- [렉시컬 환경](#렉시컬-환경)

<br>

## 실행 컨텍스트 스택

JS 엔진은 한 줄씩 코드를 순차적으로 실행하며 [소스코드의 종류](#1-1-소스코드의-종류)에 따라 실행 컨텍스트를 생성한다. 생성된 실행 컨텍스트는 생성된 순서에 따라 쌓이는 형태의 [스택(Stack) 자료구조]()를 통해 관리되는데,  이를 실행 컨텍스트 스택이라 부르며 스택은 후입선출(LIFO, Last-in-First-out)의 순서를 가진다

아래 예제를 살펴보면 JS 엔진은 먼저 런타임 이전에 전역 코드를 평가하여 전역 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 추가(Push)된다. 그리고 런타임에는 전역 코드를 실행한다. 그러다가 함수 호출문에 도달하면 함수가 호출되어 함수 코드를 평가하여 함수 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 추가된다. 이런식으로 먼저 스택에 쌓인 실행 컨텍스트 이후에 다른 실행 컨텍스트가 쌓이게되면, 나중에 쌓인 실행 컨텍스트부터 처리하여 처리 완료된 실행 컨텍스트는 제거(Pop)되는 구조라고 이해하면 된다

```javascript
const x = 1;

function outerFunc() {
  const y = 2; 
  
  function innerFunc() {
    const z = 3;
    console.log(x + y + z);
  }
  innerFunc();
}

foo(); // → 6
```

실행 컨텍스트 스택의 최상위에 쌓인 실행 컨텍스트는 언제나 현재 실행되어 처리되는 코드(전역, 함수, `eval`, 모듈)의 실행 컨텍스트라는 것을 알 수 있다(실행 컨텍스트 스택 최상위 = 현재 실행되는 코드)

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e9d56636-5a55-46cf-83c4-a3e7a7796307" width="100%">

위 예제를 단계별로 살펴보자

1. **전역 코드의 평가 후 실행**

   - 전역 코드의 평가 시 `x` 전역 변수, `outerFunc` 전역 함수가 호이스팅되고 전역 실행 컨텍스트가 생성되어 식별자들을 등록한다. 그리고 전역 실행 컨텍스트는 실행 컨텍스트 스택에 추가된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/5ab0ce0f-20de-441e-8dfc-b654555e06ed" width="100%">

   - 전역 코드의 실행 시 `x` 변수에 값의 할당이 이뤄지고, `outerFunc` 함수가 호출된다. 함수 호출로 인해서 코드의 실행 흐름은 `outerFunc` 함수 내부로 변경되며 추후 코드 실행 순서의 복귀를 위해 함수의 호출 시점은 전역 실행 컨텍스트에 등록된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/f0ad353b-734e-4454-9c43-9fcccdf917d7" width="100%">

2. **`outerFunc` 함수 코드의 평가 후 실행**

   - `outerFunc` 함수의 내부 코드 평가 시 `y` 지역 변수와 `innerFunc` 중첩 함수가 호이스팅되고 함수 실행 컨텍스트가 생성되어 식별자들을 등록한다. 그리고 함수 실행 컨텍스트는 실행 컨텍스트 스택에 추가된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/aa3f073f-f48c-4e6c-bf1f-4bf9d8b32c19" width="100%">

   - `outerFunc` 함수 내부의 코드 실행 시 `y` 지역 변수에 값의 할당이 이뤄지고, `innerFunc` 중첩 함수가 호출된다. 함수 호출로 인해서 코드의 실행 흐름은 `innerFunc` 함수 내부로 변경되며 추후 코드 실행 순서의 복귀를 위해 함수의 호출 시점은 함수 실행 컨텍스트에 등록된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/d7138b2e-fa0d-4386-a135-c283d62fbeab" width="100%">

3. **`innerFunc`  함수 코드의 평가 후 실행**

   - `innerFunc` 함수 내부의 코드 평가 시 `z` 지역 변수가 호이스팅되고 `innerFunc` 함수를 위한 함수 실행 컨텍스트가 생성되어 `z` 지역 변수를 등록한다. 그리고 함수 실행 컨텍스트는 실행 컨텍스트 스택에 추가된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/4a6fd682-2794-4b37-ac61-5f177a957360" width='100%'>

   - `innerFunc` 함수 내부의 코드 실행 시 `z` 지역 변수에 값의 할당이 이뤄지고, `console.log` 메서드가 호출된다. `console.log` 메서드의 실행을 위해 JS 엔진은 지역 스코프에서부터 전역 스코프까지 `console` 프로퍼티를 탐색한다. 전역 객체 `window`의 프로퍼티인 `console` 프로퍼티 검색 후 `console.prototype`의 프로토타입 체인을 통해 `log` 메서드를 상속받아 호출한다. `x`, `y`, `z` 식별자는 각 실행 컨텍스트가 관리하는 스코프 체인을 통해 검색하여 각 식별자에 바인딩된 값을 참조한다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/176302dc-6b32-4fc0-8a04-896fca316d05" width="100%">

     그리고 `innerFunc` 함수 내부 코드가 모두 실행되면 실행 컨텍스트 스택에서 `innerFunc` 함수 실행 컨텍스트는 제거된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/417cf0d1-7d36-47bd-9a98-246e002c7908" widht="100%">

4. **`outerFunc` 함수 코드로의 복귀**

   - `innerFunc` 함수가 종료되면 코드의 실행 흐름은 `outerFunc` 함수로 이동한다. 그리고 `outerFunc` 함수도 더 이상 실행할 코드가 없기 때문에 종료된다. 그럼 JS 엔진은 `innerFunc` 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 제거(Pop)한다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/3c43e181-6cd9-4ae3-ac54-6545ef774720" width="100%">

5. **전역 코드로의 복귀**

   - `outerFunc` 함수가 종료되면 코드의 실행 흐름은 다시 전역 코드로 이동한다. 전역 코드도 더 이상 실행할 코드가 남아있지 않게되면 전역 실행 컨텍스트도 스택에서 제거되어 실행 컨텍스트 스택에는 아무것도 남아있지 않게 된다

     <img src="https://github.com/jacenam/WIL-archive/assets/92138751/95887044-80d4-4a16-9ad0-b7fc8c8ab94f" width="100%">

<br>

## 렉시컬 환경

앞서 실행 컨텍스트에 대해서 살펴봤듯이, 실행 컨텍스트는 식별자, 스코프 및 코드 실행 흐름(실행 컨텍스트 스택)의 등록과 관리를 위한 정보들로 구성되어 있다. 코드 실행 흐름은 실행 컨텍스트 스택을 통해 관리되는 반면 식별자와 스코프 정보는 렉시컬 환경(Lexical Environment)을 통해 관리된다

> 렉시컬(Lexical)의 사전적 의미는 (특정 언어의) 어휘를 의미한다. 즉 렉시컬 환경이란 JS에서 데이터의 구분 어휘라 할 수 있는 식별자 정보를 등록 및 관리하는 환경이라 이해할 수 있다

앞서 [실행 컨텍스트 스택](#2-실행-컨텍스트-스택) 파트에서 살펴본 예제를 살펴보자. 소스코드가 JS 엔진에 의해 평가되는 과정에서 소스코드 종류에 따라 실행 컨텍스트가 생성되고 실행 컨텍스트 스택과 식별자(변수, 함수 등) 정보가 실행 컨텍스트에 등록된다 했다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/5ab0ce0f-20de-441e-8dfc-b654555e06ed" width="100%">

그러나 실행 컨텍스트의 내부 구성을 세부적으로 살펴보면 사실은 아래 그림과 같다. 실행 컨텍스트는 `LexicalEnvironment`와 `VariableEnvironment`라는 컴포넌트들로 구성된다. 실행 컨텍스트가 생성되면 컴포넌트들은 하나의 동일한 렉시컬 환경을 참조한다(이후 몇 가지 상황에 달하면 `VariableEnvironment` 컴포넌트는 새로운 렉시컬 환경을 참조하기도 한다)

그리고 렉시컬 환경은 또 다시 `EnvironmentRecord`와 `OuterLexicalEnvironmentReference` 두 개의 컴포넌트로 구성된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/49b1d991-df3b-4e97-a082-faca655e9d4d" width="100%">

환경 레코드(Environment Record)는 특정 렉시컬 환경의 스코프에 포함된 식별자와 식별자에 바인딩된 값을 키와 값 형태의 자료구조로 등록하여 관리하는 일종의 스코프 및 식별자 저장소다. 앞서 언급한 [소스코드의 종류](#1-1-소스코드의-종류)에 따라 관리하는 스코프, 식별자와 식별자 값이 상이한다

외부 렉시컬 환경에 대한 참조(Outer Lexical Environment Reference)는 특정 렉시컬 환경의 상위 스코프를 가리키는 참조값이 담겨있다. 외부 렉시컬 환경에 대한 참조를 통해 하위 및 상위 스코프가 단방향으로 연결(a.k.a [단방향 링크드 리스트인]())되어 [스코프 체인](https://github.com/jacenam/WIL-archive/blob/main/Web%20Development/JS/JS%20Basics/Scope/scope.md#3-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%B2%B4%EC%9D%B8)이 구현되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/7ab16f4d-1d0b-4ff1-bc3f-34ad1b867581" widht="100%"> 

상위 스코프, 현재 스코프, 식별자 정보가 타입별 생성되는 실행 컨텍스트 내의 렉시컬 환경에 각각 등록되어 관리되기 때문에, JS 엔진이 소스코드의 "평가"와 "실행" 시 각각의 실행 컨텍스트에 등록된 식별자 정보를 해당 스코프 내에서 검색하고 취득할 수 있는 것이다

<br>

## 참고

- [모던 자바스크립트 Deep Dive]()