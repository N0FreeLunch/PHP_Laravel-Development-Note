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
        var_dump($this); // object(A)#1 (0) {}
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

## 일급 콜러블 문법 자세히 알아보기

> The first class callable syntax is introduced as of PHP 8.1.0, as a way of creating anonymous functions from callable. It supersedes existing callable syntax using strings and arrays. The advantage of this syntax is that it is accessible to static analysis, and uses the scope at the point where the callable is acquired.

- `callable`에서 익명함수를 생성하는데 사용된다.
- 기본의 `Closure::`을 사용한 방식과 다른 점은 문법이므로 정적 분석이 가능하다는 점이다.
- 그리고 `Closure::`을 사용한 방식이 바인딩할 객체나 클래스를 지정해 주어야 하는 반면, 이 문법을 사용한 시점의 대상을 바인딩 하므로 별도로 바인딩할 대상을 지정할 필요가 없다.

```php
class Foo {
   public function method() {}
   public static function staticmethod() {}
   public function __invoke() {}
}

$obj = new Foo();
$classStr = 'Foo';
$methodStr = 'method';
$staticmethodStr = 'staticmethod';

$f1 = strlen(...);
$f2 = $obj(...);  // invokable object
$f3 = $obj->method(...);
$f4 = $obj->$methodStr(...);
$f5 = Foo::staticmethod(...);
$f6 = $classStr::$staticmethodStr(...);

// traditional callable using string, array
$f7 = 'strlen'(...);
$f8 = [$obj, 'method'](...);
$f9 = [Foo::class, 'staticmethod'](...);
```

내장 함수를 클로저로 만들기
```php
$f1 = strlen(...);
```

`__invoke`가 정의된 내장 함수를 클로저로 만들기
```php
$f2 = $obj(...);
```

비 정적 메소드를 클로저로 만들기
```php
$f3 = $obj->method(...);
```

메소드명을 담은 변수로 메소드를 지정해서 클로저 만들기
```php
$f4 = $obj->$methodStr(...);
```

정적 메소드를 클로저로 만들기
```php
$f5 = Foo::staticmethod(...);
```

메소드명을 담은 변수로 정적 메소드를 지정해서 클로저 만들기
```php
$f6 = $classStr::$staticmethodStr(...);
```

내장 함수의 이름을 문자열에 담아 `callable`을 만들고 내장함수를 클로저로 만들기
```php
$f7 = 'strlen'(...);
```

배열의 0번 인덱스에 객체를, 배열의 1번 인덱스에 메소드명을 담아 `callable`을 만들고 메소드를 클로저로 만들기
```php
$f8 = [$obj, 'method'](...);
```

배열의 0번 인덱스에 클래스를, 배열의 1번 인덱스에 메소드명을 담아 `callable`을 만들고 정적 메소드를 클로저로 만들기
```
$f9 = [Foo::class, 'staticmethod'](...);
```

### 일급 콜러블의 접근 제한자 무시

#### 메소드를 배열 형태의 콜러블로 만들 때의 문제점

접근 제한자가 있는 메소드에는 접근할 수 없다. 아래의 예를 보자.

```php
class Foo {
    public function getPrivateMethod() {
        return [$this, 'privateMethod'];
    }

    private function privateMethod() {
        echo __METHOD__, "\n";
    }
}

$foo = new Foo;
$privateMethod = $foo->getPrivateMethod();
$privateMethod();
```

`$this`는 변수이다. 클래스 내부에서 사용하면 클래스로 인스턴스가 만들어졌을 때, 해당 인스턴스를 참조하는 변수이다.

`getPrivateMethod`는 메소드를 값으로 반환하는 함수이다. 메소드를 값으로 반환하기 위해서는 `callable` 타입의 값으로 만들어주어야 하고, 이를 위해서 `[$this, 'privateMethod']`를 반환한다.

`$foo->getPrivateMethod()`는 `[$this, 'privateMethod']`라는 값을 반환할 것이다. php에서 배열은 함수에 전달하고 반환할 때 기본적으로 복사가 되지만, 내부의 값이 참조의 방식이라면 참조로 유지된다. 이 때 반환된 배열의 `$this`는 `$foo` 객체에 대한 참조를 유지할 것이다.

문제는 `$foo` 객체에는 `privateMethod`란 접근할 수 있는 메소드가 존재하지 않는다. 왜냐하면 클래스에서 `private` 접근자로 메소드를 만들었기 때문에 객체에는 `privateMethod` 접근 가능한 대상이 없는 것이다. `[$this, 'privateMethod']`에서 `$foo` 객체의 `'privateMethod'` 메소드를 실행하고 싶지만 실행할 수가 없다. 따라서 에러가 발생한다.

> // Fatal error: Call to private method Foo::privateMethod() from global scope

> // This is because call is performed outside from Foo and visibility will be checked from this point.

이 때 php는 객체의 엑세스 할 수 없는 메소드에 대한 정보를 찾기 위해 오브젝트의 주형 클래스에 정의된 `Foo::privateMethod()`를 찾는다. 프라이빗 메소드이기 때문에 프라이빗 메소드에 대한 엑세스를 허가하는 객체 내부에서 실행하는 것이 아니라면 엑세스 할 수 없다는 의미이며, `$privateMethod()`으로 `[$this, 'privateMethod']`가 실행된 맥락은 글로벌 스코프이기 때문에 private 메소드에 대한 엑세스는 불가능하다는 에러를 낸다.

다음과 같이 퍼블릭 메소드인 경우 문제 없이 실행할 수 있다.

```php
class Foo {
    public function getPublicMethod() {
        return [$this, 'publicMethod'];
    }

    public function publicMethod() {
        echo __METHOD__, "\n";
    }
}

$foo = new Foo;
$publicMethod = $foo->getPublicMethod();
$publicMethod();
```

#### 일급 콜러블로 접근제한자 무시하기

일급 콜러블 문법으로 콜러블이 아닌 클로저로 만들면, 콜러블이 바인딩할 대상의 정보를 담고 있지 못하는 반면, 클로저는 바인딩할 대상의 정보를 담고 있기 때문에 클래스를 바인딩하고 있다면 클래스 정보에, 객체를 바인딩하고 있다면 객체 정보에 자유롭게 엑세스 가능하다. 다음의 `getPrivateMethod` 메소드는 콜러블을 반환하는 것이 아니라, 일급 콜러블 문법을 사용하여 클로저를 반환을 한다.

배열으로 메소드를 콜러블로 접근하는 방식은 외부에서 해당 메소드에 접근하는 방식이므로 접근할 수 없는 반면, 클로저로서 실행될 때는 마치 해당 객체를 내부에 속한 익명함수를 실행하는 것이 되어 접근 제한자로 가려진 대상에 접근할 수 있게 된다.

```php
class Foo1 {
    public function getPrivateMethod() {
        // Uses the scope where the callable is acquired.
        return $this->privateMethod(...); // identical to Closure::fromCallable([$this, 'privateMethod']);
    }

    private function privateMethod() {
        echo __METHOD__, "\n";
    }
}

$foo1 = new Foo1;
$privateMethod = $foo1->getPrivateMethod();
$privateMethod();  // Foo1::privateMethod
```
