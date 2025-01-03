# 스코프란?

스코프란 변수가 동작하는 유효 범위를 의미한다.

## php에서 스코프

php에서 스코프는 함수 내부를 스코프로 한다. `function () { }`에서 중괄호 안이 스코프의 범위이다.

```php
function () {
    echo "no variable";
    $variable = "value";
    echo "variable has $variable";
}
```

위의 코드에서 `$variable`이란 변수의 스코프는 변수가 초기화 된 위치 `$variable = "value";` 부터 함수가 끝나는 `}`까지이다. `{}` 안에서 선언된 `$variable` 변수는 `{}` 밖에서는 존재하지 않는 변수가 된다. 변수가 선언된 부분에서 중괄호가 닫히는 부분까지가 변수의 유효범위이고 이 범위가 스코프에 해당한다.

코드의 실행에 따라 변수가 살아 있는 범위를 스코프라고 한다. 하지만 일반적으로 어떤 변수를 선언할 수 있고, 선언된 변수가 사라지는 코드 실행 지점까지인 함수의 괄호 `{}`를 스코프라고 부른다.

## 상위 스코프의 변수 차단

많은 언어에서 함수의 스코프는 상위 스코프의 변수를 차단한다.

```php
$outerVariable = 'outer variable';
$writeOuterVariable = function () {
    echo "out of scope variable : ".$outerVariable;
};
$writeOuterVariable(); // Warning: Undefined variable $outerVariable
```

위의 코드에서 함수 내에서는 함수 스코프 바깥의 변수에 접근을 할 수 없다. php8에서는 경고가 반환되고 echo 키워드에 의해 `$outerVariable`가 빈 문자열이 되어 `out of scope variable : `라는 출력이 된다. php9 부터는 정의되지 않은 변수의 경우 에러가 발생하고 `echo` 부분의 코드는 결과를 내지 못한채 종료된다.

### 상위 스코프의 변수를 캡쳐하기

스코프 안에서 스코프 밖의 변수를 사용하는 것을 캡쳐라고 부른다. php의 함수에서 캡쳐를 하기 위해서는 `use` 키워드를 사용한다.

```php
$outerVariable = 'outer variable';
$writeOuterVariable = function () use ($outerVariable) {
    echo "out of scope variable : ".$outerVariable;
};
$writeOuterVariable();
```

`use`로 함수 스코프 밖의 변수 중에서 함수 스코프 안에서 사용할 변수를 지정하면 함수 내에서 사용할 수 있다.

#### 익명함수만 캡쳐 할 수 있다.

php에서 상위 스코프의 변수를 함수에서 사용하기 위해서는 **익명함수**로 만들어 줘야한다. 기명함수는 `use` 키워드를 사용할 수 없기 때문에 상위 스코프의 변수를 사용할 수 없다.

```php
$outerVariable = 'outer variable';
function writeOuterVariable() use ($outerVariable) {
    echo "out of scope variable : ".$outerVariable;
};

writeOuterVariable();
```

`writeOuterVariable`는 위에서 기명함수이다. 기명함수에 `use` 키워드로 외부 변수를 캡쳐하는 것은 에러를 반환한다.

## 클로저

### php에서 클로저

php에서 함수의 타입으로 Closure를 사용할 수 있는데, 주로 함수를 매개 변수로 받거나 함수의 반환 값으로 함수를 반환할 때 타입힌트로 사용된다.

```php
$phpClosure = function(Closure $fnParam): Closure {
    return function () use ($fnParam) {
        $fnParam();
    };
};

$returnedClosure = $phpClosure(function () {
    echo "hello";
});

var_dump($returnedClosure);

$returnedClosure();
```

Closure 타입으로 선언된 파라메터에 `function () { echo "hello"; }` 함수가 전달된다.

Closure 타입으로 선언된 반환 타입에 `function () use ($fnParam) { $fnParam(); }` 함수가 반환된다.

반환된 클로저를 `var_dump`로 `var_dump($returnedClosure)` 부분에서 확인해 보면 `object(Closure)#3 (1) { ["static"]=> array(1) { ["fnParam"] => object(Closure)#2 (0) { } } }`가 된다. 함수는 Closure 타입을 주형으로 한 오브젝트를 반환하고, `use` 키워드로 캡쳐한 함수를 함수 내부에 스테틱 멤버로 저장을 하고 있는 것을 알 수 있다.

### 일반적인 의미에서의 클로저

일반적으로 다른 언어에서 클로저(closure)라고 하는 것은 함수가 외부의 변수를 캡쳐 하는 것이다. php에서 함수에 `use` 키워드로 외부 변수를 내부에서 이용할 수 있도록 하는 것이 클로저이다.

```php
$phpClosure = function(Closure $fnParam): Closure {
    return function () use ($fnParam) {
        $fnParam();
    };
};
```

반환 함수 내부에서 사용하기 위해서 외부의 변수를 가져오는 것을 캡쳐라고 한다. 위 예에서 `$fnParam` 변수는 반환 함수에 캡쳐된다. 이를 다른 언어에서 클로저라고 부르는 것이다.

### 자바스크립트에서 클로저

php에서 함수에 `use` 키워드로 함수 밖의 변수를 캡쳐해야 함수 내부에서 외부 변수를 사용할 수 있는 것에 반해 자바스크립트의 경우 마치 자동으로 캡쳐링되는 것 처럼 상위 스코프의 변수를 하위 스코프에서 그대로 이용할 수 있다는 특징이 있다.

```js
const outerVariable = 'outer variable';
const writeOuterVariable = function () {
    console.log("out of scope scope variable : " + outerVariable);
};
writeOuterVariable();
```

`writeOuterVariable` 변수에 저장된 익명 함수 내부의 `outerVariable`는 특별히 외부의 변수를 캡쳐하지 않았지만, 외부의 변수에 접근 가능하다.

## 익명함수와 기명함수

php에는 익명 함수와 기명함수가 있다. 익명 함수는 `function() {}`의 형태를 갖고 있으며, 기명함수는 `function name() {}`의 형태를 갖고 있다.

익명함수와 기명함수의 큰 차이점은 익명함수가 값으로 사용될 수 있는 대상인 것에 반해, 기명함수는 값으로 사용할 수 없는 문법이다.

### 익명함수

클래스와 객체를 이용한 코딩 위주라면 기명함수는 거의 사용할 일이 없고, 익명함수를 주로 사용하는데, 클로저를 받는 매개변수에 익명함수를 전달하거나 (`fn(function () {});`), 변수에 익명함수를 저장거나 (`$fn = function () {};`), 변수를 호출하는 방식(`$fn()`)으로 사용한다. 익명 함수는 즉시 실행함수 `(function() {})();`로도 사용할 수 있다.

익명함수의 함수는 php에서 값의 형태로 변수에 할당 가능하며 변수가 전달될 수 있는 모든 곳에 전달 가능한 일급 시민이다. php에서 일급 시민에 해당하는 값 뒤에는 `;` 문법의 종결 의미인 세미콜론을 붙여준다. 

### 기명함수

기명함수는 변수에 값으로 할당될 수 없는 특성을 가지며 변수에 할당하지 않으므로 변수를 사용한 호출인 `$`를 사용한 변수명으로 호출하지 않고 함수에 지어준 이름 그대로 사용한다. 이는 php에서 변수에 할당할 수 있는 값인 일급시민이 아닌 특수한 문법으로 값이 아닌 구문이기 때문에 세미콜론을 붙이지 않는다. 클래스는 값이 아니라 구문이기 때문에 클래스 뒤에 세미콜론을 붙이지 않는 것과 동일하다. `function name() {}`는 맞지만 `function name() {};`는 맞지 않다. 

자바스크립트의 기명함수가 값으로 취급되어 `(function fn() { console.log('hello') })()`와 같이 즉시 실행 함수로 실행할 수 있는 것과 달리 php의 기명함수는 값이 아닌 문법이기 때문에 `(function fn() { echo "hello"; })()`와 같은 방법으로 사용할 수 없다. 이에 반해 익명 함수는 값이므로 `(function () { echo "hello"; })()`와 같이 즉시 실행 함수로 실행할 수 있다.

#### 기명함수에서 전역변수 사용하기

기명함수는 외부 스코프의 변수를 사용할 수 없으며 `use` 키워드도 사용할 수 없다. 기명함수에서 사용할 수 있는 외부 스코프의 변수는 '전역변수'뿐이다. `const`로 선언된 상수 또는 글로벌 스코프에서 선언된 변수만을 `global` 키워드를 사용하여 기명 함수 내부에서 사용할 수 있으며, 어떠한 (전역 변수가 아닌) 지역변수도 기명함수 스코프 밖에 있다면 사용할 수 없다.

```php
$glovalVariable = 'hello';

function namedFunction() {
    $localVariable = 'world';

    function namedNestedFunction() {
        global $glovalVariable, $localVariable;
        echo "$glovalVariable $localVariable"; // hello 
    };

    namedNestedFunction();
}

namedFunction();
```

글로벌 변수에는 `'hello'`라는 값의 변수가 있고, 지역 변수에는 `'world'`라는 값의 변수가 있다. 함수 안에 내포된 기명함수를 호출했을 때, 전역 변수의 값 `hello `는 (공백 포함으로) 출력 되는 반면, 지역 변수의 값 `'world'`의 값은 전역변수의 값이 아니기 때문에 가져올 수 없어서 `null`이 되어 출력되지 않는다.

`namedNestedFunction` 함수는 기명함수이므로 `use` 키워드를 사용할 수 없고 외부 스코프의 변수를 함수 내부로 캡쳐 할 수 없다.

## 함수형 프로그래밍

현대의 많은 프로그래밍 언어들은 함수형 프로그래밍 기법을 도입하고 있는데, 가장 기본이 되는 문법적 특징은 함수가 값으로 취급되는 일급 시민이어야 한다는 것이다. php에서 익명함수는 값으로 취급되기 때문에 함수형 프로그래밍 기법을 활용할 수 있게 해 준다. 반면에 기명함수의 경우 값으로 취급되지 않기 때문에 일반적으로 함수형 프로그래밍으로 다룰 대상은 아니지만, php만의 특별한 방식을 통해서 기명함수를 함수의 인자로 전달할 수 있다.

### 문자열로 기명함수 호출하기

```php
function namedFn() {
    echo "hello";
}

namedFn();
'namedFn'();
```

기명 함수명을 키워드로 하여 기명함수를 호출할 수도 있지만, 문자열에 `()`를 붙여주는 것을 통해서 기명함수를 호출할 수 있다.

#### 기명함수의 타입힌트

```php
function namedFn() {
    echo "hello";
};

function callFn(Callable $fn) {
	var_dump($fn);
    $fn();
}

callFn('namedFn');
callFn('undefinedNamedFn'); // Fatal error: Uncaught TypeError: callFn(): Argument #1 ($fn) must be of type callable, string given
```

`Callable` 타입힌트를 통해서 기명함수를 전달 받을 수 있다. 기명함수는 호출 가능한 객체인 `Closure`가 아니므로 `Closure` 타입으로 받을 수 없다. `Callable` 타입에는 기명함수 이름을 문자열로 전달하는데, 만약 존재하지 않는 기명함수라면 `must be of type callable, string given`라는 에러가 발생하며, 전달된 문자열을 문자열로 판별한다. 하지만 존재하는 기명함수라면 `Callable` 타입으로 전달이 되며, 전달된 후의 매개변수를 확인하면 문자열 타입이지만, 존재하는 기명함수 이름이므로 해당 문자열에 `()`를 붙여 기명 함수를 호출할 수 있다.

`Callable`타입의 하위 타입이 `Closure` 타입이므로 `Callable` 타입은 기명함수, 익명함수 모두 받을 수 있지만, 하위 타입인 `Closure`는 좀 더 좁은 익명함수만 받을 수 있다.

### 익명 함수를 함수의 인자로 전달하기

```php
$anonymousFn = function () {
    echo "hello";
};

function callFn($fn) {
    $fn();
}

callFn($anonymousFn);
$anonymousFn->__invoke();
```

php에서 익명 함수는 호출 가능한 오브젝트이다. 클래스를 주형으로 오브젝트를 만들 때 `__invoke`라는 메소드를 사용하면 객체에 `()`를 붙여주는 것을 통해서 호출 할 수 있다. 그래서 `$anonymousFn->__invoke();` 방법으로도 익명 함수를 실행할 수 있는 것을 알 수 있다.

### 기명 함수를 함수의 인자로 전달하기

```php
function namedFn() {
    echo "hello";
};

function callFn($fn) {
    $fn();
}

callFn('namedFn');
```

기명함수는 오브젝트가 아니다. 익명함수와 같이 `'namedFn'->__invoke();`와 같은 방식으로 호출할 수 없다. 값이 함수인 경우에 호출 가능한 대상인데, php는 기본적으로 문자열에 `()`를 붙여주는 것을 통해서 기명 함수를 실행하는 특수한 문법을 가지고 있다. 이는 특수한 동작 방식이며, 어디까지나 문자열이기 때문에 익명함수와 같은 호출 가능한 객체가 아니라서 `__invoke` 메소드를 가지고 있지 않다.

기명 함수를 함수의 인자로 전달하는 것은 문자열을 다뤄야 하기 때문에 익명 함수와 같이 함수 내부에 어떤 조작을 할 수 없다. 곧 문자열이 가리키는 기명함수를 실행하는 것 외에 다른 역할을 하지 못한다.

### 기명함수와 순수함수

익명함수가 호출 가능한 오브젝트로 `__invoke` 메소드를 가지고, 캡쳐한 변수의 이름을 정적 멤버명으로 해당 변수의 값을 값으로 갖는 것에 반해, 기명 함수는 특별한 값이 아니라서 호출만 할 수 있는 대상이다. 이런 기명함수는 변수를 캡쳐링하지도 않기 때문에 순수함수로서의 역할을 할 수 있다. 물론 기명함수는 내부에 `global` 키워드로 전역변수를 가져다 쓸 수 있지만, `global` 키워드도 사용하지 않는다면 순수함수에 해당한다.

또한 기명함수는 깔끔한 재귀적인 코드를 만드는 것이 가능한데, 기명함수와 익명함수의 재귀 코드를 비교해 보자.

```php
function factorial($n) {
    if ($n <= 1) {
        return 1;
    } else {
        return $n * factorial($n - 1);
    }
}

var_dump(factorial(5));
```

```php
$factorial = function ($n) use (&$factorial) {
    if ($n <= 1) {
        return 1;
    } else {
        return $n * $factorial($n - 1);
    }
};

var_dump($factorial(5));
```

기명 함수는 함수 내에서 자기 이름의 함수를 호출하기 쉽기 때문에 재귀 코드를 좀 더 간결하게 짜는 기능을 제공한다. 익명함수를 할당한 변수명을 함수 내부로 전달하려면 참조 형식으로 전달해 줘야 하는데 이러한 문법을 사용하는 것은 직접 코드의 사용 방식을 견식하지 않는 이상 떠올리기 어렵다.

`use` 키워드로 변수를 캡쳐할 때는 변수가 정의되든 정의되지 않든 캡쳐할 수 있다. 하지만 캡쳐한 대상을 함수 내부에서 사용하기 위해서는 값이 존재해야 하는데, 값을 복사를 하면 정의되지 않은 값이 복사가 되므로 참조로 변수의 주소만 가리키도록 한다. `use` 키워드는 기본적으로 값을 복사하며, 객체의 경우 참조하는 동작을 한다. 익명함수가 정의되는 시점에서 `$factorial`는 아직 정의되지 않은 값이기 때문에 복사로 값을 가져올 수 없으므로 참조로 변수만을 가리키도록 하며, 이 익명함수 정의되어 `$factorial` 변수에 할당이 된 상태에서는 `$factorial`의 익명함수가 호출이 될 때 참조로 캡쳐한 `$factorial` 변수에 익명함수를 할당하였으므로 `$factorial`이 호출 가능한 대상이 되어 실행할 수 있게 된다.

OOP나 오토로딩을 통한 네임스페이스를 적극 사용하고 있다면 전역변수를 거의 사용하지 않을 것이고 이러한 상황에서 지역 변수를 캡쳐할 수 없고 전력 변수만 함수 내부에서 사용할 수 있는 기명함수는 순수함수의 표현으로 사용될 수 있다. 또한 재귀코드의 사용도 기명함수에 비해 쉽다. 심지어 멤버 메소드의 경우 `$this->member` `self::member`으로 접근할 수 있기 때문에 메소드가 클래스 내부의 값으로 오염될 수 있는 반면 기명함수는 이런 염려도 없다. 따라서 기명 함수를 사용하여 순수 함수라는 것을 표현해 보는 것도 좋아보인다.

### 기명함수의 단점

기명함수는 치명적인 단점이 있는데, 지역 스코프 내에서 선언이 되더라도 지역 스코프에 위치하지 않고 글로벌 스코프에 위치하게 된다. 스코프는 변수의 유효범위를 설정하는 것인데, php의 기명함수는 익명함수와 달리 변수에 저장되는 대상이 아니기 때문에 스코프에 영향을 받지 않는다. 이런 문제를 회피하기 위해 기명함수를 선언하면 항상 전역에서 호출할 수 있는 대상이 된다.

```php
class PureFunctionTest
{
    private string $hello = 'hello';

    public function method()
    {
        function namedFunction() {
            return $this->hello;
        }
        return namedFunction();
    }
}

echo (new PureFunctionTest)->method();
```

위 코드에서 기명함수(`namedFunction`) 내에서 `$this->hello`으로 객체의 멤버에 접근하는 코드는 에러를 발생시킨다. 기명함수는 객체 내부에서 정의되더라도 객체의 값에 접근하지 못하는 특징을 가지므로 
순수함수의 표현으로 유용하다.

### 지역 스코프에 기명함수 정의 금지

기명함수는 순수 함수의 특성을 갖는 대상을 나타낼 때 유용하지만, 정적 검사 툴을 이용하면 기명함수는 오래된 스타일이므로 기명함수 보다는 익명함수를 사용하는 것을 요구하는 메시지가 나온다. [참고](https://github.com/phpstan/phpstan/issues/165) `Creating a function within {object} could be a code smell. Consider refactoring`라고 제한을 두었는데 그 이유는 기명함수는 지역 스코프에 제한되지 않으며 어디서든 불러서 사용할 수 있는 전역 스코프와 비슷한 특성을 가지고 있기 때문이다. 로컬에서 선언했지만 지역 스코프는 무시되고 어디서나 호출할 수 있는 대상이 되므로 지역 스코프에 기명함수를 사용하는 것은 지역 스코프 내에서 사용할 수 있다는 인식을 하게 될 수 있다. 더구나 함수 등으로 기명함수를 정의한 코드를 2번 이상 호출하게 되면 함수가 충돌하는 문제가 발생한다.

기명함수를 사용할 때 `if (!function_exists('namedFunction')) { function nameFunction() {} }`으로 코드를 사용하는 이유는 기명함수가 두 번 선언되어 충돌이 발생할 수 있어서이다. 애초에 기명함수가 충돌하는 이유는 개발자도 모르게 두 번 어디선가 함수가 호출이 되었다는 것인데, 기명함수만을 따로 모아서 코드 구조를 개선하고 이유도 모른채 동일한 이름의 기명함수가 있을 수 있으니까 확인하는 코드를 짜는 것은 좋은 방식은 아니다.

## 함수와 스코프

### 자바스크립트의 스코프

자바스크립트의 변수는 `const`나 `let`으로 선언되면, 블록 스코프를 갖는다. 함수인지 관계 없이 중괄호 `{}`가 변수의 스코프가 된다. `var`나 선언 키워드 없이 사용된 변수의 경우 `function` 함수의 스코프를 갖는다. 그래서 조건문이나 반복문의 중괄호 안에 사용된 변수도 스코프를 갖는다.

### php의 스코프

자바스크립트와 달리, 그리고 대부분의 언어에서는 블록 스코프를 사용하지 않고, 함수 스코프를 사용한다. 조건문이나 반복문 안의 중괄호가 변수의 스코프가 되지 않는 것은, php의 변수가 함수 스코프를 갖기 때문이다.

```php
if(true) {
    $innerIf = 'hello';
}
echo $innerIf;
```

블록 내에서 선언한 변수가 소멸되지 않고 블록 밖에서도 소멸하지 않았는데, 이는 함수 스코프내에서 선언된 변수가 아니기 때문에 전역 변수로 선언된 것이라서 그렇다.

### 로컬 선언 키워드 없음

php는 로컬 스코프에서 변수를 선언하는 키워드는 존재하지 않는다.

#### 변수 선언 키워드가 없다는 것

```php
(function () {
    const hello = 'hello';
    readonly string $world = 'world';
    echo hello + $world;
})();
```

글로벌 변수나 클래스의 멤버로 변수를 선언할 때는 `const`를 사용할 수 있고, 클래스의 멤버로 변수를 선언할 때는 재할당 불가 키워드인 `readonly`와 타입을 지정하는 키워드인 `string`을 사용할 수 있다.

그러나 로컬 스코프에서는 이러한 로컬 변수에 선언할 수 있는 키워드가 없다. php 언어 스펙을 결정하는 사람들은 로컬 스코프에서의 선언 키워드 도입에 부정적인데, 클래스의 멤버 변수로 변수 선언 키워드를 사용할 수 있기 때문에 로컬 스코프에서의 선언 키워드를 대체할 수 있는 상황에서 이러한 문법의 도입이 php 파서의 복잡성을 늘리는 것에 비해 이점이 크지 않다는 의견이다.

하지만 로컬 스코프에서 이러한 변수를 제한하는 키워드가 없다는 것은 코드가 길어지면 길어질수록 변수가 재선언 된다던지, 다른 타입의 값이 할당된다던지 하는 상황이 자주 일어날 수 있고, 변수의 변화 과정을 추적하기 어려워지는 등의 문제를 마주할 수 있다.

타입스크립트와 같이 여러 타입을 변수에 선언할 수 있다면 변수가 이 타입 아니면 저 타입이라는 것을 알 수 있지만, php의 클래스 멤버 변수가 아닌 일반 변수인 경우에는 하나의 타입이 아니면 또 무슨 타입을 쓰는지 알 수 없는 문제가 생기기 때문에 php에서는 변수에 타입 하나와 필요에 따라 null 타입을 사용하는 방식으로 변수의 타입 사용을 줄여 혼동을 줄이는 방식을 사용한다.

자바스크립트의 경우, 함수 또는 블록의 바깥의 모든 상위 스코프의 변수를 내부에서 사용할 수 있기 때문에 재할당 방지 키워드가 없으면 어느 순간 로컬 스코프의 변수 조작으로 상위 스코프의 값이 바뀔 가능성이 높다. 자바스크립트의 경우는 재할당 방지 키워드가 필수이다. 하지만 php의 경우 함수 스코프에 상위 스코프의 변수를 사용하기 위해서는 `use` 키워드로 가져와야 하기 때문에 상위 스코프의 변수를 함부로 바꾸게 되는 경우가 드물다. 그리고 `use` 키워드로 가져온 값은 객체가 아니라면 일부러 참조 문법을 사용하지 않는 한 복사가 되므로 의도적으로 참조를 사용하거나 객체를 변경하는 작업을 하지 않는다면 상위 스코프의 변수가 함부로 변경되지 않아서 자바스크립트에 비해서는 필요성이 덜하다.

```php
$message = ['hello'];
$function = function () use ($message) {
    array_push($message, 'world');
};

$function();

var_dump($message);
```

위의 예제에서 배열을 `use` 키워드로 함수 내부로 가져와서 변경을 했지만, 함수 외부에서 출력되는 결과값은 `['hello']`으로 `'world'`가 원소로 추가되지 않은 것을 알 수 있다. 이는 `use` 키워드로 함수 내부로 스칼라 값을 가져올 때 복사가 되었기 때문에 (객체라면 참조가 된다) 함수 내부에서는 변경 되었지만, 함수 외부에서는 변경되지 않은 것이다.

php는 자바스크립트에 비해 상위 스코프의 변수의 오염의 염려가 적고 통제 가능하기 때문에 특별한 문제가 없을 수는 있지만, 로컬 스코프 변수 선언 키워드가 없기 때문에 한 스코프 내의 코드가 길수록 변수를 통제하기 어려운 특성이 있다. 따라서 변수에 특별한 제약을 하지 않더라도 충분히 통제할 수 있는 단위의 코드를 작성할 수 있도록 함수의 기능을 작은 단위로 쪼개어 사용해야 한다.

### 함수를 통한 변수 격리

php는 로컬 스코프에서 변수를 제한하는 기능을 제공하지 않기 때문에 한 스코프에 많은 코드를 넣게 되면 변수의 상태를 추적하기 어려워진다. 따라서 php에서 권장하는 코딩 스타일은 하나의 스코프에 너무 많은 변수를 사용하지 말고 함수나 메소드를 사용해서 별도의 스코프로 분리될 수 있는 대상은 별도의 스코프에 넣어서 한 스코프에 존재하는 변수의 수를 줄이는 코딩이 필요하다.

일반적인 코딩 프렉티스로 함수 사용을 남발하지 말고 코드가 심플하다면 별도의 추상화 없이 코드를 쓰는 게 낫다는 의견이 있다. 하지만 php는 변수에 부여할 수 있는 키워드가 제한되므로 변수의 스코프 격리를 해 주는 것을 통해서 코드를 좀 더 명확하게 알 수 있다는 장점이 있으므로 함수를 통한 스코프 분리는 상당히 의미있는 코딩 스타일이라고 본다.

### 화살표 함수

php에서 화살표 함수는 자바스크립트 처럼 상위 스코프의 변수를 자유롭게 받을 수 있는 특징이 있다.

```php
$hello = 'hello';
echo (fn($var) => "$hello $var")('world');
```

화살표 함수는 `return`을 하기 전에 미리 처리하는 기능 없이 `=>`이후의 코드를 `return`하는 함수이며 스코프를 나타내는 중괄호 `{}`가 없는 코드이다.

함수의 스코프가 없는 대신에 화살표 함수를 사용하고 있는 스코프의 변수를 그대로 사용할 수 있다.

단, 스코프가 존재하는 대상이 있는데, 화살표 함수의 매개변수는 스코프가 존재하며 화살표 함수가 반환된 이후 화살표 함수의 매개변수는 수명을 다한다. 물론 클로저가 반환되면 매개변수는 좀 더 오래 생존할 수 있다.

`function() use () {}` 대신에 `fn() {}`을 사용하여 자바스크립트처럼 외부 스코프의 변수를 자동으로 캡쳐하는 기능을 제공하자는 [RFC](https://wiki.php.net/rfc/auto-capture-closure)
가 제안 되었다. 투표로 다수의 동의는 얻었으나 아직 구현 예정이라는 소식은 없다. 간단히는 `fn` 키워드는 외부 변수를 자동으로 캡쳐링하는 기능을 제공한다고 보면 된다. [참고](https://stitcher.io/blog/why-we-need-multi-line-short-closures-in-php)

## [Closure 클래스](https://www.php.net/manual/en/class.closure.php)

php의 클로저 클래스에 대해 좀 더 이해해 보자.

```php
final class Closure {
    /* Methods */
    private __construct()
    public static bind(Closure $closure, ?object $newThis, object|string|null $newScope = "static"): ?Closure
    public bindTo(?object $newThis, object|string|null $newScope = "static"): ?Closure
    public call(object $newThis, mixed ...$args): mixed
    public static fromCallable(callable $callback): Closure
}
```

클로저 클래스는 final 키워드로 정의되어 있으므로 상속을 할 수 없다. 곧, 사용자 정의 클래스로 익명함수를 만들 수 없다는 것을 의미한다. 또한 생성자의 접근 제한이 `private`이므로 `new` 키워드를 사용한 인스턴스화도 할 수 없다.

앞서 익명함수를 `var_dump`로 확인해 보면 `object(Closure)#3 (1) { ["static"]=> array(1) { ["fnParam"] => object(Closure)#2 (0) { } } }`로 되어 있지만, 캡쳐한 대상을 클래스의 정적 멤버에 추가하는 등의 방식은 사용할 수 없다.

클래스에 `__invoke` 메소드를 정의해서 인스턴스화 된 객체를 함수로 사용할 수는 있지만 이는 클로저 객체를 상속한 것이 아니고 상속할 방법이 있는 것도 아니므로 익명함수로 만들 수는 없다.

### 콜러블을 익명함수로 바꾸기

php의 내장함수 또는 기명함수는 호출할 수 있는 대상 callable으로 분류된다. 하지만 Closure 클래스 기반의 익명함수는 아니다. 타입 관계로는 Callable 클래스의 하위 타입이 Closure에 해당한다. 곧, callable 타입힌트에 Closure 타입의 값을 전달할 수 있다.

Closure가 아닌 callable인 php의 내장함수 또는 기명함수를 익명함수로 바꿀 수 있는데 다음을 보자.

```php
$numbers = [1,2,3,4,5];

$map = Closure::fromCallable('array_map');

$result = $map(fn($v) => $v+5, $numbers);

var_dump($result);
```

`array_map`이라는 내장 함수는 `callable`이다. 이를 `Closure::fromCallable()`에 전달하여 `$map`이라는 익명함수를 얻는다.

### `__invoke`가 있는 객체를 익명함수로 만들기

```php
$numbers = [1,2,3,4,5];

class Map
{
    public function __invoke(array $array, Closure $closure)
    {
        return array_map($closure, $array);
    }
}

$map = Closure::fromCallable(new Map);

$result = $map($numbers, fn($v) => $v+3);

var_dump($result);
```

클래스에 `__invoke` 메소드가 정의되어 있으면 인스턴스화 된 객체는 호출가능하다. 호출 가능한 객체의 타입은 주형 클래스를 따르며 Closure 객체의 상속은 불가능하므로 Closure 타입에 전달할 수 없다. 이를 Closure 타입에 전달하기 위해서는 Closure 타입으로 만들어 줘야한다. `Closure::fromCallable()`을 통해서 호출할 수 있는 객체를 Closure로 바꿀 수 있다. 앞서 생성된 Map을 Closure으로 바꾸면 Closure 타입이 된다는 것을 알 수 있다.

```php
var_dump($map instanceof Map); // false
var_dump($map instanceof Closure); // true
var_dump(is_a($map, 'Map')); // false
var_dump(is_a($map, 'Closure')); // true
```
