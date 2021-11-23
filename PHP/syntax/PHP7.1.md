## Nullable types
```
<?php

function testReturn(): ?string
{
    return 'elePHPant';
}

var_dump(testReturn());

function testReturn(): ?string
{
    return null;
}

var_dump(testReturn());

function test(?string $name)
{
    var_dump($name);
}

test('elePHPant');
test(null);
test();
```
- 매개 변수 및 반환값의 타입선언에 ? 접두사를 붙여서 사용한다.
- 선언된 타입 선언 및 null 값도 매개변수나 반환값의 타입에 추가로 지정할 수 있다.

## void 타입
```
<?php
function swap(&$left, &$right): void
{
    if ($left === $right) {
        return;
    }

    $tmp = $left;
    $left = $right;
    $right = $tmp;
}

$a = 1;
$b = 2;
var_dump(swap($a, $b), $a, $b);
```
- 함수의 리턴 값이 없거나 `return;`, 함수의 리턴(return)을 지정하지 않는 방식으로 리턴하는 함수의 경우 void 타입을 사용한다.
- 함수의 실행 결과를 받을 때는 null을 반환하는 것으로 한다.
> Attempting to use a void function's return value simply evaluates to null, with no warnings emitted. The reason for this is because warnings would implicate the use of generic higher order functions. 이 부분 좀 더 분석

## 구조 분해 할당 (Symmetric array destructuring)
```
<?php
$data = [
    [1, 'Tom'],
    [2, 'Fred'],
];

// list() style
list($id1, $name1) = $data[0];

// [] style
[$id1, $name1] = $data[0];

// list() style
foreach ($data as list($id, $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as [$id, $name]) {
    // logic here with $id and $name
}
```

---

## Reference
- https://www.php.net/manual/en/migration71.new-features.php
