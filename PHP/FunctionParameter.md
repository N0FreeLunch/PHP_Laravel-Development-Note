# 함수 파라메터

php에서 함수를 파라메터로 받을 때 타입 힌트로 Clousure 클래스를 사용한다.

## php Closure의 사양

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

### 상속 불가능

`final class` 이므로 상속 불가능하며 기존 메소드를 오버라이딩해서 변경하거나 메소드를 추가할 수 없다. 주어진 클래스의 사양의 변경 없이 그대로 사용해야 한다.

### 객체 생성 불가

`private __construct`는 객체 외부에서 new 키워드를 통한 객체 생성을 차단한다. new로 새로운 객체가 생성될 때는 `public`인 생성자 함수를 호출하면서 새로운 객체를 만드는데, 생성자 함수가 `private`이므로 접근을 차단하기 때문에 객체 생성을 할 수 없게 만든다.

`private`라서 객체 내부에서는 생성자 함수에 접근할 수 있어서 `new Self`와 같은 코드로 객체를 생성하는 메소드를 만들 수는 있지만, final로 상속 불가능한 클래스이므로 각종 메소드를 추가 설정할 수 없다. 

`Closure::method`의 방식으로는 객체를 생성할 수 없고, 익명함수를 생성하는 문법(`function ($param) { /* code */}`, `fn($param) => $return; `)으로만 클로저 객체를 생성할 수 있다.

### 바인딩이란?

익명함수는 Closure 클래스의 호출 가능한 메소드를 정의하는 것과 같다. 그래서 익명함수의 함수 블록안에 `$this`는 Closure 객체를 가리킨다. 이때 객체를 바인딩하는 것을 통해서 익명 함수 블록 내의 `$this`를 바인딩한 객체로 만들 수 있다.

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

## Clousure의 문제

### 파라메터 함수의 타입 정의 불가

매개 변수로 함수를 받을 때 특정 타입의 매개변수를 갖는 함수의 타입을 정의 할 수 없다.

함수를 인자로 전달할 수 있는 고차함수의 특성이 php에 있음에도 불구하고, php에서 함수형 프로그래밍이 적절하지 않는 의견은 매개변수로 받을 함수의 타입을 지정할 수 없다는 점 때문이다.

받을 함수의 타입을 지정할 수 없기 때문에 구체적으로 어떤 타입의 함수를 전달해야 할지 알 수 없다는 문제점이 있다.

### Clousure 클래스 상속 불가

php에서 Clousure는 언어에 내장된 클래스이다. 만약 이 클래스를 상속해서 새로운 클래스를 만들 수 있다면, 파라메터의 함수 시그니처로 특수한 구현체의 Clouure를 받을 수 있을거라 생각할 수 있다.

하지만 php에서 Clousure 클래스는 final 클래스이므로 상속해서 별도의 클래스명을 부여할 수 없기 때문에 시그니처로 사용하기에 문제가 있다.

## 정적 분석을 통한 타입 확인

php 문법으로는 타입 추론을 할 수 없는 함수의 사양 때문에, phpdoc으로 호출가능한 함수의 타입을 지정할 수 있다.

```php
/**
 * @param callable(int, int): int $operation 두 개의 정수를 받아 정수를 반환하는 콜백
 */
function calculate(callable $operation, int $a, int $b): int {
    return $operation($a, $b);
}

$result = calculate(fn($x, $y) => $x + $y, 3, 5);
echo $result; // 8
```

위와 같이 파라메터로 전달되는 함수의 타입을 지정해서 정적 분석으로 잘못된 타입을 지정할 수 있다. 하지만 실제 실행할 때, 잘못되었는지 알려주지 않는 문제가 있으므로 전달 받은 함수의 파라메터 타입과 인자의 수가 의도한 형태의 함수인지 확인하는 코드를 추가하는 편이 좋다.

## 런타임 타입 확인

컴파일을 하는 정적 타입 언어와 달리 동적 타입 언어인 php는 정적 분석으로 코드의 무결성을 보장하기 힘들다. phpstan과 같은 정적 분석 툴을 최고 레벨로 두어 개발할 수 있지만 이런 개발 방식은 특별한 php 코딩 스타일을 요구하므로 초기 설정 부터 엄격한 타이핑의 코드를 작성할 수 있는 환경 세팅을 해 줘야 한다. 그렇지 않다면 정적 분석 레벨을 높이기 위해 많은 코드 스타일 변경 리펙토링을 요구한다. 또한 엄격한 정적 분석이 되도록 phpdoc을 써 줘야 하기 때문에 많은 보일러 플레이트를 요구 하는 등 php를 꼭 써야하는지에 대한 의문점 들 수 있다. 따라서 일반적인 코딩 스타일에서 정적 분석과 런타임 타임 검사를 적절히 조합하는 방식으로 코드를 작성할 때 IDE의 정적 분석에 맞는 코드로 실수를 줄이고, 런타임으로 실행할 때 한 번 더 코드를 체크하는 과정이 필요하다.

phpdoc으로 함수의 파라메터로 함수를 받을 때 함수의 타입 정보를 기술할 수 있었다면, php의 불완전한 타입검사의 한계를 보완하기 위해 런타임으로 타입을 검증하는 과정을 넣어준다. 전달된 클로저가 어떤 타입을 받는지 알기 위해서는 리플렉션을 사용해야 한다. php에서는 [클로저의 정보를 확인할 수 있는 리플렉션 클래스](https://www.php.net/manual/en/class.reflectionfunction.php)가 제공되며 이를 통해서 전달된 클로저가 가진 인자의 타입정보, 반환 타입정보 등 다양한 정보를 알 수 있다.

### 예제코드

```php
function checkStringIntParamStringReturn(Closure $fn): bool {
     $ref = new ReflectionFunction($fn);
     $params = $ref->getParameters();
     if (count($params) !== 3) return false;
     if ($params[0]->getType()->getName() !== 'string') return false;
     if ($params[1]->getType()->getName() !== 'int') return false;
     if ($params[2]->getType()->getName() !== Storage::class) return false;
     return true;
}

$repeat = fn(string $word, int $terationNumber, Storage $store) => $word * $terationNumber;

assert(checkStringIntParamStringReturn($repeat));

class Storage
{
	public readonly string $value;
}
```

`$ref = new ReflectionFunction($fn);`: 익명 함수 `$fn`의 리플렉션 정보를 취득한다.

`$params = $ref->getParameters();`: 각각의 파라메터 정보를 인덱싱된 배열로 반환한다.

`if (count($params) !== 3) return false;`: 파라메터가 3개인지 확인한다. `$ref->getNumberOfParameters()`를 사용할 수도 있다.

`if ($params[0]->getType()->getName() !== 'string') return false;`: 첫 번째 파라메터가 문자열 타입인지 확인한다.

`if ($params[1]->getType()->getName() !== 'int') return false;`: 두 번째 파라메터가 정수 타입인지 확인한다.

`if ($params[2]->getType()->getName() !== Storage::class) return false;`: 세 번째 파라메터가 Storage 클래스인지 확인한다.

`assert(checkStringIntParamStringReturn($repeat));`로 런타임 타입 검사에 활용한다.
