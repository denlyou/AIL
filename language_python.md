# Python

> https://www.python.org/

## 기초와 기본 문법

### 소개

- 인터프리터 언어
- 2.x 버전과 3.x 버전이 같이 사용됨

### 문법

- 세미콜론을 사용하지 않음

#### 출력 : `print()`

```py
print('Hello world')
```

#### 연산자

- 사칙연산 `+` `-` `*` `/`
  - 숫자 앞의 -는 음수 표현 (주의)
  - 0으로 나누면 에러 `ZeroDivisionError`
  - 나눗셈의 결과는 실수(float)
  - 실수와 정수 연산 결과는 실수
- 추가 산술 연산자
  - 제곱 : `**`
  - 나머지 연산에서 몫(quotient)과 나머지(Remainder)
    - 몫 연산자 (floor division) : `\\`
    - 나머지 연산자 (modulo) : `%`

#### 자료형

- String : 작은 따옴표`'문자열'`나 큰 따옴표`"string"` 사용
  - Escape문자열은 역슬래시 (`\`)
    - `\n` : 줄넘김

#### 입력 : `input()`

```py
input("문자를 입력하세요: ")
```
