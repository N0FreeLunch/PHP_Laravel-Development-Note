# 함수 파라메터

php에서 함수를 파라메터로 받을 때 타입 힌트로 Clousure 클래스를 사용한다.

## php Closure의 사양

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

- `final class` : 클래스의 상속이 불가능하다.
- `private __construct` : new 키워드를 통한 객체 생성이 불가능하다. 특별한 객체 생성 메소드도 `new Self`와 같은 코드 기반으로 만들기 때문에 `Closure::method`의 방식으로는 객체를 생성할 수 없고, `function () {}` 문법으로만 클로저 객체를 생성할 수 있다.
- `public static fromCallable(callable $callback): Closure`: callable 타입의 호출할 수 있는 타입을 받아서 Closure 타입의 함수를 생성할 수 있다.　(Closure는 callable의 하위 타입이므로 callable 타입힌트에 Closure 타입을 전달할 수 있다.)

## Clousure의 문제

### 파라메터 함수의 타입 정의 불가

매개 변수로 함수를 받을 때 특정 타입의 매개변수를 갖는 함수의 타입을 정의 할 수 없다.

함수를 인자로 전달할 수 있는 고차함수의 특성이 php에 있음에도 불구하고, php에서 함수형 프로그래밍이 적절하지 않는 의견은 매개변수로 받을 함수의 타입을 지정할 수 없다는 점 때문이다.

받을 함수의 타입을 지정할 수 없기 때문에 구체적으로 어떤 타입의 함수를 전달해야 할지 알 수 없다는 문제점이 있다.

### Clousure 클래스 상속 불가

php에서 Clousure는 언어에 내장된 클래스이다. 만약 이 클래스를 상속해서 새로운 클래스를 만들 수 있다면, 파라메터의 함수 시그니처로 특수한 구현체의 Clousure를 받을 수 있을거라 생각할 수 있다.

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
