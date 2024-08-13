## 타입과 조건

### 타입 어셜선
- php에서 타입이란 assertion과 같은 역할을 한다. 기본적으로 php의 변수는 모든 형태의 타입을 다 가질 수 있으며, 타입힌트나 is_type과 같은 함수들은 변수 내에 포함되어 있는 값이 type인지 아닌지 확인하는 용도로 사용된다.

### 함수의 파라메터 타입
```php
$fn = function (int $a) {
    if ($a > 0) {
        echo '$a is a natural number.';
    } else {
        echo '$a is not a natural number.';
    }
};
```
- `$fn`으로 전달 되어야 하는 값은 어떤 값이 되어야 할까? 모든 정수 값이 다 전달될 수 있다.
