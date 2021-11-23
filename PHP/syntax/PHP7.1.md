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
