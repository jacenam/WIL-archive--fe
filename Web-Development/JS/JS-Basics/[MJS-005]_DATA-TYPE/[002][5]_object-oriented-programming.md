# 객체지향 프로그래밍

**Table of Contents**

- [객체지향 프로그래밍이란](#객체지향-프로그래밍이란)

<br>

## 객체지향 프로그래밍이란

객체지향 프로그래밍(Object Oriented Programming)이란 프로그래밍 명령어 혹은 함수의 목록을 여러 개의 독립적인 단위, 즉 객체 단위로 프로그램을 표현하려는 프로그래밍 패러다임을 의미한다. 

객체지향 프로그래밍은 실세계의 사람이 인지하는 사물이나 개념 같은 실체를 프로그래밍에 접목하려는 시도에서 시작했다. 사물이나 개념의 특징 혹은 성질을 표현하여 이를 통해 구별력을 갖게 하는 것이다.

아래 예제와 같이 사람의 이름, 나이, 성별, 주소, 학력, 성격, 행동 등 다양한 속성을 프로그래밍적으로 표현하는 것이다. 이를 통해 객체들은 각자만의 차별점을 갖기 때문에, 이를 통해 프로그래머는 서로 다른 객체들을 구별할 수 있게 된다

```javascript
const user = {
  // 한 사람의 속성을 프로그래밍상 정적으로 표현하는 방법 
  name: "Jace",
  age: 31, 
  country: "Korea",
  address: "Seoul",
  // 한 사람의 속성을 프로그래밍상 동적으로 표현하는 방법(메서드)
  selfIntro() {
    console.log(`My name is ${this.name}. I live in ${Seoul}`);
  }
}
```

한 가지 예제를 더 살펴보자. 위 예제처럼, 원이라는 특정 실체를 프로그램상 정적으로 표현하는 방법과 동적으로 행위를 표현하는 방법이 존재한다. 이렇듯, 특정 실체의 상태 데이터와 그에 대한 동작을 객체로 표현할 수 있으며 이러한 실체의 여러가지 속성 값들을 하나의 단위인 객체로 묶어 복합적인 구조로 해당 실체를 표현할 수 있게 된다

```javascript
const circle = {
  radius: 10, 
  
  getDiameter() {
    return 2 * this.radius;
  },
  
  getPerimeter() {
    return 2 * Math.PI * this.radius; 
  },
  
  getArea() {
    return Math.PI * Math.pow(this.radius, 2);
  }
}

console.log(circle.getDiameter()); // → 20
console.log(circle.getArea()); // → 314.1592653589793
```

각 객체들은 이렇게 고유의 속성들을 갖는 독립적인 주체로 간주할 수 있지만, 자신의 고유 속성들을 표현하는 기능을 수행하면서 다른 객체와의 관계성을 가질 수도 있다