<img src="https://ifh.cc/g/PvKPBC.png" style="max-width: 100%" align="center">

### 목차

- [1 Tools for Code](#1-Tools-for-Code)
  - [1-1 ESLint](#1-1-ESLint)
  - [1-2 Prettier](#1-2-Prettier)

***

<br>

## 1 Tools for Code

Prettier, ESLint는 코드의 일관성을 유지하기 위해 사용하는 일종의 코드 툴(프로그램)이다. 특히 팀 프로젝트 협업 시 팀별 [코딩 컨벤션(Coding Convention)]()이 상이할 수 있기 때문에 팀에서 수립된 컨벤션에 따라 코드 일관성을 유지할 때 효율적이다

### 1-1 ESLint

정적 타입의 프로그래밍 언어(Java, C, C++)는 [컴파일]() 과정에서 코드의 문제들을 잡아주는 'Linter' 프로그램이 내장되어 있다. 그러나 JS는 인터프리터 언어로 Linter가 제공되지 않기 때문에 ESLint라는 외부 코드 프로그램이 필요하다. ESLint는 JS 코드 전용 코드 프로그램으로서 코드의 문법, 패턴, 구조 등의 문제들을 식별해내 일관성 있는 코드로 바로 잡아주는 기능을 수행한다

> **Lint & Linter?**
>
> Lint는 소스코드 분석 툴로서 코드의 버그, 에러, 불일치성을 검수한다. 따라서 Linter는 Lint 툴을 수행하는 프로그램이다

ESLint는 그래서 왜 필요한가? 특정 기능을 구현할 때, 그 기능을 구현하기 위한 하나의 방법만이 존재하는 것이 아니다. 즉, 여러 방법으로 그 기능을 구현할 수 있다. 아래 함수 정의 코드 예제만 해도 그렇다. 어떨 때는 함수 선언문을 사용하고, 어떨 때는 함수 표현식을 사용한다:

```javascript
// 함수 선언문
function sayHello(name) {
  console.log(`Hello, ${name}`);
}

// 함수 표현식 
const msg = function(name) {
  console.log(`Hello, ${name}`);
}

// 생성자 함수
const msg = new Function('name', 'console.log(`Hello, ${name}`)');

// 화살표 함수
const msg = (name) => console.log(`Hello, ${name}`);
```

팀 협업이라 가정했을 때, 누구는 배열의 요소를 순회할 때  `for`문을 사용하지만 누구는 `forEach()`, `map()` 등 [빌트인 메서드]()를 사용할 수도 있다:

```javascript
const array = ["a", "b", "c"]; 

// for문
for(let i = 0; i < array.length; i++) {
  console.log(array[i]);
}

// forEach()
array.forEach(e => console.log(e));

// map()
array.map(e => console.log(e));
```

하나의 방식으로 코드의 일관성을 유지하는 것이 가독성 및 유지보수성 측면에서 더 뛰어나다. 따라서 ESLint는 이러한 각각의 코드 작성 방식들을 통일된 방식으로 바로 잡아주는 역할을 수행하는 것이다

> Airbnb, Google, Dropbox 등 각 회사(팀)마다,  JavaScript Standard, jQuery 등 표준 작성법에 따라 사용하는 코딩 컨벤션은 상이한다. ESLint의 설정(Configuration)을 팀에 적합하게 설정해서 코드 통일성을 유지할 수 있다. 이에 대해서는 밑에 정리한 [ESLint 설정]() 참고하자

### 1-2 Prettier

Prettier는 ESLint처럼 문법, 패턴, 구조 등의 문제를 식별해 코드의 품질과 구현 방식을 개선해주는 것이 아닌 공백, 줄바꿈, 들여쓰기 등 지정한 Prettier 설정에 따라 코드의 서식(스타일)을 교정해주는 프로그램이다. 팀에서 사용할 Prettier를 동일하게 설정해 협업 시 동일한 서식으로 코드 작성의 가독성 및 유지보수성을 향상시킬 수 있다



<br>

***

### 참고

