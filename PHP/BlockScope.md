# 블록 스코프

## 스코프란?

스코프란 변수의 유효 범위를 뜻한다. 보통은 블록 또는 함수 블록이 열렸을 때, 블록 내부에서 선언된 변수는 블록이 닫히면 소멸한다. 블록 안에서 선언된 변수는 블록 밖에서 사용할 수 없다.

#### 블록 스코프를 가진 대상의 의사코드

```
if (condition) {
    declare variale;
}

variable dose not exists;
```

```
fn = function () {
    declare variale;
}

variable dose not exists;
```

#### 함수 스코프를 가진 대상의 의사코드

```
if (condition) {
    declare variale;
}

variale is undefined or declared.
```

```
fn = function () {
    declare variale;
}

variable dose not exists;
```

## 인터프리터 언어

C 계열의 문법적 특징을 가진 언어는 블록 스코프를 가진다. 하지만 ES6 이전의 자바스크립트, PHP, 파이썬 등의 인터프리터 언어는 함수 스코프를 가진다.

## 변수 섀도잉 (Variable Shadowing)

블록 스코프는 스코프 밖의 변수를 스코프 안에서 동일한 이름의 변수명을 선언해서, 상위 스코프의 변수를 하위 스코프에서 덮어 씌우는 것을 의미한다.

스코프의 중첩이 깊어지면, 특정 변수가 상위 스코프의 어디에서 가져온 것인지 하위 스코프의 어디에서 섀도잉이 되었는지 파악해야 하는 문제가 생긴다. 변수명이 같기 때문에 어디서 선언된 변수인지 알아야 변수를 사용할 때 잘못된 사용을 방지할 수 있다. 자바스크립트에서 변수 선언 키워드로 const가 존재하는 것은 하위 스코프의 변수가 덮어 써 지는 것을 방지하기 위함이다.

## References

- https://externals.io/message/109029
