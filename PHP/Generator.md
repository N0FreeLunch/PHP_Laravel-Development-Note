# Generator

## Generator 사용 이유

`Iterator` 인터페이스를 구현하는 것은 `current`, `key`, `next`, `rewind`, `valid` 등의 메소드를 모두 구현해야 하기 때문에 보일러 플레이트가 장황하다. 순 방향 조회의 순회만 할 경우, `Iterator` 인터페이스에서 `next`만을 사용하는 방법으로 사용한다고 하면, Generator를 사용해서 보일러 플레이트를 줄이는 코드를 작성할 수 있다.

## 제너레이터의 특징

### 메모리 소모

일반적인 자료 구조는 메모리에 순회 대상의 데이터를 모두 올려 두는 방식을 사용한다. 하지만 제너레이터는 모든 데이터를 메모리에 올려 두는 것이 아니라, 순회에 필요한 데이터만을 생성하는 방식으로 동작한다.

모든 데이터를 미리 생성하면, 반복되는 사용에는 유용하지만 1회성 작업에는 많은 메모리를 소비하기 때문에 적절한 방법은 아니다. 순회와 동시에 1회만 취득하는 경우 제너레이터를 사용해서 메모리 소비를 최소한으로 하여 사용가능하다.

### `Iterator`의 인터페이스 사용 가능

제너레이트는 `Iterator`의 인터페이스와 동일한 인터페이스를 갖는데, `Iterator`의 인터페이스인 `current`, `key`, `next`, `rewind`, `valid` 뿐만 아니라, `send`, `getReturn`, `throw`, `_​_​wakeup` 등의 메소드도 추가적으로 사용할 수 있다. `Iterator`의 인터페이스를 가지고 있기 때문에 `Iterator`를 활용하는 다양한 구문에 제너레이터를 사용할 수 있다.

## 제너레이터의 기본 문법

### 클래스 사양

```php
final class Generator implements Iterator {
    /* Methods */
    public current(): mixed
    public getReturn(): mixed
    public key(): mixed
    public next(): void
    public rewind(): void
    public send(mixed $value): mixed
    public throw(Throwable $exception): mixed
    public valid(): bool
    public __wakeup(): void
}
```

`Iterator` 인터페이스를 클래스를 구현하므로 `Iterator` 인터페이스를 전달할 수 있는 다양한 인자 및 구문에 사용될 수 있다.

`final` 클래스이므로 상속을 사용해서 확장할 수 없다. 이는 메소드의 타입힌트를 구체화하여 명확한 타입을 사용하도록 하는 방법을 사용할 수 없게 한다.

> Generator objects cannot be instantiated via new.

제너레이터는 클래스이고 `private function __construct()`가 정의된 것은 아니지만 `new` 키워드를 통해서 객체를 생성할 수 없는 특수한 대상이다. 만약 new 키워드로 `new Generator` 코드를 사용하면 다음과 같은 에러메시지가 나타난다.

> Fatal error: Uncaught Error: The "Generator" class is reserved for internal use and cannot be manually instantiated

### 메소드

#### [current](https://www.php.net/manual/en/generator.current.php)

```
public Generator::current(): mixed
```

제너레이터 내부의 현제 단계의 yielded 값을 반환한다.

#### [getReturn](https://www.php.net/manual/en/generator.getreturn.php)

```
public Generator::getReturn(): mixed
```

제너레이터 함수 내부의 함수의 반환 값(`return` 키워드로 반환되는) 값을 얻는다.

```php
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

이 때 제너레이터 내부의 단계가 `return` 키워드에 도달하지 못하고 어떤 `yield` 단계에서 멈춰 있다면 에러가 발생한다. 다음 예를 보자.

```php
$gen = (function() {
    yield 1;
    yield 2;
    yield 3;
})();

$gen->next();
var_dump('key: '.$gen->key());
var_dump('yield: '.$gen->current());

$gen->next();
var_dump('key: '.$gen->key());
var_dump('yield: '.$gen->current());

$gen->rewind(); // Fatal error: Uncaught Exception: Cannot rewind a generator that was already run 

var_dump('key: '.$gen->key());
var_dump('yield: '.$gen->current());
```

> Fatal error: Uncaught Exception: Cannot get return value of a generator that hasn't returned 

#### [key](https://www.php.net/manual/en/generator.key.php)

```
public Generator::key(): mixed
```

`yielded` 값의 키를 반환한다. 이 때 키는 몇 번째 실행된 `yield`인지를 파악하기 위해서 사용된다.

#### [next](https://www.php.net/manual/en/generator.next.php)

```
public Generator::next(): void
```

함수 내의 다음 번 실행되는 `yield` 위치까지 실행을 한다. 반환값이 `void`인 것을 통해서 알 수 있는 것은, 다음 번 `yield` 부분까지 이동하고 `yielded` 값을 반환하지는 않는다는 의미이다.

#### [rewind](https://www.php.net/manual/en/generator.rewind.php)

```
public Generator::rewind(): void
```

제너레이터는 순 방향으로만 데이터를 조회할 수 있기 때문에, `rewind` 메소드로 이전 `yield`로 갈 수 없으며 단순히 `iterator` 인터페이스와 일치시키기 위한 역할의 구현체이다.

단, 제너레이터의 단계가 첫 번째 `yield` 단계라면 유의미한 실행을 하는데, 첫 번째 `yield`가 실행되기 바로 전 지점까지 동작시킨다. 이는 제너레이터의 생성 시점에서는 제너레이터 내부에 정의된 코드가 실행되지는 않지만 `rewind`를 사용하면 첫 번째 `yield` 지점까지 코드를 실행한다.

`current` 메소드를 사용하면 해당 단계의 `yield` 값을 얻을 수는 있지만, 다음 단계의 `yield` 위치로 이동하지는 않는다. 첫 번째 단계의 `yield`에서 `current`를 사용하면 단계는 여전히 첫 번째 단계이므로 `rewind`를 사용하면 해당 단계 그대로 머물게 된다.

```php
function generator(): Generator
{
    echo "I'm a generator!\n";

    for ($i = 1; $i <= 3; $i++) {
        yield $i;
    }
}

// Initialize the generator
$generator = generator();

// Rewind the generator to the beginning of the first yield expression,
// if it's not already there
$generator->rewind(); // I'm a generator!

// Nothing happens here; the generator is already rewound
$generator->rewind(); // No output (NULL)
```

제너레이터를 생성 후 첫 번째 `rewind` 메소드가 실행되면, 첫 번째 yield 위치까지의 코드가 실행되며, 첫 번째 yielded 값은 아직 얻지 않은 단계까지 코드의 실행이 이뤄진다.

그래서 for문 내부의 yield 부분의 값은 얻지 않고, 그 전의 `echo "I'm a generator!\n";` 부분의 코드만 실행된다.

첫 번째 `yield` 단계에서 `rewind` 메소드가 실행되면, 이미 첫 번째 `yield` 단계에서 첫 번째 `yield` 단계로 이동하는 것이기 때문에 특별하게 코드를 실행할 것이 없기 때문에 아무런 코드를 실행하지 않는다.

```php
foreach ($generator as $value) {
    // After yielding the first value, the generator remains at
    // the first yield expression until it resumes execution and advances to the next yield
    echo $value, PHP_EOL; // 1

    break;
}
```

아직 첫 번째 `yield` 단계에 있기 때문에, 제너레이터를 순회하는 `foreach` 구문에 의해서 첫 번째 `yield` 값 부터 순회를 하게 된다.

위의 예제는 `foreach` 문에 `break`가 존재하기 때문에 첫 번째 순회만 실행하므로 1만 출력한다.

```php
// Resume and rewind again. No error occurs because the generator has not advanced beyond the first yield
$generator->rewind();

echo $generator->current(), PHP_EOL; // 1
```

`yield`의 단계가 첫 번째 단계이므로 `rewind` 메소드를 다시 실행해도 첫 번째 단계에서 멈춰있는 단계가 된다. `current`으로 현재 값을 취득할 때는 해당 단계의 값인 1을 얻는다.

```php
// No error occurs, the generator is still at the first yield
$generator->rewind();

// This advances the generator to the second yield expression
$generator->next();

try {
    // This will throw an Exception,
    // because the generator has already advanced to the second yield
    $generator->rewind(); // Fatal error: Uncaught Exception: Cannot rewind a generator that was already run
} catch (Exception $e) {
    echo $e->getMessage();
}
```

`next()` 메소드를 통해서, 제너레이터의 단계가 다음 단계의 `yield`로 이동을 하게되면, `rewind()`메소드는 첫 번째 단계의 `yield`로 가야 하는데, 제너레이터는 순 방향 조회만 가능하므로, 두 번째 단계의 `yield`에서 첫 번째 단계의 `yield`로 `rewind` 할 수 없다는 에러를 반환한다.

#### [send](https://www.php.net/manual/en/generator.send.php)

```
public Generator::send(mixed $value): mixed
```

`yield value`에서 `value`는 제너레이터가 `yield` 지점에서 실행을 멈추었을 때 `current`으로 `yield`에 의해 반환되는 값이다. 이에 반해 `yield`에 값을 부여하지 않을 때는 제너레이터가 멈추는데, 제너레이터 외부에서 `send` 메소드로 `yield` 값을 제너레이트 내부로 보내면 `yield` 값이 있으므로 제너레이터 재개되고, 그 다음으로 값이 없는 `yield` 단계에서 정지한다.

```php
function printer() {
    echo "I'm printer!".PHP_EOL;
    while (true) {
        $string = yield;
        echo $string.PHP_EOL;
    }
}

$printer = printer();
$printer->send('Hello world!');
$printer->send('Bye world!');
```

`$printer = printer();`에 의해 제너레이터가 생성된다. 생성된 제너레이트는 `$printer->send()`에 의해서 첫 번째로 `yield`값을 받고, 첫 번째 `yield` 단계까지 코드를 실행한다.

따라서 `$printer->send('Hello world!');`는 `echo "I'm printer!".PHP_EOL;` 부분의 코드와 while 문의 첫 번째 `$string = yield;`를 실행하여 `I'm printer! Hello world!`가 되고, 두 번째 `yield`는 값이 없으므로 이 지점에서 정지해 있다가 `$printer->send('Bye world!')`으로 다음 `yield` 값을 받으면 실행이 되어 `Bye world!`를 출력한다.

#### [throw](https://www.php.net/manual/en/generator.throw.php)

```
public Generator::throw(Throwable $exception): mixed
```

`yield` 부분에 예외 객체를 전달하면서 던진다. `send`로 단순히 에외 객체를 전달하면 예외 객체를 값으로 사용하는 것이 되며, 예외 객체를 전달하는 것 + 전달한 예외 객체를 던지는 것까지 이뤄진다.

```php
function gen() {
    echo "Foo\n";
    try {
        yield;
    } catch (Exception $e) {
        echo "Exception: {$e->getMessage()}\n";
    }
    echo "Bar\n";
}
 
$gen = gen();
$gen->rewind();
$gen->throw(new Exception('Test'));
```

#### [valid](https://www.php.net/manual/en/generator.valid.php)

```
public Generator::valid(): bool
```

제너레이터의 모든 단계가 순회 되었는지 아니면 아직 단계의 진행중인지 확인한다. 어떤 `yield` 단계에서 정지되어 있다면 `true`를 모든 단계가 끝나 (`return`이 없어도 `null`을 반환하며 종료하기 때문) 함수가 종료되었다면 `false`를 반환한다.

```php
$gen1to3 = (function() {
    for ($i = 1; $i <= 3; $i++) {
        // Note that $i is preserved between yields.
        yield $i;
    }
})();

foreach ($gen1to3 as $value) {
    var_dump($gen1to3->valid());
    echo "$value\n";
}

var_dump($gen1to3->valid());
```

위의 결과는 다음과 같다.

```
bool(true)
1
bool(true)
2
bool(true)
3
bool(false)
```

제너레이터 단계가 yield에서 멈춰 있을 때, `valid()` 메소드는 `true`를 반환하지만 제너레이터 함수가 종료 되었을 때는 `false`를 반환한다.

#### [__wakeup](https://www.php.net/manual/en/generator.wakeup.php)

```
public Generator::__wakeup(): void
```

> The intended use of __wakeup() is to reestablish any database connections that may have been lost during serialization and perform other reinitialization tasks.

`__wakeup` 매직 메소드는 시리얼라이즈 된 문자열을 다시 프로그래밍 언어의 값으로 (언시리얼라이즈를 통해서) 부활 시켰을 때, 이 객체를 동작시키기 위해서 필요한 조건들을 세팅하기 위한 것들을 정의하는 데 사용한다. 제너레이터는 직렬화 대상이 아니므로 이 메소드를 실행하는 것은 에러를 발생한다. 시리얼라이즈 이후 자동으로 `__wakeup` 메소드가 실행이 되는데, 이 때 에러를 발생시켜 언시리얼라이즈를 할 수 없도록 만든다.

## 제너레이터의 활용

```php
function gen1to3() {
    for ($i = 1; $i <= 3; $i++) {
        // Note that $i is preserved between yields.
        yield $i;
    }
}

$generator = gen1to3();
foreach ($generator as $value) {
    echo "$value\n";
}
```

제너레이터는 함수의 평가와 같이 '함수()'의 방식의 리턴 값을 통해서 제너레이터 객체를 얻을 수 있다. `new Generator`를 사용하지 않고 '제너레이터_함수()'의 방식으로 제너레이터 객체를 얻는 것에 주의하자.

제너레이터 객체는 `Iterator` 인터페이스를 구현하며, `Iterator`는 `Traversable` 인터페이스를 상속하는 인터페이스인데, `Traversable` 인터페이스는 `foreach`의 순회가 가능한 인터페이스 사양을 가진다.

제너레이터는 `foreach`에 의한 순회가 가능한 인터페이스를 가지고 있으며, 배열의 각각의 원소를 `foreach($arr as $el)`으로 사용하는 것과 같이 `foreach($gen as $yielded)` 각 `yieled` 단계를 엑세스 할 수 있다.

```php
foreach ($generator as $value) {
    echo "$value\n";
}
```

`foreach`의 각 순회 단계 중괄호를 여는 시점에서 제너레이터는 이전 단계에서 다음 단계의 `yield`까지 코드를 실행을 한다.

### `yield`에서 키-값 쌍을 반환하기 

php의 foreach 구문은 `foreach ($generator as $value)`와 같이 각각의 원소에 대한 순회를 지원하기도 하지만, `foreach ($generator as $key => $value)` 방식의 순회를 할 수 있다.
`foreach` 구문에 의한 순회가 가능한 `Traversable`의 구현체는 순회할 값 뿐만 아니라 키에 대한 구현도 하기 때문에 키를 기반으로 한 `foreach` 순회가 가능하다.

제너레이터에서는 `yield $key => $value`의 구문으로 `foreach` 구문의 `$key => $value`에서 키와 값을 취득할 수 있도록 할 수 있다.

```php
/*
 * The input is semi-colon separated fields, with the first
 * field being an ID to use as a key.
 */

$input = <<<'EOF'
1;PHP;Likes dollar signs
2;Python;Likes whitespace
3;Ruby;Likes blocks
EOF;

function input_parser($input) {
    foreach (explode("\n", $input) as $line) {
        $fields = explode(';', $line);
        $id = array_shift($fields);

        yield $id => $fields;
    }
}

foreach (input_parser($input) as $id => $fields) {
    echo "$id:\n";
    echo "    $fields[0]\n";
    echo "    $fields[1]\n";
}
```

`input_parser` 함수는 제너레이터 함수이다. 이 제너레이터의 `yield`는 `yield $id => $fields`으로 키와 값을 `foreach` 구문에 전달한다.

`input_parser($input) as $id => $fields`으로 생성한 제너레이터를 순회할 때, `yield`에서 전달된 키-벨류를 반환한다.

### 자동 키 생성

```php
function gen_three_nulls() {
    foreach (range(1, 3) as $i) {
        yield;
    }
}

var_dump(iterator_to_array(gen_three_nulls()));
```

위 제너레이터의 `yield`의 인자는 세팅되지 않았다. 하지만, 해당 단계의 `yield` 값은 `null`이 되며, 키-벨류 쌍의 배열로 변경할 때 생성된 순서대로 0,1,2...으로 인덱스가 생성된다.

### yield 참조 (Yielding by reference)

php에서 참조 기호는 `&`를 변수에 함께 표기하는 방식으로 사용한다. 제너레이터의 참조 표기는 조금 독특한데, 함수명에 `&`를 쓰지만, 참조하는 값은 `yield` 부분이 참조 된다. 이런 표기의 문제는 마치 함수를 참조하는 것 처럼 생각하게 한다는 것이다.

제너레이터는 `yield` 단계를 가진다. 곧, 일반적인 함수와 달리, 어느 `yield` 단계에 위치해 있는지에 대한 상태 정보를 가지고 있다. 참조 표기된 제너레이터를 사용해서 제너레이터 객체를 생성하면, 다른 제너레이터들과 `yield` 단계를 공유할 수 있는 참조형 제너레이터를 만들 것 같은 느낌을 주지만, `yield` 단계를 공유하는 것이 아니라, `yield` 값을 참조하는 표기를 함수에 한다는 것이라는 것에 유의하자.

'제너레이터 참조'라는 용어로 기억할 수 있는데, Yielding by reference으로 '참조에 의한 yield의 동작' 정도의 의미이다. `&`표기는 제너레이터 함수에 하지만, 제너레이터 함수에 대한 참조가 아닌, `yield`에 대한 참조이다.

#### yield의 인자로 변수 사용

`yield` 값이 참조 되어야 하기 때문에 `yield`의 인자로는 변수가 할당 되어야 한다. 다음과 같이 `yield` 다음에 변수에 할당되지 않은 값이 인자로 주어지면 에러가 발생한다. `yield`는 변수가 아닌 특정 값을 전달하거나 담고 있는 키워드로서 변수가 아니기 때문에 참조의 대상이 아니다. 따라서 참조 표현을 사용하면 다음과 같은 에러가 발생한다.

```php
function &genFn() {
    yield 1;
    yield 2;
    yield 3;
};

$gen = genFn();
$gen->next(); // Notice: Only variable references should be yielded by reference
```

다음과 같이 yield에 변수를 인자로 할당하면 문제 위의 에러가 발생하지 않는다.

```php
function &genFn() {
    $var1 = 1;
    $var2 = 2;
    $var3 = 3;

    yield $var1;
    yield $var2;
    yield $var3;
};

$gen = genFn();
$gen->next();
```

#### 외부에서 참조 yield 값 변경

```php
function &gen_reference() {
    $value = 3;

    while ($value > 0) {
        yield $value;
    }
}

/*
 * Note that we can change $number within the loop, and
 * because the generator is yielding references, $value
 * within gen_reference() changes.
 */
foreach (gen_reference() as &$number) {
    echo (--$number).'... ';
}
```

참조 `yield`를 사용하면, `yield` 값으로 반환되는 변수는 참조할 수 있는 값이 되며, `as &$number`와 같이 `yield` 값을 받는 변수에 `&` 표기를 사용하는 것으로 `yield` 인자에 할당된 값을 참조하는 변수를 만들 수 있다. 이를 통해서 제너레이터 외부에서 참조 값을 변경하면, 제너레이터 내부의 변수 값이 바뀌게 된다.

### 제너레이터와 스프레드 연산자

스프레드 연산자도 이터레이터를 기반으로 동작하기 때문에, \[...배열\] 뿐만 아니라, \[...제너레이터\]의 방식으로 스프레드 연산자로 언패킹을 할 수 있다.

```php
function genFn() {
    yield 1;
    yield 2;
    yield 3;
};

var_dump([...genFn()]);
```

## 제너레이터를 사용하는 상황

### queue로 사용하기

간단한 큐를 구성할 때, 배열 대신에 사용할 수 있다. 배열과 달리, 큐의 원소를 꺼낼 때 실행이 되기 때문에 조금을 꺼내 쓰기 위해서 전체 배열을 로드하는 일을 하지 않아도 된다.

데이터의 평가가 특정한 순서대로 이뤄져야 할 경우, 각각의 단계에서 앞선 단계와는 다른 로직으로 동작해야 하는 경우, 순차성 및 서로 다른 로직을 정의하기 좋다.

### foreach 구문에 전달할 iterator로 사용하기

`Iterator` 인터페이스를 구현하는 것은 여러 메소드를 모두 만들어야 하기 때문에 시간이 많이 걸린다. 단방향 조회만 하는 경우 제너레이터를 사용하여 `foreach` 구문에 사용 가능한 `Iterator`를 간단하게 만들 수 있다.

### 누적 값 정의하기

제너레이터는 `send`를 통해서 `yield` 값을 세팅할 수 있다. 이전의 `yield` 값을 사용하여 다음 `yield` 값을 만들거나, 제너레이터 함수 내의 지역 변수를 통해서 누적된 값을 생성할 수 있다.

제너레이터는 `Iterator`와 달리 함수의 반환값을 갖기 때문에 모든 순회가 끝난 후, 누적된 최종 결과값을 취득할 수 있다.

### 지연 평가 이용하기

제너레이터는 다음 단계로 진행하는 특수한 문법(foreach의 loop와 같은 것)이나 메소드(next)를 사용하므로 이들을 사용하지 않는다면 제너레이터는 다음 단계의 yield로 진행하지 않는다. 이런 특성은 다음 단계로 진행시켰을 때만 코드를 실행하기 때문에, 긴 흐름의 로직을 구성할 때, 코드가 전부 실행되지 않고 도중에 처리가 멈췄을 때, 앞선 로직에서 당장 필요하지 않아서 실행하지 않는 코드를 만들기 때문에 시스템 리소스를 절감하는 코드를 작성할 수 있다.

#### 함수형 프로그래밍의 지연평가

함수형 프로그래밍에서 지연 평가라는 개념이 존재한다. 원본 함수가 평가되기 위해 요구하는 모든 인자를 받지 않을 때 함수의 반환 값으로, 아직 받지 못한 인자를 받을 수 있는 함수를 반환하고, 이 반환된 함수가 아직 인자를 다 받지 못하면 마찬가지로 함수의 반환값으로 인자를 받을 수 있는 함수를 반환하고, 인자를 모두 받으면 평가되는 것이 지연 평가이다.

일반적으로 지연 평가는 커링이라는 방식을 통해서 이뤄지는데, 커링은 인자를 일부만 받고, 아직 함수를 평가하기에 필요한 충분한 인자가 전달되지 않은 경우, 나머지 인자를 받을 수 있는 함수를 반환한다. 그리고 나머지 인자를 받을 수 있는 함수가 더이상 반환되지 않으면, 원본 함수가 평가된 결과값을 반환한다.

# References

- https://www.php.net/manual/en/language.generators.overview.php
