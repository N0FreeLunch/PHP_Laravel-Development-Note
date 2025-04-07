# Callable

## 함수는 문자열로 전달된다.

> A PHP function is passed by its name as a string. Any built-in or user-defined function can be used, except language constructs such as: array(), echo, empty(), eval(), exit(), isset(), list(), print or unset().

위의 설명은 'php에서 함수라는 것은 문자열로 전달된다'라고 말하고 있다. 문자열로 함수 이름을 전달하고, 함수 이름이 담긴 문자열을 `()`로 실행하면, 문자열에 매칭되는 함수가 실행이 된다.

php 엔진에서 제공하는 내장 함수, 유저가 정의한 기명 함수 모두 문자열로 실행할 수 있지만, 함수처럼 생겼지만 함수가 아닌 구조에 해당하는 array(), echo, empty(), eval(), exit(), isset(), list(), print or unset()은 문자열로 실행할 수 있는 대상이 아니다.

```php
function fnName() {
    var_dump("call: fnName");
};

fnName();
'fnName'();
```

함수 명칭을 그 자체로 함수를 실행할 수도 있지만, 함수명과 동일한 문자열을 실행하여 함수를 실행할 수 있다.

```php
function callStringParam(string $param) {
    $param();
}

callStringParam('fnName');
```

함수명을 파라메터로 전달할 때는 문자열로 전달해서 함수를 실행할 수 있다.

## 배열으로 메소드 실행하기

### 정적 메소드를 배열으로 실행하기

> Static class methods can also be passed without instantiating an object of that class by either, passing the class name instead of an object at index 0, or passing 'ClassName::methodName'.

클래스 이름의 문자열을 배열의 0번 인덱스 위치에, 정적 메소드를 배열의 1번 인덱스 위치에 배치된 배열은, 배열이기도 하지만 callable이기도 하다. '배열()'의 방식으로 이 배열을 실행할 수 있다.

```php
class A
{
    public static function staticMethod()
    {
        echo 'call: '.__METHOD__;
    }
}

['A', 'staticMethod']();
```

`A` 클래스의 `staticMethod` 정적 메소드를 배열을 사용한 `['A', 'staticMethod']()`을 통해서 실행할 수 있다.

### 객체 메소드를 배열으로 실행하기

> A method of an instantiated object is passed as an array containing an object at index 0 and the method name at index 1. Accessing protected and private methods from within a class is allowed.

오브젝트를 배열의 0번 인덱스 위치에, 메소드 이름을 1번 인덱스 위치에 배치된 배열은, 배열이기도 하지만 callable이기도 하다. '배열()'의 방식으로 이 배열을 실행할 수 있다.

이 때 배열의 1번 인덱스 위치의 메소드 이름의 문자열은 public 메소드 뿐만 아닌 private나 protected 등도 실행할 수 있다.

```php
class B
{
    public function objectMethod()
    {
        echo 'call: '.__METHOD__;
    }
}

[new B, 'objectMethod']();
```

`B` 클래스를 주형으로 하는 객체 `objectMethod` (비정적) 메소드를 배열을 사용한 `[new B, 'objectMethod']()`을 통해서 실행할 수 있다.

## 콜러블 타입

> Callbacks can be denoted by the callable type declaration.

callable 타입은 객체가 아니므로 `instanceof`로 타입을 판단할 수 없으며, 첫 글자가 대문자가 아니다. `string`, `int`, `array` 타입들과 같이 모두 소문자로 쓰여진 타입이다.

`is_callable` 함수는 callable 타입인지 확인하는 함수이다. 배열으로 객체의 메소드 또는 클래스의 정적 메소드를 호출하는 콜러블을 만들 수 있으므로 다음과 같은 배열은 `callable` 타입으로 판정된다.

```php
class A
{
    public static function staticMethod()
    {
        echo 'call: '.__METHOD__;
    }
}

var_dump(is_callable(['A', 'staticMethod'])); // bool(true)
var_dump(['A', 'staticMethod'] instanceof Closure); // bool(false)
```

```php
class B
{
    public function objectMethod()
    {
        echo 'call: '.__METHOD__;
    }
}

var_dump(is_callable([new B, 'objectMethod'])); // bool(true)
var_dump([new B, 'objectMethod'] instanceof Closure); // bool(false)
```

#### 클로저

```php
function callParam(callable $param, bool $printout = false) {
    if ($printout) {
        echo $param();
        return;
    }
    $param();
}

callParam(function () { var_dump('anonymous function param'); });

callParam(fn() => var_dump('arrow function param'));
```

`callable` 타입의 매개변수에 (Closure 타입의) 익명 함수와 화살표 함수를 전달할 수 있다.

#### 문자열

```php
function namedFunction() {
    var_dump(__FUNCTION__);
}

callParam('namedFunction');

callParam('phpversion', true);
```

익명 함수와 화살표 함수는 값이므로 그대로 함수의 인자로 전달할 수 있지만, 기명 함수는 값이 아니므로 그냥 전달 할 수 없고, 기명 함수 이름을 담은 문자열을 전달하여 이 문자열을 함수처럼 실행한다.

매개변수의 타입은 callable인데 문자열이 전달된다. 

```php
function callParam(callable $param, bool $printout = false) {
    if ($printout) {
        echo $param();
        return;
    }
    $param();
}


callParam('hello', true); // Fatal error: Uncaught TypeError: callParam(): Argument #1 ($param) must be of type callable, string given
```

전달된 문자열이 기명함수의 이름이 아닌 경우, callable로 판별되지 않기 때문에 에러가 발생한다.

### 배열과 정적 메소드

```php
function callParam(callable $param, bool $printout = false) {
    if ($printout) {
        echo $param();
        return;
    }
    $param();
}

callParam(['A', 'staticMethod']); // call: A::staticMethod
callParam(['A', 'not exists method']); // Fatal error: Uncaught TypeError: callParam(): Argument #1 ($param) must be of type callable, array given

class A
{
    public static function staticMethod()
    {
        echo 'call: '.__METHOD__;
    }
}
```

배열의 첫 번째 인덱스에 주형 클래스를, 두 번째 인덱스에 (정적이든 비정적이든) 메소드를 담은 배열의 형태일 때만 `callable` 타입으로 판단한다. 만약 존재하지 않는 클래스 또는 메소드 명을 배열에 넣은 경우에는 `callable`이 아닌 배열으로 판단한다.

### 배열과 비정적 메소드

```php
function callParam(callable $param, bool $printout = false) {
    if ($printout) {
        echo $param();
        return;
    }
    $param();
}

callParam([new A, 'nonStaticMethod']); // call: A::staticMethod
callParam([new A, 'not exists method']); // Fatal error: Uncaught TypeError: callParam(): Argument #1 ($param) must be of type callable, array given

class A
{
    public function nonStaticMethod()
    {
        echo 'call: '.__METHOD__;
    }
}
```

배열의 첫 번째 인덱스에 객체를, (정적이든 비정적이든) 두 번째 인덱스에 비정적 메소드를 담은 배열의 형태일 때만 `callable` 타입으로 판단한다. 만약 객체가 아니거나 존재하지 않는 메소드 명을 배열에 넣은 경우에는 `callable`이 아닌 배열으로 판단한다.

## 메소드를 값으로 사용하기

메소드는 값이 아니라서 다른 함수의 파라메터로 전달할 수 없다. 기명 함수의 경우 문자열로 전달해서 문자열을 호출하면 되지만, 메소드는 문자열로도 전달할 수 없기 때문에 배열의 0번 인덱스에 클래스 또는 객체를 담고 1번 인덱스에 메소드 또는 정적 메소드를 담아서 배열 형태로 메소드를 값으로 전달하는 특수 구문이 도입되었다.

## 메소드를 클로저로 만들기

```php
class A
{
    public function runMethodAsClosure()
    {
    	 $ClosureFromMethod = Closure::fromCallable([$this, 'methodTobeClosure']);
    	 $ClosureFromMethod('closure param');
    }

    private function methodTobeClosure($methodParam)
    {
        var_dump('run: '.__METHOD__); // string(25) "run: A::methodTobeClosure"
        var_dump($this); object(A)#1 (0) {}
        var_dump('method param: '.$methodParam); // string(27) "method param: closure param"
    }
}

(new A)->runMethodAsClosure();
```

`fromCallable`의 [사양](https://www.php.net/manual/en/closure.fromcallable.php)을 보면 `public static Closure::fromCallable(callable $callback): Closure`으로 되어 있다. `callable`로 판단되는 배열에 대한 지식이 없다면 `Closure::fromCallable([$this, 'methodTobeClosure'])`는 배열을 전달했는데 왜 실행이 되는지 이상하다고 생각할 것이다. 하지만, 객체와 메소드가 0번과 1번 인덱스에 할당된 형태로 `callable`에 해당하는 값으로 `fromCallable`에 할당할 수 있는 값이다. 메소드는 배열 형태의 `callable`이 아니면 값으로 전달할 수 없기 때문에 위와 같은 방법을 사용해서 메소드를 값으로 전달하였다.

### First class callable syntax 이용하기

PHP8.1 부터 일급 콜러블 문법이라는 `...` 스프레드 구문을 통해서 `callable`을 `Closure`로 만드는 문법이 도입되었다.

`callable`인 함수에 인자를 하나도 넣지 않고 `...`만 할당하면 동일하게 함수의 형태이면서 타입만 `callable`에서 `Closure`으로 바뀌게 된다. 이를 통해서 `Closure::`의 방식을 사용하지 않아도 간단하게 `callable`을 `Closure`으로 바꿀 수 있게 되었다.

```php
class A
{
    public function runMethodAsClosure()
    {
        $ClosureFromMethod = $this->methodTobeClosure(...);
        $ClosureFromMethod('closure param');
    }

    private function methodTobeClosure($methodParam)
    {
        var_dump('run: '.__METHOD__); // string(25) "run: A::methodTobeClosure"
        var_dump($this); // object(A)#1 (0) {}
        var_dump('method param: '.$methodParam); // string(27) "method param: closure param"
    }
}

(new A)->runMethodAsClosure();
```

#### 일급 콜러블 문법 자세히 알아보기
