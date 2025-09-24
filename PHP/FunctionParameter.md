# 함수 파라메터

# 들어가며

php에서 Closure를 함수 인자로 받을 때는 `Closure` 타입힌트를 사용한다. 함수를 사용하려면 매개변수로 어떤 값을 전달 받고, 반환 값으로 어떤 타입을 반환하는지 알아야 하는데, `Closure` 매개변수만으로는 함수의 스펙 정보가 부족하다. 이 글은 php의 리플렉션을 통한 런타임 검사를 통해 함수의 인자로 전달된 `Closure`의 매개변수 타입과 반환 타입을 확인하는 기능을 제안하고, 클로저의 매개변수와 반환 값의 타입 정보를 추가하여 클로저를 다룰 때의 불편함을 줄이는 방법을 알아본다. 또한 클로저를 사용할 때 신중해야 하는 이유와 대안도 함께 살펴보자.

## php Closure의 특징

다음은 [PHP 공식 문서](https://www.php.net/manual/en/class.closure.php#closure.synopsis)의 `Closure`에 대한 시놉시스(synopsis: 요약, 개요)이다.

```
final class Closure {
    /* Methods */
    private __construct()
    public static bind(Closure $closure, ?object $newThis, object|string|null $newScope = "static"): ?Closure
    public bindTo(?object $newThis, object|string|null $newScope = "static"): ?Closure
    public call(object $newThis, mixed ...$args): mixed
    public static fromCallable(callable $callback): Closure
}
```

위 시그니처의 특성을 이해하면 왜 클로저의 매개변수와 반환 타입을 지정하는 방식을 생각할 수 없는지 생각해 볼 수 있다. 우선 Closure의 특성을 알아보자.

### 상속 불가능

`final class` 이므로 상속 불가능하며 기존 메소드를 오버라이딩해서 변경하거나 메소드를 추가할 수 없다. 주어진 클래스의 사양의 변경 없이 그대로 사용해야 한다.

### 객체 생성 불가

`private __construct`는 객체 외부에서 new 키워드를 통한 객체 생성을 차단한다. new로 새로운 객체가 생성될 때는 `public`인 생성자 함수를 호출하면서 새로운 객체를 만드는데, 생성자 함수가 `private`이므로 접근을 차단하기 때문에 객체 생성을 할 수 없게 만든다.

`private`라서 객체 내부에서는 생성자 함수에 접근할 수 있어서 `new Self`와 같은 코드로 객체를 생성하는 메소드를 만들 수는 있지만, final로 상속 불가능한 클래스이므로 각종 메소드를 추가 설정할 수 없다. 

`Closure::method`의 방식으로는 객체를 생성할 수 없고, 익명함수를 생성하는 문법(`function ($param) { /* code */}`, `fn($param) => $return; `)으로만 클로저 객체를 생성할 수 있다.

### 바인딩이란?

익명함수는 Closure 클래스의 호출 가능한 메소드를 정의하는 것과 같다. 그래서 익명함수의 함수 블록안에 `$this`는 `Closure` 객체를 가리킨다. 이때 객체를 바인딩하는 것을 통해서 익명 함수 블록 내의 `$this`를 바인딩한 객체로 만들 수 있다.

### 익명함수 복사하기

```php
public static bind(Closure $closure, ?object $newThis, object|string|null $newScope = "static"): ?Closure
```

`$newFn = Closure::bind($fn, $obj)`으로 익명함수 `$fn`을 복사하면서, 필요에 따라 바인딩할 오브젝트를 지정하여 복사된 익명함수에 바인딩할 수 있다.

오브젝트를 바인딩하여 `$this`으로 바인딩한 오브젝트에 엑세스를 할 수 있지만, 클래스가 객체화 되는 순간 퍼블릭 멤버에만 접근할 수 있게 된다. 이미 생성된 오브젝트를 바인딩하는 것이므로 바인딩한 대상의 프라이빗 또는 퍼블릭 멤버에 엑세스 할 수 없다. 비공개 멤버에도 접근하기 위해서는 인스턴스화 되어 더 이상 비공개 멤버에 접근할 수 없는 대상이 아닌, 인스턴스화 하기 전의 주형 정보가 있어야 접근할 수 있다.

리플렉션을 통해서 오브젝트의 클래스 정보를 얻어 오브젝트의 비공개 멤버에 접근하는 방식을 택할 수도 있지만, 주형 정보 직접 사용하는 것으로 리플렉션에 리소스를 소비하지 않을 수 있다.

`$newScope`는 복사되는 익명함수에 주형 정보를 바인딩한다. 기본적으로는 `'static'`이란 문자열로 되어 있는데, 클로저 멤버에서 `static::`의 코드로 접근할 수 있게 한다. 만약 클래스 A를 바인딩하면, `A::`의 코드를 익명 함수 블록 내의 코드에서 사용할 수 있고, `self::`의 self는 A가 되는 식이다.

### 익명 함수에 바인딩 대상을 전달하며 실행하기

```php
public call(object $newThis, mixed ...$args): mixed
```

메소드 선언부는 바인딩 할 객체를 받고, 클로저를 실행할 때 필요한 인자를 받는 형태로 구성되어 있다.

정적 메소드가 아닌데 그 이유는 `$fn->call()`의 방식으로 익명함수에 (익명 함수를 객체로 두고) 직접 `->call()`으로 실행한다는 것이다.

### 콜러블 함수를 클로저로 만들기

```php
public static fromCallable(callable $callback): Closure
```

부분은 callable 타입의 호출할 수 있는 타입을 받아서 Closure 타입의 함수를 생성할 수 있다는 것을 드러낸다.

참고로 Closure는 callable의 하위 타입이므로 callable 타입힌트에 Closure 타입을 전달할 수 있다.

## Closure의 문제

### 매개 변수 및 반환 타입의 정의 불가

매개 변수로 함수를 받을 때 특정 타입의 매개변수를 갖는 함수의 타입을 정의 할 수 없다.

함수를 인자로 전달할 수 있는 고차함수의 특성이 php에 있음에도 불구하고, php에서 함수형 프로그래밍이 적절하지 않는 의견은 매개변수로 받을 함수의 타입을 지정할 수 없다는 점 때문이다.

받을 함수의 타입을 지정할 수 없기 때문에 구체적으로 어떤 타입의 함수를 전달해야 할지 알 수 없다는 문제점이 있다.

### Closure 클래스 상속 불가

php에서 Closure는 언어에 내장된 클래스이다. 만약 이 클래스를 상속해서 새로운 클래스를 만들 수 있다면, 함수의 파라메터로 함수를 받을 때, Closure를 상속 받아 재정의한 서브 클래스를 받게하여, 특수한 시그니처의 Closure 타입을 받는다는 생각을 할 수 있다.

하지만 php에서 Closure 클래스는 final 클래스이므로 상속해서 별도의 클래스명을 부여할 수 없기 때문에 별도의 시그니처를 정의할 수 없기 때문에 Closure 파라메터가 어떤 매개 변수를 타입을 받아야 하고 어떤 반환 타입을 반환하는지 별도의 주석이나 문서 없이는 알 수 없다는 문제가 있다.

## 클로저의 타입을 확인하지 못하여 발생하는 문제

```php
declare(strict_types=1);

function calculate(Closure $operation, $transArg1, $transArg2): int {
    /*************
     * some code *
     *************/
    echo 'debug';
    /*************
     * some code *
     *************/
    return $operation($transArg1, $transArg2); // Fatal error: Uncaught TypeError: {closure}(): Argument #2 ($y) must be of type int, string given
}

$result = calculate(fn(int $x, int $y) => $x + $y, 3, '5');
echo $result; // 8
```

`$operation` 함수는 두 인자 `$x`, `$y`를 받아서 연산한 결과를 반환하는 함수이다. 그런데 `calculate` 함수에 전달할 때 `$operation` 함수에 인자가 전달되는 것이 아니라, `calculate`에 모든 인자가 전달된 이후 `$operation($transArg1, $transArg2)` 코드가 실행이 될 때, `$operation($transArg1, $transArg2)` 부분에서 `$operation` 함수에 인자가 전달된다. `echo 'debug';` 부분이 잘 출력 된다는 것은 여기까지는 에러가 발생하지 않았다는 의미이다. 그 이후 `$operation` 함수를 실행하는 코드에서 첫 번째 인자로 int 타입과 두 번째 인자로 string 타입의 값이 전달되는데, 두 번째 인자는 int 타입이 전달되어야 하는데 string 타입이 전달 되므로 에러가 발생한다.

문제는 `calculate` 함수의 매개변수인 `Closure $operation`에 인자가 전달될 때 타입 검증이 되지 않는다는 점이다. 전달된 클로저의 실행 타이밍에 타입에러가 발생하기 때문에 클로저에 잘못된 타입이 전달되었는지 빠르게 파악하지 못한다. 함수의 로직이 길어지면 클로저의 타입 문제인지 모르고 아닌 다른 원인을 찾느라 시간을 허비할 수도 있고, 클로저의 스펙을 알지 못하여 전달되는 클로저의 스펙을 확인학기 위한 코드 추적을 해야하는 등의 수고가 든다.

여기서는 `calculate` 함수에 클로저를 전달할 때, `$operation` 클로저는 함수는 인자를 2개를 받아야 하고 첫 번째 인자와 두 번째 인자는 int 타입이며, `calculate`의 2번째, 3번째 인자가 `$operation` 함수의 인자로 전달될 값을 받는다는 사양을 알아야 하는데, 클로저 파라메터의 정보가 없다는 문제가 있다.

이러한 문제 해결하기 위해 클로저를 사용한다면 클로저의 시그니처를 표시해 주어야 한다. 하지만, php가 제공하는 문법으로는 클로저의 사양을 표기할 수 없기 때문에 [phpdoc을 이용한 표기](https://phpstan.org/writing-php-code/phpdoc-types#callables)나, 런타임 리플렉션을 통한 타입 체커 기능을 사용해야 한다.

### 함수명을 이용한 시그니처 추측의 한계

위와 같은 `calculate` 함수를 함수형 프로그래밍에서는 `apply`라는 이름의 함수로 구현을 하는데, 함수형 프로그래밍에서 사용하는 함수 이름을 사용하여 함수를 정의할 줄 아는 사람도, 그 함수형 프로그래밍에서 자주 사용되는 함수 이름을 통해서 함수의 역할과 사용법을 알아낼 수 있는 사람도 드물기 때문에 함수 파라메터의 스펙을 최대한 제공하여 전달해야 하는 함수의 사양을 명확히 하는 것이 좋다.

```php
declare(strict_types=1);

function apply(Closure $operation, mixed ...$transArgs): int {
    return $operation(...$transArgs);
}

$result = apply(fn(int $x, int $y) => $x + $y, 3, 5);
echo $result; // 8
```

↑ apply 함수의 예

## 정적 분석을 통한 타입 확인

php 문법으로는 타입 추론을 할 수 없는 함수의 사양 때문에, `phpdoc`으로 호출가능한 함수의 타입을 지정할 수 있다.

```php
/**
 * @param Closure(int, int): int $operation
 */
function calculate(Closure $operation, int $trasArg1, int $transArg2): int {
    return $operation($trasArg1, $transArg2);
}

$result = calculate(fn($x, $y) => $x + $y, 3, 5);
echo $result; // 8
```

위와 같이 파라메터로 전달되는 함수의 타입을 지정해서 정적 분석으로 잘못된 타입을 지정할 수 있다. 이는 강력한 정적검사 툴에 의해서는 타입 불일치가 지적이 되지만, IDE에서는 버그 지적되지 않는 케이스도 있다. 또한 런타임의 동작에서 잘못되었는지 알려주지 않는 문제가 있으므로 런타임 시점에 전달 받은 함수의 파라메터 타입과 인자의 수가 의도한 형태의 함수인지 확인하는 코드를 추가하는 편이 좋다.

## 런타임 타입 확인

컴파일을 하는 정적 타입 언어와 달리 동적 타입 언어인 php는 정적 분석으로 코드의 무결성을 보장하기 힘들다. phpstan과 같은 정적 분석 툴을 최고 레벨로 두어 개발할 수 있지만 이런 개발 방식은 특별한 php 코딩 스타일을 요구하므로 초기 설정 부터 엄격한 타이핑의 코드를 작성할 수 있는 환경 세팅을 해 줘야 한다. 그렇지 않다면 정적 분석 레벨을 높이기 위해 많은 코드 스타일 변경 리펙토링을 요구한다. 또한 엄격한 정적 분석이 되도록 phpdoc을 써 줘야 하기 때문에 많은 보일러플레이트가 필요하고, 경우에 따라 일부 docblock 구문이 IDE의 코드 검사에서 이뤄지지 않는 경우도 있기 때문에, 일반적인 코딩 스타일에서 정적 분석과 런타임 타임 검사를 적절히 조합하는 방식으로 코드를 작성하여 IDE의 정적 분석에 맞는 코드로 실수를 줄이고, 런타임으로 실행할 때 한 번 더 코드를 체크하는 과정을 거치는 편이 좋다.

docblock으로 함수의 파라메터로 함수를 받을 때 함수의 타입 정보를 기술할 수 있었다면, 추가적으로 php의 불완전한 타입검사의 한계를 보완하기 위해 런타임으로 타입을 검증하는 과정을 넣어준다. 전달된 클로저가 어떤 타입을 받는지 알기 위해서는 리플렉션을 사용해야 한다. php에서는 [클로저의 정보를 확인할 수 있는 리플렉션 클래스](https://www.php.net/manual/en/class.reflectionfunction.php)가 제공되며 이를 통해서 전달된 클로저가 가진 인자의 타입정보, 반환 타입정보를 알 수 있다.

### 예

```php
function check0Target1String2IntParamStringReturn(Closure $fn): bool {
    $ref = new ReflectionFunction($fn);
    $params = $ref->getParameters();
    if (count($params) !== 3) return false;
    $param0TypeName = $params[0]->getType()->getName();
    if ($param0TypeName !== Target::class || is_subclass_of($param0TypeName, Target::class)) return false;
    if ($params[1]->getType()->getName() !== 'string') return false;
    if ($params[2]->getType()->getName() !== 'int') return false;
    if ($ref->getReturnType()->getName() !== 'string') return false; 
    return true;
}

$repeatPropertyValue = fn(Target $target, string $property, int $terationNumber): string => $target->{$property} * $terationNumber;

assert(check0Target1String2IntParamStringReturn($repeatPropertyValue));

class Target
{
    public readonly string $value;
}
```

`$ref = new ReflectionFunction($fn);`: 익명 함수 `$fn`의 리플렉션 정보를 취득한다.

`$params = $ref->getParameters();`: 각각의 파라메터 정보를 인덱싱된 배열로 반환한다.

`if (count($params) !== 3) return false;`: 파라메터가 3개인지 확인한다. `$ref->getNumberOfParameters()`를 사용할 수도 있다.

리플랙션 클래스의 인스턴스의 `getType()`를 사용하면 [ReflectionType](https://www.php.net/manual/en/class.reflectiontype.php) 클래스의 객체를 반환한다. `getName` 메소드로 타입의 이름을 확인할 수 있다.

`if ($param0TypeName !== Target::class || is_subclass_of($param2TypeName, Target::class)) return false;`: 첫 번째 파라메터가 Target 클래스인지 확인한다. 타입 체크를 할 때는 서브 타입인 경우도 허용되는 타입으로 고려해야 하므로 전달된 대상의 타입이 서브타입인지도 확인한다.

`if ($params[1]->getType()->getName() !== 'string') return false;`: 두 번째 파라메터가 문자열 타입인지 확인한다.

`if ($params[2]->getType()->getName() !== 'int') return false;`: 세 번째 파라메터가 정수 타입인지 확인한다.

#### 참고 : 서브타입 체크 방식

```php
class ParentClass {}
class ChildClass extends ParentClass {}
var_dump(is_subclass_of(ChildClass::class, ParentClass::class));
```

`assert(checkStringIntParamStringReturn($repeat));`로 런타임 타입 검사에 활용한다.

## 재사용할 수 있는 코드로 만들기

파라메터 하나씩 검증할 수 있는 기능을 만들면 재사용할 수 있다.

```php
function checkClosureParam(Closure $fn, string|int $keyNameOrIdx, string ...$types): bool {
    $ref = new ReflectionFunction($fn);
    $params = $ref->getParameters();
    if (is_numeric($keyNameOrIdx)) {
    	$paramIdx = $keyNameOrIdx;
    } else {
        $paramIdx = array_search($keyNameOrIdx, array_map(fn($e) => $e->getName(), $params));
    }
    $targetParam = $params[$paramIdx] ?? null;
    if (is_null($targetParam)) return false;
    $paramTypeName = $targetParam->getType()?->getName() ?? '';
    return in_array($paramTypeName, $types, true) || (array_reduce($types, fn($acc, $type) => $acc || is_subclass_of($paramTypeName, $type), false));
}

function checkClosureReturn(Closure $fn, string ...$types): bool {
    $ref = new ReflectionFunction($fn);
    $returnTypeObj = $ref->getReturnType();
    $returnTypeName = $returnTypeObj->getName() ?? '';
    return in_array($returnTypeName, $types, true) || (array_reduce($types, fn($acc, $type) => $acc || is_subclass_of($returnTypeName, $type), false));
}

$repeatPropertyValue = fn(ChildClass $target, string $property, int $terationNumber, $option): string => $target->{$property} * $terationNumber;

assert(checkClosureParam($repeatPropertyValue, 'target', ParentClass::class));
assert(checkClosureParam($repeatPropertyValue, 0, ParentClass::class));
assert(checkClosureParam($repeatPropertyValue, 'property', 'string'));
assert(checkClosureParam($repeatPropertyValue, 1, 'string'));
assert(checkClosureParam($repeatPropertyValue, 'terationNumber', 'int'));
assert(checkClosureParam($repeatPropertyValue, 2, 'int'));
assert(checkClosureParam($repeatPropertyValue, 'option', ''));
assert(checkClosureParam($repeatPropertyValue, 3, ''));
assert(checkClosureReturn($repeatPropertyValue, 'string'));

class ParentClass {}
class ChildClass extends ParentClass {}
```

런타임 타입 검사를 위해 `checkClosureParam`라는 파라메터의 타입을 검사하는 기능과 `checkClosureReturn`라는 리턴 타입을 검사하는 기능을 만들었다. 유니온 타입을 체크할 수 있도록 가변 파라메터로 복수의 타입을 지정할 수 있도록 만들어 주었다. 또한 타입힌트가 없는 경우도 허용할지 말지를 정할 수 있도록 빈 문자열('')로 타입힌트가 없는 경우 통과할 수 있도록 만들었다. 또한 리스코프 치환이 가능하도록 `is_subclass_of`으로 타입 체크를 하였다. 이후 한 번 만든 함수를 여러 파일에서 재사용할 수 있도록 `use function checkClosureParam`, `use function checkClosureReturn`으로 불러와서 클로저의 타입을 확인하자. 

하지만 이 방식은 타입을 하나씩 검사하는 방식이므로 제네릭(타입 파라메터)를 만드는 방식은 사용할 수 없다는 한계가 있다. 제네릭에 관한 정보는 이 방법이 아닌 docblock 제네릭이나, 유니온 타입, 타입 단언 등을 사용하여 보완하자.

런타임 확인을 위한 리플렉션은 리소스가 드는 작업이므로 프로덕션 환경에서는 동작하지 않는 `assert` 함수로 로컬 또는 테스트 환경에서의 실행을 확인할 때 사용하면 적절하다.

## 클로저의 대안 찾기

### 클로저의 문제

라라벨의 각종 메소드는 익명함수 파라메터를 사용하는 코드가 많은데, 이는 겉으로 보기에는 코드에 우아함을 더하지만, 전달해야 하는 함수의 사양을 php 언어 수준에서 확인할 수 없기 때문에 이를 보조하는 공식 문서등의 자료가 필수적이다. 잘 정의된 문서, 충분한 주석 설명이 갖춰지지 않는다면, php의 함수 파라메터는 어떤 인자 타입과 어떤 반환 타입을 가져야 하는지 모르기 때문에 재사용하기에 좋지 않은 코드가 된다.

### 클로저를 활용하려면

#### 일반적이거나 직관적인 함수명 또는 문서화하기

클로저를 잘 활용하기 위해서는 프로그래밍 세계에서 일반적으로 자주 쓰이는 함수명을 통해서 어떤 사양의 클로저를 받는지 알 수 있는 형태 예를 들어 `map`과 같은 컬렉션의 메소드를 사용하거나, 추가적인 사양 문서나 주석으로 어떤 클로저를 전달해야 하는지 사용법을 잘 기술하는 것이 중요하다.

예를 들어 라라벨의 컬렉션은 배열 또는 컬렉션 자료구조의 순회하는 원소 각각에 적용되는 익명함수를 사용한다. 이 때 익명함수는 컬렉션의 원소의 타입을 받기 때문에 기본적으로는 특정한 타입에 얽매여있지 않다. 익명함수는 사용될 때, 특정 타입에 의존적인 기능을 갖기 보다는 타입에 의존적이지 않게 만들어, A 타입을 함수의 인자로 전달하면 A 타입을 받고, B 타입을 함수의 인자로 반환하면 체인된 컬렉션 메소드의 익명함수에 B 타입이 전달되는 처음 컬렉션 메소드에 전달되는 타입을 알면, 체인된 다음 컬렉션 메소드에 전달되는 타입도 알 수 있는 직관적으로 이해할 수 있는 메소드가 많다. 또한 직관적으로 알 수 없는 경우 각 메소드의 설명이 라라벨 문서에 잘 기술되어 있으므로 참고하면 된다.

#### 인자 사용되는 클로저가 필요한가?

익명함수는 설계된 코드에 변화를 주기 위해 자주 쓰인다. 하지만 지나치게 자유로운 로직을 허용해 설계 의도를 흐릴 수 있다. 객체나 함수에서 미리 설계된 구체적인 로직이 있고 제공될 값의 범위가 정해진 경우, 굳이 익명함수를 사용할 필요가 없으며 오히려 익명 함수로 전달할 수 있는 로직의 자유도로 인해 객체나 함수가 익명 함수를 통해 지나치게 다양한 양상 갖게 되어 코드의 원래 설계 목적, 도메인적인 제약사항을 초과하거나 무시하는 동작을 만들어 낼 수 있다.

도메인에서 쓰이는 범위로 기능을 제한하는 것으로 해당 코드의 의미와 목적을 분명하게 만들 수 있는 장점이 있는데, 익명 함수가 남발되면서 만들어진 목적과 의미를 훼손하는 코드가 생성될 수 있다. 익명함수를 써야 한다면, 익명함수를 인자로 받으므로서 가능한 어떠한 로직에도 객체나 함수의 동작이 제한되어야 하고 익명 함수를 인자로 전달 받음에도 불구하고 로직이 명확한 편이 좋다.

보통 함수의 파라메터로 함수를 받는 것은 함수형 프로그래밍에서 사용한다. 순수 함수라면 함수를 전달하더라도 동작의 예측이 쉬울 수 있지만, 순수함수가 아니라 객체내에서 전달 받은 함수에 외부 변수나, 객체를 바인딩하는 코드가 있다면 객체나 변수의 상태를 변경할 수 있기 때문에 어떤 동작을 할지 예측하기 어려워진다. 또한 전달 받은 함수를 객체에서 어떤 목적으로 이용할 것인지를 알 수 있도록 객체의 사양(인터페이스, 퍼블릭 메소드)을 드러내야 하는데 이를 드러내기 쉽지 않은 경우가 있어서 함수를 파라메터로 사용하는 코드를 남발하는 것은 좋지 않다.

### 클로저의 대안

#### 메소드 사용하기

익명함수를 만드는 것은 함수 외부의 변수를 캡쳐해서 사용할 수 있는 이점이 있고, 해당 맥락에서만 사용하는 코드를 작성하는데 유용하다. 

클래스 내부의 코드의 경우 익명함수 보다는 매개변수의 타입과 반환 타입을 시그니처로 나타낼 수 있는 메소드를 사용하는 것이 좋다. 함수의 시그니처를 나타내지 않아도 사용법을 알아채기 쉬운 코드나 간단한 코드인 경우에는 익명함수를 쓰는 것이 메소드를 작성하는 것 보다 간단히 작성할 수 있는 이점이 있지만, 그렇지 않다면 메소드를 만들어 함수의 사양을 파악하기 쉽도록 하자.

인스턴스 메소드는 `$this`를 자주 사용하게 되어 객체의 상태 관리가 복잡하게 되고 어려워질 수 있으므로, 정적 메소드를 사용하고, 특별한 상황이 아닌 한 정적 메소드는 정적 멤버의 상태 공유가 없는 순수 함수처럼 정의하여 사용하여 함수를 사용하면 어떤 일이 일어날지 예측하기 쉽게 만들자.

#### 디자인 패턴을 활용하기

디자인 패턴을 통해 미리 만들어둔 특정 사양의 객체로 교체 할 수 있게 만들거나, 설정할 수 있는 옵션을 미리 구비하여 필요에 따라 설정된 대상을 주입하여 교체할 수 있도록 코드를 만들 수 있는 각종 패턴을 만드는 방법을 배우도록 한다. 함수 파라메터를 대체할 수 있는 OOP의 디자인 패턴으로는 전략 패턴, 상태 패턴, 템플릿 메소드 패턴 등이 있다. (각각의 세부 설명은 디자인 패턴과 관련된 내용이므로 여기서는 생략한다.)

- 전략 패턴 : 런타임에 교체 가능한 여러 개의 알고리즘(전략)을 캡슐화하고, 이를 인터페이스로 추상화하여 선택적으로 사용할 수 있도록 하는 패턴.
- 상태 패턴 : 객체의 상태를 별도의 클래스로 분리하고, 상태 변경에 따라 객체의 동작이 다르게 수행되도록 하는 패턴.
- 템플릿 메소드 패턴 : 상위 클래스에서 알고리즘의 기본 흐름을 정의하고, 일부 세부 구현을 하위 클래스에서 정의하도록 하는 패턴.

#### 클래스도 대체하기

클래스를 객체화하고, 클래스 자체를 함수처럼 호출해 사용할 수 있도록 `__invoke`를 사용하여 매개변수의 타입과 반환 타입을 정의하는 것이 가능하다. 클로저 대신에 별도의 클래스를 타입으로 한 객체를 전달하는 방법도 사용할 수 있다.

또는 Value Object를 정의해서 클로저 대신 사용하는 방법도 좋은 방법이다. Value Object는 도메인의 제약이 담긴 값을 담는 용도 뿐만 아니라, 도메인의 제약사항을 표현하는 로직을 담은 클래스를 만드는 방식으로 클로저를 대체한다.

# 마지막으로

php에서 Closure 타입은 매개변수나 반환 값의 타입과 같은 함수의 시그니처를 표기할 수 없다는 한계를 가지고 있다. 언어의 네이티브 기능으로 클로저의 시그니처 제약사항을 표현할 수 없기 때문에 런타임 사양 검증 및 대안적인 방법을 제시하였다.

Closure 매개변수로 사용하기에 앞서, 꼭 사용해야 할까? 한 번쯤 고민해 보면 도움이 될 것이다.

