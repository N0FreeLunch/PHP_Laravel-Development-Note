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

### callable 하위 타입

```php
function callParam(callable $param) {
	$param();
}


callParam(function () { var_dump('anonymous function param'); });

callParam(fn() => var_dump('arrow function param'));
```

`callable` 타입의 매개변수에 (Closure 타입의) 익명 함수와 화살표 함수를 전달할 수 있다.

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
        var_dump('run: '.__METHOD__);
        var_dump($this);
        var_dump('method param: '.$methodParam);
    }
}

(new A)->runMethodAsClosure();
```
