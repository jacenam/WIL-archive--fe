# 프로토타입 관련 메서드

### 목차 

- [1 instanceof 연산자](#1-instanceof-연산자)
- [2 직접 상속](#2-직접-상속)
  - [2-1 Object.create에 의한 직접 상속](#2-1-Object.create에-의한-직접-상속)
  - [2-2 proto 접근자 프로퍼티에 의한 직접 상속](#2-2-proto-접근자-프로퍼티에-의한-직접-상속)
- [3 프로퍼티 존재 확인](#3-프로퍼티-존재-확인)
  - [3-1 in 연산자](#3-1-in-연산자)
  - [3-2 Object.prototype.hasOwnProperty 메서드](#3-2-Object.prototype.hasOwnProperty-메서드)
- [4 프로퍼티 열거](#4-프로퍼티-열거)
  - [4-1 for...in 문](#4-1-for...in-문)
  - [4-2 Object.keys/values/entries 메서드](#4-1-Object.keys/values/entries-메서드)

<br>

## 1 instanceof 연산자

