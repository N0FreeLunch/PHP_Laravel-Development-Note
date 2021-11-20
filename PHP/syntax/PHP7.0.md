## 스칼라 타입 선언
- 스칼라 타입 선언에는 2가지 방법이 있다.
- 디폴트인 '강제 모드(coercive)'와 '엄격 모드(strict)'
- 강제 모드 (코얼시브 모드) : 전달된 값을 선언된 타입에 맞게 강제적으로 변환한다.
- 엄격 모드 : 전달된 값이 선언된 타입과 다르면 에러를 발생시키며 허용된 타입만 사용할 수 있다.

### 강제 모드의 예 (default)
```
<?php
function sumOfInts(int ...$ints)
{
    return array_sum($ints);
}
var_dump(sumOfInts(2, '3', 4.1));
```
- 문자열 `'3'`, 소수점 `4.1`으로 입력된 값을 강제적으로 int형으로 변환하는 역할을 한다.

### 엄격 모드
- 엄격 모드를 사용하기 위해서는 PHP문서의 PHP 코드를 입력할 수 있는 영역 최상단에 declare 함수를 선언해야 한다. 
```
<?php
declare(ticks=1);
```
- 엄격 모드의 선언 방식의 형태는 엄격모드의 선언이 파일단위로 이뤄진다는 것을 의미한다.
- 엄격 모드는 매개변수의 타입, 함수의 반환 값 타입, PHP 표준 함수, PHP의 확장 모듈 함수의 타입 등 모든 타입 영역에 영향을 준다.


## 반환값의 타입 선언
- 함수, 메소드 등의 반환값을 특정 타입으로만 반환 되도록 만든다.
- 인수(argument)의 타임선언과 동일하므로 강제 모드일 때는 형변환을 해서 반환하며, 엄격모드일 경우에는 반환된 타입이 다르면 에러가 발생한다.

```
<?php

function arraysSum(array ...$arrays): array
{
    return array_map(function(array $array): int {
        return array_sum($array);
    }, $arrays);
}

print_r(arraysSum([1,2,3], [4,5,6], [7,8,9]));
```


---

## Reference
- https://www.php.net/manual/en/migration70.new-features.php
