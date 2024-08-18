## 함수와 조건

### 함수의 파라메터 타입
```php
$fn1 = function (int $a) {
    if ($a > 0) {
        echo '$a is a natural number.';
    } else {
        echo '$a is not a natural number.';
    }
};
```
- `$fn1`은 모든 범위의 정수를 받을 수 있다.
- 위의 코드는 phpstan 통과이다.
```php
$fn2 = function (int $a) {
    if ($a === 0) {
        echo '$a is a zero';
    } else {
        echo '$a is a natural number.';
    }
};
```
- 함수 `$fn2`는 모든 범위의 정수를 받을 수 있지만, 동작의 전제를 0이상으로 한다.
- 위의 코드는 phpstan 통과이다.
```php
$fn3 = function (int $a) {
    if ($a === 0) {
        echo '$a is a zero';
    } elseif ($a > 0) {
        echo '$a is a natural number.';
    }
};
```
- 위의 코드는 phpstan 통과이다.
- 함수 `$fn3`은 모든 범위의 정수를 받을 수 있지만, 음의 정수에 대해서는 처리가 일어나지 않는다. 기본적으로 위의 코드는 아무런 문제가 되지 않는다. 그러나 반환 타입이 정해지면 문제가 발생할 수 있다.
```php
$fn1 = function (int $a): string {
    if ($a > 0) {
        return '$a is a natural number.';
    } else {
        return '$a is not a natural number.';
    }
};
```
- 위의 코드는 phpstan 통과이다.
```php
$fn2 = function (int $a): string {
    if ($a === 0) {
        return '$a is a zero';
    } else {
        return '$a is a natural number.';
    }
};
```
- 위의 코드는 phpstan 통과이다.
```php
$fn3 = function (int $a): string {
    if ($a === 0) {
        return '$a is a zero';
    } elseif ($a > 0) {
        return '$a is a natural number.';
    }
};
```
- 위의 코드는 phpstan의 정적 분석을 통과하지 못한다.
- `$fn3`은 인자로 음의 정수가 전달되면 함수의 코드가 실행되어도 return 하는 대상이 없으므로 null이 반환된다. 하지만, 반환 값이 string으로 고정되어 있기 때문에 null은 타입 불일치로 인한 에러가 발생한다.
- 함수가 존재하는 맥락상 `$fn3`을 실행할 때 음의 정수가 들어오지 않는다는 암묵적인 가정이 있어 php의 코드로는 문제없이 실행되더라도, 정적 분석은 `$fn3`가 기본적으로는 모든 정수에 관해서 성립하는 것을 가정하기 때문에 정적 분석에 의한 에러가 발생한다.
```php
$fn3 = function (int $a): string {
    assert($a >= 0);
    if ($a === 0) {
        return '$a is a zero';
    } elseif ($a > 0) {
        return '$a is a natural number.';
    }
};
```
- `assert($a >= 0)`라는 코드가 추가 되었지만, 위의 코드는 phpstan의 정적 분석을 통과하지 못한다.
- assert에 의해 파라메터의 범위가 0이상으로 정해졌기 때문에, $a가 0인 것을 확인하는 것으로 나머지 값은 자연수가 된다. 정적 분석 툴은 `elseif ($a > 0)`을 불필요한 것으로 본다. 다음과 같은 코딩 스타일을 사용하여 코드를 표현할 수 있다.
```php
$fn3 = function (int $a): string {
    assert($a >= 0);
    if ($a === 0) {
        return '$a is a zero';
    }
    return '$a is a natural number.';
};
```
- 위의 코드는 phpstan 통과이다.
