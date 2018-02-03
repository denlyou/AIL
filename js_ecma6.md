# Javascript ES6

## ECMA Script6 문법 정리

### 변수와 상수 (let, const)

> **한줄 요약 : let은 변수, const는 상수 선언으로 구분**

> [참고 사이트 : PoiemaWeb](http://poiemaweb.com/es6-block-scope)

- Scope ( 변수의 유효 범위 )
  - Function-level scope : 자바 스크립트의 기본 function안에서 지역변수 (var)
  - Block-level scope : 코드블록( {...} ) 내부에서 유효 (let, const)

- 중복 선언 불가 (SyntaxError 발생)

- 호이스팅 (Hoisting) : 모든 선언(var, let, const, function, function*, class)을 해당 스코프의 선두로 옮기는 것
  - 일시적 사각지대(Temporal Dead Zone; TDZ) : let 키워드로 선언된 변수는 선언문 전에 상사용하면 Uncaught ReferenceError 발생
