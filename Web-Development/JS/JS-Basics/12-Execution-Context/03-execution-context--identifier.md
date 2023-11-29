# 실행 컨텍스트와 식별자 검색 과정

이번 파트에서는 앞선 실행 컨텍스트와 렉시컬 환경 파트에서의 내용을 더 세부적으로 살펴볼 예정이다. 특정 소스코드가 JS 엔진에 의해 평가 및 실행될 때 실행 컨텍스트와 렉시컬 환경에서 코드의 실행 결과가 어떻게 관리되고 식별자를 검색하는지 이해할 수 있다

**Table of Contents**

- [전역 코드, 실행 컨텍스트와 렉시컬 환경](#전역-코드,-실행-컨텍스트와-렉시컬-환경)
  - [전역 객체 생성](#전역-객체-생성)
  - [전역 코드 평가](#전역-코드-평가)
  - [전역 코드 실행](#전역-코드-실행)
- [함수 코드, 실행 컨텍스트와 렉시컬 환경](#함수-코드,-실행-컨텍스트와-렉시컬-환경)
  - [outerFunc 함수 코드 평가](#outerFunc-함수-코드-평가)
  - [outerFunc 함수 코드 실행](#outerFunc-함수-코드-실행)
  - [innerFunc 함수 코드 평가](#innerFunc-함수-코드-평가)
  - [innerFunc 함수 코드 실행](#innerFunc-함수-코드-실행)
- [코드 실행 종료](#코드-실행-종료)
  - [innerFunc 함수 코드 실행 종료](#innerFunc-함수-코드-실행-종료)
  - [outerFunc 함수 코드 실행 종료](#outerFunc-함수-코드-실행-종료)
  - [전역 코드 실행 종료](#전역-코드-실행-종료)

<br>

## 전역 코드, 실행 컨텍스트와 렉시컬 환경

### 전역 객체 생성

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

### 전역 코드 평가

소스코드의 로딩이 완료되면 JS 엔진은 전역 코드의 평가를 시작한다. 전역 코드 평가는 다음과 같은 순서로 진행된다

- 1 전역 실행 컨텍스트 생성
- 2 전역 렉시컬 환경 생성
  - 2.1 전역 환경 레코드 생성
    - 2.1.1 객체 환경 레코드 생성
    - 2.1.2 선언적 환경 레코드 생성
  - 2.2 this 바인딩
  - 2.3 외부 렉시컬 환경에 대한 참조 결정

**1 전역 실행 컨텍스트 생성**

전역 코드는 JS 엔진에 의해 평가되어 전역 실행 컨텍스트를 생성하고 이를 실행 컨텍스트 스택에 추가(Push)한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/a4884c3a-4119-4193-85c2-f022c1794e01" width="100%">

**2 전역 렉시컬 환경 생성**

앞서 [렉시컬 환경]() 파트에서 언급했듯이 실행 컨텍스트는 `LexicalEnvironment`와 `VariableEnvironment` 컴포넌트들로 구성되어 있다(`VariableEnvironment` 컴포넌트는 살펴보지 않을 예정이다). 이때 `LexicalEnvironment` 컴포넌트는 렉시컬 환경을 참조한다. 그리고 렉시컬 환경은 "환경 레코드"와 "외부 렉시컬 환경에 대한 참조"로 구성된다.

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/c77da597-0984-412b-804b-4d259570a9f4" widht="100%">

이때 전역 렉시컬 환경은 스코프 체인에서 최상위에 위치하기 때문에 외부 렉시컬 환경에 대한 참조의 참조값은 존재하지 않기 때문에 `null`이 할당된다

**2.1 전역 환경 레코드 생성**

렉시컬 환경의 구성 요소 중 하나인 환경 레코드는 각각의 스코프와 스코프 내에서 선언된 변수를 관리한다. 전역 렉시컬 환경이 생성된 이후 전역 환경 레코드가 생성된다

환경 레코드는 또 다시 객체 환경 레코드(`ObjectEnvironmentRecord`)와 선언적 환경 레코드(`DeclarativeEnvironmentRecord`)로 구성된다. `var` 키워드로 선언한 전역 변수는 전역 객체 `window`의 프로퍼티가 되는 반면 `let`과 `const` 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 되지 않고 블록 스코프 내에 존재하게 된다. 이 때문에 키워드별로 선언된 전역 변수를 구분하기 위해 환경 레코드를 객체 환경 레코드와 선언적 환경 레코드로 나눈 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/58183667-4158-4676-84eb-0d62e4768251" width="100%">

**2.1.1 객체 환경 레코드 생성**

객체 환경 레코드는 `var` 키워드로 선언한 전역 변수, 함수 선언문으로 정의한 전역 함수, 빌트인 전역 프로퍼티, 빌트인 전역 함수, 표준 빌트인 객체를 관리한다. 이들은 모두 객체 환경 레코드의 속한 `BindingObject`라는 객체를 통해 관리되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/b4f88b5c-7940-42e7-ba15-755507aea4b2" width="100%">

전역 실행 컨텍스트, 전역 렉시컬 환경, 전역 환경 레코드가 순서대로 생성되고나면 객체 환경 레코드가 생성된다. `var` 키워드로 선언된 전역 변수와 함수선언문으로 정의된 전역 함수는 객체 환경 레코드에 속한 `BindingObject` 객체에 바인딩되어 전역 객체 `window`의 프로퍼티와 메서드가 된다. 

> `var` 키워드로 선언한 전역 변수와 함수선언문으로 정의한 전역 함수가 전역 객체의 프로퍼티와 메서드가 되어 전역 객체를 가리키는 식별자 `window` 없이 전역 객체의 프로퍼티를 참조할 수 있는 메커니즘은 모두 객체 환경 레코드의 `BindingObject`에 의해 가능한 것이다. `alert`와 같은 빌트인 전역 함수(메서드), 빌트인 객체도 모두 이와 동일한 메커니즘으로 관리된다 
>
> <img src="https://github.com/jacenam/WIL-archive/assets/92138751/a06eaf6b-3a81-4c98-aa75-710e7246e00f" width="100%">

객체 환경 레코드가 생성되고 나면 위 코드 예제에서의 `x`  전역 변수와 `foo` 전역 함수는 모두 객체 환경 레코드의 `BindingObject` 객체에 바인딩되어 전역 객체의 프로퍼티와 메서드가 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f7a6a9c7-e264-4d27-9df7-3d9ce132f0f8" width="100%"> 

`var` 키워드로 선언한 `x` 전역 변수는 선언 단계와 초기화 단계가 동시에 진행되기 때문에 암묵적으로 `undefined`를 할당받게 된다. 현 시점은 코드 평가 단계이기 때문에 값(`1`)은 아직 할당되기 전이다

**2.1.2 선언적 환경 레코드 생성**

선언적 환경 레코드는 `let`, `const` 키워드로 선언한 전역 변수를 관리한다. 즉 `var` 키워드로 선언한 전역 변수와 함수 선언문으로 정의한 전역 함수 이외의 모든 선언은 선언적 환경 레코드에 등록되어 관리 되는 것이다. [위 예제](#실행-컨텍스트의-생성과-식별자-검색-과정)의 `y` 전역 변수는 `const` 키워드로 선언되었으므로 [선언 단계와 초기화 단계가 분리되어 진행](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/09-Scope/03-variable-indentifiers.md#const와-let-키워드)된다. 즉 현 시점의 코드 평가 단계에서는 일시적 사각지대(Temporal Dead Zone, TDZ)에 빠지게 되어 `<uninitialized>`로 표현되어 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/61cebbd7-b7fa-4b78-bcb0-3456eb7a7bcf" width="100%">

또한 `let`, `const` 키워드로 선언한 변수는 전역 객체 `window`의 프로퍼티가 되지 않으므로 `window.y`와 같이 전역 객체의 프로퍼티로서 참조할 수 없게 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/75441ee0-0012-43bf-957e-91c09f76fc33" width="100%">

**2.2 this 바인딩**

환경 레코드(현 예제에서는 전역 환경 레코드)에는 원래 `[[ThisValue]]`(현 예제에서는 `[[GlobalThisValue]]`) 내부 슬롯도 포함되어 있으나, 앞서 [전역 환경 레코드 생성](#전역-환경-레코드-생성) 파트에서는 언급하지 않았다. 그래서 사실 전역 환경 레코드는 객체 환경 레코드, 선언적 환경 레코드와 더불어 아래 그림과 같이 `[[GlobalThisValue]]` 내부슬롯까지 존재하는 것을 확인할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/1bf69e5e-48b8-4c29-9a4d-471ba2fdaee6" width="100%">

전역 코드에서 `this`는 전역 객체를 가리키므로 전역 환경 레코드의 `[[GlobalThisValue]]` 내부 슬롯에는 전역 객체 `window`가 바인딩되어 전역 객체를 가리키는 참조값을 포함하고 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e02c2d04-4099-45d9-a8ea-71d429974001" width="100%">

따라서 [`this` 참조](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/11-This/01-this-intro.md#this의-참조) 파트에서 살펴봤듯이,  전역 코드에서 `this`를 참조했을 때 전역 환경 레코드의 `[[GlobalThisValue]]` 내부 슬롯에 바인딩되어 있는 전역 객체 `window`를 반환할 수 있게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f5196629-2793-4d75-98ec-4aa638251389" width="100%">

**2.3 외부 렉시컬 환경에 대한 참조 결정**

외부 렉시컬 환경에 대한 참조는 현재 평가 중인 소스코드보다 스코프 체인상 상위 스코프에 위치한 스코프를 가리키며, 이는 [단반향 링크드 리스트]()인 형태로 스코프 체인을 이룬다. 현 시점은 전역 코드의 평가 시점으로 전역 코드를 포함하는 상위 스코프가 존재하지 않는다. 따라서 전역 렉시컬 환경의 외부 렉시컬 환경에 대한 참조는 `null`로 할당된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/f59e997a-c5e6-440f-87bb-49e07a2fb960" width="100%">

### 전역 코드 실행

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

## 함수 코드, 실행 컨텍스트와 렉시컬 환경

### outerFunc 함수 코드 평가

전역 코드에서 함수 호출문이 실행되면 전역 코드의 실행이 중단되고 `outerFunc` 함수 내부로 코드의 흐름이 이동한다. 그리고 함수 코드의 평가가 시작된다. 함수 코드 평가는 아래와 같은 순서로 진행된다:

- 1 함수 실행 컨텍스트 생성시

- 2 함수 렉시컬 환경 생성
  - 2.1 함수 환경 레코드 생성
  - 2.2 this 바인딩
  - 2.3 외부 렉시컬 환경에 대한 참조 결정

**1 함수 실행 컨텍스트 생성**

함수 코드는 JS 엔진에 의해 평가되어 함수 실행 컨텍스트를 생성하고 이를 실행 컨텍스트 스택에 푸시한다. 그리고 실행 컨텍스트 스택의 최상위에 쌓인 함수 실행 컨텍스트는 현재 실행 중인 실행 컨텍스트가 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/390de938-6a42-4f72-8a01-829191aa2aea" width="100%">

**2 함수 렉시컬 환경 생성**

`outerFunc` 실행 컨텍스트의 `LexicalEnvironment` 컴포넌트는 `outerFunc` 렉시컬 환경을 참조한다. 그리고 `outerFunc` 렉시컬 환경은 환경 레코드(함수 환경 레코드)와 외부 렉시컬 환경에 대한 참조 컴포넌트로 구성된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e4b7126e-7955-4846-9433-06e73ab4a28d" width="100%">

**2.1 함수 환경 레코드 생성**

함수 환경 레코드는 매개변수, `arguments` 객체, 함수 내부의 지역 변수, 중첩 함수를 등록하고 관리한다

> [`arguments` 객체]()는 매개변수로 전달되는 인수를 [배열]() 형태로 객체화 하여 함수 호출 시 필요에 따라 전달되는 인수의 동작을 다르게 제어할 수 있게 된다. 아래 예제와 같이 `arguments` 객체는 배열의 메서드를 사용하여 객체의 상태를 확인할 수 있다
>
> ```javascript
> function sum(a, b) {
>   console.dir(arguments); // → Arguments(2)
>   console.log(arguments[0], arguments.length); // → 1 2
>   console.log(arguments.callee); // → funciton { ƒ }
>   return a + b;
> }
> 
> sum(1, 2);
> ```

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/49fd0e58-5518-42e2-90f1-55a7d19b0394" width="100%">

**2.2 this 바인딩**

[함수 환경 레코드 생성](#함수-환경-레코드-생성) 파트에서는 언급하진 않았지만, `outerFunc` 렉시컬 환경의 함수 환경 레코드에는 `[[ThisValue]]` 내부 슬롯도 존재한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e3d008e1-2cb8-41e5-a94a-f5ae6c598b81" width="100%">

함수 환경 레코드가 생성되고 난 후 해당 `[[ThisValue]]` 내부 슬롯에 `this`가 바인딩된다. 앞서 [함수 호출과 this 바인딩 결정 방식](https://github.com/jacenam/WIL-archive/blob/main/Web-Development/JS/JS-Basics/11-This/02-function-invoke-this-binding.md#함수-호출-방식과-this-바인딩-결정-방식)에서 살펴봤듯이, `outerFunc` 함수는 일반적인 함수 호출 방식으로 호출되었으므로 `this`는 전역 객체에 바인딩된다. 전역 객체 `window`는 위에 [전역 코드 실행](#전역-코드-실행)에서 식별자에 값의 할당이 모두 완료된 상태다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/87a46e81-cc05-4cae-9bf7-f0abd16bf4a1" width="100%">

**2.3 외부 렉시컬 환경에 대한 참조 결정**

외부 렉시컬 환경에 대한 참조는 특정 코드가 평가된 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킨다. 다시 말해, `outerFunc` 함수는 전역에서 정의된 전역 함수로 전역 코드 평가 시점에서 평가되었기 때문에 전역 실행 컨텍스트의 렉시컬 환경을 가리키게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ddc2439b-091c-4766-97e5-ef3f7f5840ff" width="100%">

뒤에서 언급하지만, `innerFunc` 함수 렉시컬 환경의 외부 렉시컬 환경에 대한 참조는 `outerFunc` 렉시컬 환경을 가리키게 된다. `innerFunc` 함수는 `outerFunc` 함수 내부에서 정의된 지역 함수로 `outerFunc` 함수 코드 평가 시점에서 평가되기 때문에 `outerFunc` 렉시컬 환경을 가리키게 되는 것이다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/00560860-e9cd-4235-a902-6814ced20849" width="100%">

이렇듯, 함수는 자신이 정의된 스코프, 즉 상위 스코프에 대한 참조 정보를 저장하고 있다. JS 엔진은 함수를 평가하여 함수 객체를 생성하는(함수의 평가 시점 = 함수 객체의 생성 시점) 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경 정보를 함수 객체의 내부 슬롯인 `[[Environment]]`에 저장한다. 이러한 방식을 통해 렉시컬 스코프가 구현되는 것이다. `[[Environment]]` 내부 슬롯은 [클로저]()에서 매우 중요한 개념이므로 기억해두는 것이 좋다

### outerFunc 함수 코드 실행

`outerFunc` 함수의 내부 코드가 순차적으로 실행되는 시점이다

```javascript
// var x = 1;
// const y = 2; 

// function outerFunc(a) {
    var x = 3; 
    const y = 4; 

    function innerFunc(b) {
        // var x = 5; 
        // const z = 6; 

        // console.log(a + b + x + y + z); 
    }
    innerFunc(10); 
// } 

// outerFunc(20);
```

함수 호출 시 지정된 인수가 매개변수에 전달되고 지역 변수 `x`, `y`에 값이 할당된다. 이때 앞서 [전역 코드 평가](#전역-코드-평가)에서 언급한 "식별자 결정"이 이뤄져야 하기 때문에 JS 엔진은 현재 실행 중인 `outerFunc` 실행 컨텍스트의 `outerFunc` 렉시컬 환경에서 식별자를 검색하기 시작한다. 식별자 결정이 모두 완료되면 적합한 식별자에 값을 바인딩(할당)하고 `innerFunc` 호출문을 실행한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/5671c578-57b2-48f2-8e79-b2cff84cb6da" widht="100%">

### innerFunc 함수 코드 평가

`innerFunc` 함수가 호출되면 `innerFunc` 함수 내부로 코드의 흐름이 이동한다. 그리고 여느때와 마찬가지로 `innerFunc` 함수 내부의 코드에 대한 평가가 시작된다. `outerFunc` 함수 코드의 평가 과정과 동일하게 진행된다:

- 1 함수 실행 컨텍스트 생성

  <img src="https://github.com/jacenam/WIL-archive/assets/92138751/2b71a008-786a-417a-8acf-ec86938e06c2" width="100%">

- 2 함수 렉시컬 환경 생성

  <img src="https://github.com/jacenam/WIL-archive/assets/92138751/ad6e09d7-9f78-466f-8fc7-1c1f8fc587cd" width="100%">

  - 2.1 함수 환경 레코드 생성

    <img src="https://github.com/jacenam/WIL-archive/assets/92138751/54d642f8-3c2b-4a11-a936-f850eca5c772" width="100%">

  - 2.2 this 바인딩

    <img src="https://github.com/jacenam/WIL-archive/assets/92138751/690fae0f-fa0a-4658-9b4c-8ba9d612b7d4" width="100%">

  - 2.3 외부 렉시컬 환경에 대한 참조 결정

    <img src="https://github.com/jacenam/WIL-archive/assets/92138751/6ceaea6f-c07c-41fd-8cc6-8dbed325a440" width="100%">

### innerFunc 함수 코드 실행

`innerFunc` 함수 내부의 코드를 실행하는 런타임 시점이 되면 매개변수, 변수에 값이 할당된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/17a22e25-2a0c-45e5-a614-63342640b467" width="100%">

그리고 JS 엔진은 `console.log(a + b + x + y + z)` 참조문을 실행하기 위해 `console` 프로퍼티를 스코프 체인을 따라 탐색하기 시작한다. 스코프 체인의 시작점은 현재 실행 중인 실행 컨텍스트의 렉시컬 환경이므로 `innerFunc` 함수 실행 컨텍스트의 `innerFunc` 렉시컬 환경에서부터 탐색이 시작되는 것이다

`innerFunc` 렉시컬 환경에는 `console`이라는 이름을 가진 프로퍼티(식별자)가 없다 

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/26c9c7c8-8d01-4c3e-806f-e333ee933005" width="100%">

따라서 `innerFunc` 렉시컬 환경의 외부 렉시컬 환경에 대한 참조가 가리키는 스코프 체인의 상위 스코프 `outerFunc` 렉시컬 환경으로 이동하여 `console` 식별자를 검색한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ae93e26b-0a45-459a-8216-2695102e666d" width="100%">

그러나 `outerFunc` 렉시컬 환경에도 `console` 프로퍼티(식별자)가 존재하지 않는다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/52535de6-72d5-46ff-a4be-7b08529a382f" width="100%">

따라서 `outerFunc` 렉시컬 환경의 외부 렉시컬 환경에 대한 참조가 가리키는 상위 스코프 전역 렉시컬 환경으로 이동하여 `console` 식별자를 검색한다. 그리고 `console` 식별자는 전역 렉시컬 환경의 객체 환경 레코드에서 `BindingObject`를 통해 전역 객체의 프로퍼티로서 존재한다는 것을 확인할 수 있게 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/4a050eeb-af23-4cee-a95e-c3cb2918bb4c" width="100%">

브라우저에서 전역 객체 `window`를 참조하면 전역 객체의 프로퍼티로서 `console` 프로퍼티(식별자)가 존재하는 것을 확인할 수 있다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/61d2eb0e-6045-4d73-ab14-1aafc4d3614b" width="100%">

그리고 `console` 프로퍼티는 객체로서 여러 개의 메서드를 가지는데 그 중 하나가 바로 `log` 메서드다. `log` 메서드가 실행되고 `log` 메서드에 전달된 인수 `a + b + x + y + z` 표현식을 평가하기 위해 JS 엔진은 각각의 식별자를 스코프별로 탐색한다. `console.log` 메서드가 호출된 곳은 `innerFunc` 지역 스코프이므로, 전체 스코프에서 동일한 식별자가 선언된 경우 `innerFunc` 지역 스코프가 식별자에 대한 우선권을 갖게 된다

`a + b + x + y + z` 표현식이 평가되어 생성된 값 `45`를 `console.log` 메서드에 전달하여 호출하고, 콘솔에 `45`를 출력한다

<br>

## 코드 실행 종료

### innerFunc 함수 코드 실행 종료

`console.log` 메서드가 호출된 후 `innerFunc` 함수 코드 내부에는 더 이상 실행할 코드가 없으므로 `innerFunc` 함수 코드의 실행이 종료된다. 따라서 실행 컨텍스트 스택에서 `innerFunc` 함수 실행 컨텍스트는 제거(Pop)되고 `outerFunc` 함수 실행 컨텍스트가 실행 중인 컨텍스트가 된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/c29323de-a9bb-4368-a203-b58f42b4b544" width="100%">

### outerFunc 함수 코드 실행 종료

`innerFunc` 함수가 종료되면 `outerFunc` 함수도 더 이상 실행될 코드가 없으므로 종료된다. 마찬가지로 실행 컨텍스트 스택에서 `outerFunc` 함수 실행 컨텍스트도 제거된다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/ac106470-bce4-47fd-a80d-7beea7f28c7b" width="100%">

여기서 기억해야 할 것으로, 실행 컨텍스트 스택에서 `innerFunc`, `outerFunc` 함수 실행 컨텍스트들이 제거되었다고 해서 각각의 함수 렉시컬 환경까지 소멸되는 것은 아니다. 렉시컬 환경은 독립적인 객체이므로, 메모리의 일정 공간을 차지하고 있다가 누구에게라도 참조되지 않을 때 비로소 메모리 가비지 컬렉터(Memory Garbage Collector)에 의해 메모리 공간에서 해제되는 것이다

### 전역 코드 실행 종료

`outerFunc` 함수가 종료되면 전역 코드 또한 더 이상 실행될 코드가 없으므로 종료된다. 실행 컨텍스트 스택에서 전역 실행 컨텍스트가 제거되고 스택에는 아무것도 남아있지 않게 되며, 이는 코드의 종료를 의미한다

<img src="https://github.com/jacenam/WIL-archive/assets/92138751/e781f786-38be-4848-8671-80441bcdf98c" width="100%">

<br>

## 참고

- [모던 자바스크립트 Deep Dive]()