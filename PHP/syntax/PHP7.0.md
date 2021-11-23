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

## null 합체 연산자 (Null coalescing operator)
- isset() 을 사용하는 삼항연산자를 사용한 표현을 간단한게 하기 위한 문법적인 슈가로 추가 되었다.
#### 삼항 연산자를 사용한 표현의 예
```
$username = isset($_GET['user']) ? $_GET['user'] : 'nobody';
```
#### null 합체 연산자를 사용한 예
```
$username = $_GET['user'] ?? 'nobody';
```

## 우주선 연산자
```
echo 1 <=> 1; // 0
```
- 왼쪽에서 오른쪽을 뺐을 때 같으면 0
```
echo 1 <=> 2; // -1
```
- 왼쪽에서 오른쪽을 뺐을 때 음수이면 -1
```
echo 2 <=> 1; // 1
```
- 왼쪽에서 오른쪽을 뺐을 때 양수이면 1

```
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
```
- float 뿐만 아니라
```
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
```
- string도 가능

## 배열 상수
```
<?php
define('ANIMALS', [
    'dog',
    'cat',
    'bird'
]);

echo ANIMALS[1]; // outputs "cat"
?>
```
- 배열 상수 내부의 원소들을 추가, 변경, 제거 할 수 없다. (컴파일 에러 발생)

## 익명 클래스
```
<?php
interface Logger {
    public function log(string $msg);
}

class Application {
    private $logger;

    public function getLogger(): Logger {
         return $this->logger;
    }

    public function setLogger(Logger $logger) {
         $this->logger = $logger;
    }
}

$app = new Application;
$app->setLogger(new class implements Logger {
    public function log(string $msg) {
        echo $msg;
    }
});

var_dump($app->getLogger());
?>
```
- 이름 없는 클래스를 사용하고 있다.
- 객체를 사용해야 할 곳에 클래스를 선언함과 동시에 객체로 만들어 사용하고 있다.
- 인터페이스를 상속 받아 구현할 수 있다.
- 단, 인터페이스는 익명으로 만들 수 없다.

## 유니코드 이스케이프 구문
#### syntax
```
echo "\u{유니코드}";
```
- 문자열 내에 
#### example
```
echo "\u{aa}";
echo "\u{0000aa}";
echo "\u{9999}";
```

## Closure::call()
```
<?php
class A {private $x = 1;}

// Pre PHP 7 code
$getX = function() {return $this->x;};
$getXCB = $getX->bindTo(new A, 'A'); // intermediate closure
echo $getXCB();
```
- 함수에 객체를 바인딩하여 함수 내부에서 $this로 바인딩 된 대상을 호출할 수 있다.
- 바인딩 따로 함수 호출 따로

```
<?php
class A {private $x = 1;}

// PHP 7+ code
$getX = function() {return $this->x;};
echo $getX->call(new A);
```
- 함수에 객체를 바인딩하면서 호출할 수 있다.

## 필터링된 unserialize()
#### 모든 클래스를 불완전하게 언시리얼라이징 함
```
// converts all objects into __PHP_Incomplete_Class object
$data = unserialize($foo, ["allowed_classes" => false]);
```
#### 지정한 클래스만 완전하게 언시리얼라이징 함
```
// converts all objects into __PHP_Incomplete_Class object except those of MyClass and MyClass2
$data = unserialize($foo, ["allowed_classes" => ["MyClass", "MyClass2"]]);
```
#### 모든 클래스를 완전하게 언시리얼라이징 함
```
// default behaviour (same as omitting the second argument) that accepts all classes
$data = unserialize($foo, ["allowed_classes" => true]);
```

## IntlChar
```
<?php

printf('%x', IntlChar::CODEPOINT_MAX);
echo IntlChar::charName('@');
var_dump(IntlChar::ispunct('!'));
```
- PHP 유니코드 설정에 대한 수정 및 특수한 특징을 가진 유니코드를 획득할 수 있는 IntlChar 클래스가 PHP7.0에 추가 됨
- https://www.php.net/manual/en/class.intlchar.php 부분 참고
- CODEPOINT_MAX는 유니코드 번호의 최대 값 (intlchar 도큐먼트를 보면 확인 할 수 있다.) `10ffff`
- charName 메소드는 유니코드 문자의 이름을 검색 @의 시스템상 저장되어 있는 이름이 무엇인지 확인 가능하다. `COMMERCIAL AT`
- ispunct 메소드는 해당 문자가 구두점 문자인지 확인한다. 그래서 `bool(true)` 참인 값이 나온다.

## Expectations
```
<?php
ini_set('assert.exception', 1);

class CustomError extends AssertionError {}

assert(false, new CustomError('Some error message'));
?>
```

## use 그룹 선언
- 동일한 것에서 가져오는 클래스, 함수 및 상수 namespace 를 이제 단일 use명령문 으로 함께 그룹화할 수 있습니다.
#### 그룹으로 사용하지 않을 경우
```
<?php
// Pre PHP 7 code
use some\namespace\ClassA;
use some\namespace\ClassB;
use some\namespace\ClassC as C;

use function some\namespace\fn_a;
use function some\namespace\fn_b;
use function some\namespace\fn_c;

use const some\namespace\ConstA;
use const some\namespace\ConstB;
use const some\namespace\ConstC;
```

#### 그룹 문법을 사용 할 경우
```
<?php
// PHP 7+ code
use some\namespace\{ClassA, ClassB, ClassC as C};
use function some\namespace\{fn_a, fn_b, fn_c};
use const some\namespace\{ConstA, ConstB, ConstC};
?>
```
- 같은 경로를 가진 여러 네임스페이스에 하나의 닉네임을 사용할 수 있다.

## 제너레이터
```
<?php

$gen = (function() {
    yield 1;
    yield 2;

    return 3;
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL;
}

echo $gen->getReturn(), PHP_EOL;
```
- 제너레이터는 함수를 실행할 때 yield 지점에서 함수의 실행을 일시 정지하는 기능, 나중에 이 함수의 실행을 재개 할 수 있다.
- PHP에서 제너레이터를 실행할 때는 foreach 구문으로 사용한다. 위 예제에서 foreach 내부에 $val가 등장할 때 함수가 일시 정지된다.

## intdiv()
- 정수 나눗셈의 몫을 반환한다.
```
var_dump(intdiv(10, 3));
```
- 결과 값은 3




---

## Reference
- https://www.php.net/manual/en/migration70.new-features.php
