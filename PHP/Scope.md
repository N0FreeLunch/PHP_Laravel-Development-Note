## 스코프란?
- 스코프란 변수가 동작하는 유효 범위를 의미한다.

### php에서 스코프
- php에서 스코프는 함수 내부를 스코프로 한다.
- `function () { }`에서 중괄호 안이 스코프의 범위이다.
```php
function () {
    echo "no variable";
    $variable = "value";
    echo "variable has $variable";
}
```
- 위의 코드에서 `$variable`이란 변수의 스코프는 변수가 초기화 된 위치 `$variable = "value";` 부터 함수가 끝나는 `}`까지이다.
- 코드의 실행에 따라 변수가 살아 있는 범위를 스코프라고 한다. 하지만 일반적으로 어떤 변수를 선언할 수 있고, 선언된 변수가 사라지는 코드 실행 지점까지인 함수의 괄호 `{}`를 스코프라고 부른다.

### 상위 스코프의 변수 차단
- 많은 언어에서 함수의 스코프는 상위 스코프의 변수를 차단한다.
```php
$outerVariable = 'outer variable';
$writeOuterVariable = function () {
    echo "out of scope variable : ".$outerVariable;
};
$writeOuterVariable();
```
- 위의 코드에서 함수 내에서는 함수 스코프 바깥의 변수에 접근을 할 수 없다.
- 하지만 자바스크립트의 경우 상위 스코프의 변수를 하위 스코프에서 그대로 이용할 수 있다.
```js
const outerVariable = 'outer variable';
const writeOuterVariable = function () {
    console.log("out of scope scope variable : " + outerVariable);
};
writeOuterVariable();
```

### 상위 스코프의 변수 사용하기
- php에서 스코프 밖의 변수를 사용하기 위해서는 `use`을 사용한다.
```php
$outerVariable = 'outer variable';
$writeOuterVariable = function () use ($outerVariable) {
    echo "out of scope variable : ".$outerVariable;
};
$writeOuterVariable();
```
- `use`로 함수 스코프 밖의 변수 중에서 함수 스코프 안에서 사용할 변수를 지정하면 함수 내에서 사용할 수 있다.
- php에서 상위 스코프의 변수를 함수에서 사용하기 위해서는 **익명함수**로 만들어 줘야한다. 기명함수는 `use` 키워드를 사용할 수 없기 때문에 상위 스코프의 변수를 사용할 수 없다.

### 익명함수와 기명함수

### 함수와 스코프

