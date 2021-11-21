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
- 바인딩을 하면서 함수를 호출


---

## Reference
- https://www.php.net/manual/en/migration70.new-features.php
