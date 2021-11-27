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
- 배열에 세팅된 원소 순서대로 값을 변수에 할당한다.
- `[$a, $b] = [1, 2]`라는 코드이면 $a는 1, $b는 2이다.

## Class constant 가시성
```
<?php
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```
- 클래스 내 const 불변값의 접근제한자 설정을 설정할 수 있다.

## iterable pseudo-type
```
<?php
function iterator(iterable $iter)
{
    foreach ($iter as $val) {
        //
    }
}
```

## Multi catch exception handling
```
<?php
try {
    // some code
} catch (FirstException | SecondException $e) {
    // handle first and second exceptions
}
```
- 파이프 `|` 기호를 사용해서 여러 예외 클래스를 하나의 catch 내에 세팅할 수 있다.


## list
```
<?php
$data = [
    ["id" => 1, "name" => 'Tom'],
    ["id" => 2, "name" => 'Fred'],
];

// list() style
list("id" => $id1, "name" => $name1) = $data[0];

// [] style
["id" => $id1, "name" => $name1] = $data[0];

// list() style
foreach ($data as list("id" => $id, "name" => $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as ["id" => $id, "name" => $name]) {
    // logic here with $id and $name
}
```
- []가 순서가 있는 배열을 순차적으로 구조 분해 할당을 받는 것과 달리, list는 순서가 없는 연관 배열도 구조 분해 할당을 받을 수 있다.

## 음수 문자열 오프셋 지원
```
<?php
var_dump("abcdef"[-2]);
var_dump(strpos("aabbcc", "b", -3));
```
- 문자열 오프셋을 적용하는 기준점의 디폴트는 첫번째 문자열이다. 첫번째 문자열을 기준으로 음수 오프셋을 적용하면 첫번째 문자열을 오프셋 0으로 하고, 문자열의 맨 뒤로 이동하게 되며 문자열의 맨 뒤는 오프셋 -1이 된다. 그래서`"abcdef"[-2]`의 경우는 a는 0, f는 -1, e는 -2가 된다.
- 첫번째 b의 인덱스는 2, 두번째 b의 인덱스는 3이다. 3번째 인자는 기준점의 시작점이다. 문자열 뒤에서 3번째인 오른쪽의 b에서 부터 b가 나오는 지점은 인덱스 3

## Convert callables to Closures with Closure::fromCallable() ¶
```
<?php
class Test
{
    public function exposeFunction()
    {
        return Closure::fromCallable([$this, 'privateFunction']);
    }

    private function privateFunction($param)
    {
        var_dump($param);
    }
}

$privFunc = (new Test)->exposeFunction();
$privFunc('some value');
```
- ??


## ext/openssl에서 AEAD 지원
## Asynchronous signal handling
## HTTP/2 server push support in ext/curl
## Stream Context Options

---

## Reference
- https://www.php.net/manual/en/migration71.new-features.php
