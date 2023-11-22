# 실행 컨텍스트와 식별자 검색 과정

이번 파트에서는 앞선 실행 컨텍스트와 렉시컬 환경 파트에서의 내용을 더 세부적으로 살펴볼 예정이다. 특정 소스코드가 JS 엔진에 의해 평가 및 실행될 때 실행 컨텍스트와 렉시컬 환경에서 코드의 실행 결과가 어떻게 관리되고 식별자를 검색하는지 이해할 수 있다

**Table of Contents**

- [전역 객체 생성](#전역-객체-생성)
- [전역 코드 평가](#전역-코드-평가)
  - [전역 실행 컨텍스트 생성](#전역-실행-컨텍스트-생성)
  - [전역 렉시컬 환경 생성](#전역-렉시컬-환경-생성)
    - [전역 환경 레코드 생성](#전역-환경-레코드-생성)
      - [객체 환경 레코드 생성](#객체-환경-레코드-생성)
      - [선언적 환경 레코드 생성](#선언적-환경-레코드-생성)
    - [this 바인딩](#this-바인딩)
    - [외부 렉시컬 환경에 대한 참조 결정](#외부-렉시컬-환경에-대한-참조-결정)
- [전역 코드 실행](#전역-코드-실행)

<br>

## 전역 객체 생성

아래 예제를 살펴보자

```javascript
var x = 1;
const y = 2; 

function outerFunc(a) {
    var x = 3; 
    const y = 4; 

    function innerFunc(b) {
        var x = 5; 
        const z = 6; 

        console.log(a + b + x + y + z); 
    }
    innerFunc(10); 
} 

outerFunc(20);
```

전역 객체 `window`는 전역 코드가 JS 엔진에 의해 평가되기 이전에 생성된다. 즉 소스코드의 평가를 위해 JS 엔진에 로드되는 시점에 전역 객체가 생성되는 것이다

<br>

## 전역 코드 평가

소스코드의 로딩이 완료되면 JS 엔진은 전역 코드의 평가를 시작한다. 전역 코드 평가는 다음과 같은 순서로 진행된다

- 1 전역 실행 컨텍스트 생성
- 2 전역 렉시컬 환경 생성
  - 2.1 전역 환경 레코드 생성
    - 2.1.1 객체 환경 레코드 생성
    - 2.1.2 선언적 환경 레코드 생성
  - 2.2 this 바인딩
  - 2.3 외부 렉시컬 환경에 대한 참조 결정

### 전역 실행 컨텍스트 생성

전역 코드는 JS 엔진에 의해 평가되어 전역 실행 컨텍스트를 생성하고 이를 실행 컨텍스트 스택에 추가(Push)한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a4884c3a-4119-4193-85c2-f022c1794e01" width="100%">

### 전역 렉시컬 환경 생성

앞서 [렉시컬 환경]() 파트에서 언급했듯이 실행 컨텍스트는 `LexicalEnvironment`와 `VariableEnvironment` 컴포넌트들로 구성되어 있다(`VariableEnvironment` 컴포넌트는 살펴보지 않을 예정이다). 이때 `LexicalEnvironment` 컴포넌트는 렉시컬 환경을 참조한다. 그리고 렉시컬 환경은 "환경 레코드"와 "외부 렉시컬 환경에 대한 참조"로 구성된다.

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/c77da597-0984-412b-804b-4d259570a9f4" widht="100%">

이때 전역 렉시컬 환경은 스코프 체인에서 최상위에 위치하기 때문에 외부 렉시컬 환경에 대한 참조의 참조값은 존재하지 않기 때문에 `null`이 할당된다

### 전역 환경 레코드 생성

렉시컬 환경의 구성 요소 중 하나인 환경 레코드는 각각의 스코프와 스코프 내에서 선언된 변수를 관리한다. 전역 렉시컬 환경이 생성된 이후 전역 환경 레코드가 생성된다

환경 레코드는 또 다시 객체 환경 레코드(`ObjectEnvironmentRecord`)와 선언적 환경 레코드(`DeclarativeEnvironmentRecord`)로 구성된다. `var` 키워드로 선언한 전역 변수는 전역 객체 `window`의 프로퍼티가 되는 반면 `let`과 `const` 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 되지 않고 블록 스코프 내에 존재하게 된다. 이 때문에 키워드별로 선언된 전역 변수를 구분하기 위해 환경 레코드를 객체 환경 레코드와 선언적 환경 레코드로 나눈 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/58183667-4158-4676-84eb-0d62e4768251" width="100%">

### 객체 환경 레코드 생성

객체 환경 레코드는 `var` 키워드로 선언한 전역 변수, 함수 선언문으로 정의한 전역 함수, 빌트인 전역 프로퍼티, 빌트인 전역 함수, 표준 빌트인 객체를 관리한다. 이들은 모두 객체 환경 레코드의 속한 `BindingObject`라는 객체를 통해 관리되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b4f88b5c-7940-42e7-ba15-755507aea4b2" width="100%">

전역 실행 컨텍스트, 전역 렉시컬 환경, 전역 환경 레코드가 순서대로 생성되고나면 객체 환경 레코드가 생성된다. `var` 키워드로 선언된 전역 변수와 함수선언문으로 정의된 전역 함수는 객체 환경 레코드에 속한 `BindingObject` 객체에 바인딩되어 전역 객체 `window`의 프로퍼티와 메서드가 된다. 

> `var` 키워드로 선언한 전역 변수와 함수선언문으로 정의한 전역 함수가 전역 객체의 프로퍼티와 메서드가 되어 전역 객체를 가리키는 식별자 `window` 없이 전역 객체의 프로퍼티를 참조할 수 있는 메커니즘은 모두 객체 환경 레코드의 `BindingObject`에 의해 가능한 것이다. `alert`와 같은 빌트인 전역 함수(메서드), 빌트인 객체도 모두 이와 동일한 메커니즘으로 관리된다 
>
> <img src="https://github.com/jacenam/WIL-archive/assets/92138751/a06eaf6b-3a81-4c98-aa75-710e7246e00f" width="100%">

객체 환경 레코드가 생성되고 나면 위 코드 예제에서의 `x`  전역 변수와 `foo` 전역 함수는 모두 객체 환경 레코드의 `BindingObject` 객체에 바인딩되어 전역 객체의 프로퍼티와 메서드가 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f7a6a9c7-e264-4d27-9df7-3d9ce132f0f8" width="100%"> 

`var` 키워드로 선언한 `x` 전역 변수는 선언 단계와 초기화 단계가 동시에 진행되기 때문에 암묵적으로 `undefined`를 할당받게 된다. 현 시점은 코드 평가 단계이기 때문에 값(`1`)은 아직 할당되기 전이다

### 선언적 환경 레코드 생성

선언적 환경 레코드는 `let`, `const` 키워드로 선언한 전역 변수를 관리한다. 즉 `var` 키워드로 선언한 전역 변수와 함수 선언문으로 정의한 전역 함수 이외의 모든 선언은 선언적 환경 레코드에 등록되어 관리 되는 것이다. [위 예제](#실행-컨텍스트의-생성과-식별자-검색-과정)의 `y` 전역 변수는 `const` 키워드로 선언되었으므로 [선언 단계와 초기화 단계가 분리되어 진행](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/09-Scope/03-variable-indentifiers.md#const와-let-키워드)된다. 즉 현 시점의 코드 평가 단계에서는 일시적 사각지대(Temporal Dead Zone, TDZ)에 빠지게 되어 `<uninitialized>`로 표현되어 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/61cebbd7-b7fa-4b78-bcb0-3456eb7a7bcf" width="100%">

또한 `let`, `const` 키워드로 선언한 변수는 전역 객체 `window`의 프로퍼티가 되지 않으므로 `window.y`와 같이 전역 객체의 프로퍼티로서 참조할 수 없게 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/75441ee0-0012-43bf-957e-91c09f76fc33" width="100%">

### this 바인딩

환경 레코드(현 예제에서는 전역 환경 레코드)에는 원래 `[[ThisValue]]`(현 예제에서는 `[[GlobalThisValue]]`) 내부 슬롯도 포함되어 있으나, 앞서 [전역 환경 레코드 생성](#전역-환경-레코드-생성) 파트에서는 언급하지 않았다. 그래서 사실 전역 환경 레코드는 객체 환경 레코드, 선언적 환경 레코드와 더불어 아래 그림과 같이 `[[GlobalThisValue]]` 내부슬롯까지 존재하는 것을 확인할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/1bf69e5e-48b8-4c29-9a4d-471ba2fdaee6" width="100%">

전역 코드에서 `this`는 전역 객체를 가리키므로 전역 환경 레코드의 `[[GlobalThisValue]]` 내부 슬롯에는 전역 객체 `window`가 바인딩되어 전역 객체를 가리키는 참조값을 포함하고 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e02c2d04-4099-45d9-a8ea-71d429974001" width="100%">

따라서 [`this` 참조](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/11-This/01-this-intro.md#this의-참조) 파트에서 살펴봤듯이,  전역 코드에서 `this`를 참조했을 때 전역 환경 레코드의 `[[GlobalThisValue]]` 내부 슬롯에 바인딩되어 있는 전역 객체 `window`를 반환할 수 있게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f5196629-2793-4d75-98ec-4aa638251389" width="100%">

### 외부 렉시컬 환경에 대한 참조 결정

외부 렉시컬 환경에 대한 참조는 현재 평가 중인 소스코드보다 스코프 체인상 상위 스코프에 위치한 스코프를 가리키며, 이는 [단반향 링크드 리스트]()인 형태로 스코프 체인을 이룬다. 현 시점은 전역 코드의 평가 시점으로 전역 코드를 포함하는 상위 스코프가 존재하지 않는다. 따라서 전역 렉시컬 환경의 외부 렉시컬 환경에 대한 참조는 `null`로 할당된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f59e997a-c5e6-440f-87bb-49e07a2fb960" width="100%">

<br>

## 전역 코드 실행

전역 코드의 평가가 종료되면 전역 코드가 순차적으로 실행되는 런타임에 진입한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/7446c3bc-cc98-4157-84da-6f36c2f409a6" width="100%">

JS 엔진은 식별자에 값을 할당하고 함수를 호출한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ade7ceaa-aa40-4339-9667-ab847c62d8df" width="100%">

선언되어 렉시컬 환경에 등록되지 않은 식별자는 참조할 수 없으므로 할당이나 호출이 불가능하다. 따라서 식별자에 값을 할당하는 할당문, 함수 호출문이 실행되려면 JS 엔진은 먼저 식별자(변수, 함수 이름)가 선언된 식별자인지 확인해야 한다. 이 과정에서 식별자는 스코프가 다르면 동일한 이름의 식별자가 다른 스코프에 여러 개 존재할 수 있다. 선언된 식별자인지, 어느 스코프의 식별자인지 JS 엔진이 결정하는 이러한 과정을 식별자 결정(Identifier Resolution)이라 한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/3ad7e40d-b2c7-4ebc-8964-b8f8ba9a2ada" width="100%">

JS 엔진은 식별자 결정은 위한 식별자를 검색할 때 현재 실행 중인 실행 컨텍스트에서부터 식별자를 검색하기 시작한다. 앞서 [실행 컨텍스트 스택](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/12-Execution-Context/02-stack-lexical.md#실행-컨텍스트-스택) 파트에서 언급했듯이, 실행 컨텍스트 스택에서 최상위에 쌓인 실행 컨텍스트가 현재 실행되는 코드의 실행 컨텍스트라고 했다. 현재는 전역 코드를 실행 중이므로 전역 실행 컨텍스트가 스택의 최상위에 위치한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f9c24c0c-43d0-416f-a0a6-71f14ed6b9c8" width="100%">

선언된 식별자는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경의 환경 레코드에 등록되어 있다. 따라서 전역 실행 컨텍스트의 전역 렉시컬 환경에서 환경 레코드에 등록된 식별자 `x`, `y`, `outerFunc`를 검색한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/4df050cb-c2db-49c9-a7dc-bd1819114ca3" width="100%">

실행 중인 실행 컨텍스트의 렉시컬 환경에서 식별자를 검색할 수 없는 상태면 외부 렉시컬 환경에 대한 참조가 가리키는 렉시컬 환경인 상위 스코프로 이동하여 실행이  필요한 식별자를 검색한다. 식별자를 검색하는데 찾는 식별자가 존재하지 않으면 계속해서 상위 스코프로 검색이 이동하며, 스코프 체인의 종점인 전역 렉시컬 환경에서 식별자를 검색할 수 없는 경우 참조 에러(`ReferenceError`)가 발생한다. 즉 식별자 결정에 실패했다는 의미다. 이것이 바로 스코프 체인의 동작 원리다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/216e945f-8b40-4d80-a8fa-23cdf93041df" width="100%"> 

<br>

## 함수 코드 평가

전역 코드에서 함수 호출문이 실행되면 전역 코드의 실행이 중단되고 `outerFunc` 함수 내부로 코드의 흐름이 이동한다. 그리고 함수 코드의 평가가 시작된다. 함수 코드 평가는 아래와 같은 순서로 진행된다:

- 1. 함수 실행 컨텍스트 생성

- 2. 함수 렉시컬 환경 생성

  - 2.1 함수 환경 레코드 생성
  - 2.2 this 바인딩
  - 2.3 외부 렉시컬 환경에 대한 참조 결정

### 함수 실행 컨텍스트 생성

함수 코드는 JS 엔진에 의해 평가되어 함수 실행 컨텍스트를 생성하고 이를 실행 컨텍스트 스택에 푸시한다. 그리고 실행 컨텍스트 스택의 최상위에 쌓인 함수 실행 컨텍스트는 현재 실행 중인 실행 컨텍스트가 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/390de938-6a42-4f72-8a01-829191aa2aea" width="100%">

### 함수 렉시컬 환경 생성



<br>

## 참고

- [모던 자바스크립트 Deep Dive]()