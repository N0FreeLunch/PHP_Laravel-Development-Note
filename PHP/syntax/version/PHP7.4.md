## 클래스 프로퍼티에 타입선언 추가
```
<?php
class User {
    public int $id;
    public string $name;
}
?>
```

## 화살표 함수
```
<?php
$factor = 10;
$nums = array_map(fn($n) => $n * $factor, [1, 2, 3, 4]);
// $nums = array(10, 20, 30, 40);
?>
```

## 공변(covariance) 리턴, 반공변(contravariance) 매개변수에 제한 걸기
```
<?php
class A {}
class B extends A {}

class Producer {
    public function method(): A {}
}
class ChildProducer extends Producer {
    public function method(): B {}
}
?>
```
- Full variance support is only available if autoloading is used. Inside a single file only non-cyclic type references are possible, because all classes need to be available before they are referenced.

## 널 병합 할당 연산자
```
<?php
$array['key'] ??= computeDefault();
?>
```
- 위의 코드는 
```
<?php
// is roughly equivalent to
if (!isset($array['key'])) {
    $array['key'] = computeDefault();
}
?>
```
- 이 코드와 거의 같다.

## Unpacking inside arrays
```
<?php
$parts = ['apple', 'pear'];
$fruits = ['banana', 'orange', ...$parts, 'watermelon'];
// ['banana', 'orange', 'apple', 'pear', 'watermelon'];
?>
```

## 숫자 리터럴 구분 기호
```
<?php
6.674_083e-11; // float
299_792_458;   // decimal
0xCAFE_F00D;   // hexadecimal
0b0101_1111;   // binary
?>
```
- 수를 표현하는데 밑줄을 포함해서 사용할 수 있다.
- 이는 개발자가 수를 볼 때 편하게 알아보기 위한 편의 기능을 추가한 것.
- 밑줄을 치든 치지 않든 수의 표현은 동일하다.

## Weak references
## Allow exceptions from __toString()
## CURL
## Filter 
## FFI 
## GD 
## Hash 
## Multibyte String
## OPcache 
## Regular Expressions (Perl-Compatible)

---

## Reference
- https://www.php.net/manual/en/migration74.new-features.php

