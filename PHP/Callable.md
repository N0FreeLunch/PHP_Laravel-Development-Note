# Callable

## 함수는 문자열로 전달된다.

> A PHP function is passed by its name as a string. Any built-in or user-defined function can be used, except language constructs such as: array(), echo, empty(), eval(), exit(), isset(), list(), print or unset().

위의 설명은 'php에서 함수라는 것은 문자열로 전달된다'라고 말하고 있다. 문자열로 함수 이름을 전달하고, 함수 이름이 담긴 문자열을 `()`로 실행하면, 문자열에 매칭되는 함수가 실행이 된다.

php 엔진에서 제공하는 내장 함수, 유저가 정의한 기명 함수 모두 문자열로 실행할 수 있지만, 함수처럼 생겼지만 함수가 아닌 구조에 해당하는 array(), echo, empty(), eval(), exit(), isset(), list(), print or unset()은 문자열로 실행할 수 있는 대상이 아니다.

## 배열으로 메소드 실행하기

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
var_dump([new B, 'objectMethod'] instanceof Closure); // bool(true)
```

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
