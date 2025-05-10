# Generator

## 배열의 문제

PHP의 foreach와 같은 문법은 배열을 순회할 수 있는 기능을 제공한다. php에서 배열은 array의 형태를 docblock generic이나 array shape로 적어주지 않는 한, 배열 안에 든 원소가 무엇인지 정적 분석툴로 추론할 수 없다는 단점을 가진 문법이다.

배열의 타입 추론이 docblock에 의존하는 문제 때문에, 보일러 플레이트가 좀 더 있더라도, DTO 클래스를 사용하거나, Iterator 클래스를 사용하는 문법 등을 활용하는 방법이 추천되기도 한다. 하지만 이 방식은 지나치게 장황할 수 있어서 타입 추론이 잘 되지만, 많은 개발자들에게 광범위하게 채택되지 않는 문제점이 존재한다.

## Generator 사용 이유

Iterator 인터페이스를 구현하는 것은 `current`, `key`, `next`, `rewind`, `valid` 등의 메소드를 모두 구현해야 하기 때문에 보일러 플레이트가 장황하다. 순 방향 조회의 순회만 할 경우, Iterator 인터페이스에서 `next`만을 사용하는 방법으로 사용한다고 하면, Generator는  사용해서 보일러 플레이트를 줄이는 코드를 작성할 수 있다.

## 제너레이터의 특징

### 메모리 소모

일반적인 자료 구조는 메모리에 순회 대상의 데이터를 모두 올려 두는 방식을 사용한다. 하지만 제너레이터는 모든 데이터를 메모리에 올려 두는 것이 아니라, 순회에 필요한 데이터만을 생성하는 방식으로 동작한다.

모든 데이터를 미리 생성하면, 반복되는 사용에는 유용하지만 1회성 작업에는 많은 메모리를 소비하기 때문에 적절한 방법은 아니다. 순회와 동시에 1회만 취득하는 경우 제너레이터를 사용해서 메모리 소비를 최소한으로 하여 사용가능하다.

### iterator의 인터페이스 사용 가능

제너레이트는 iterator의 인터페이스와 동일한 인터페이스를 갖는데, iterator의 인터페이스인 `current`, `key`, `next`, `rewind`, `valid` 뿐만 아니라, `send`, `getReturn`, `throw`, `_​_​wakeup` 등의 메소드도 추가적으로 사용할 수 있다. iterator의 인터페이스를 가지고 있기 때문에 iterator를 활용하는 다양한 구문에 제너레이터를 사용할 수 있다.

## 제너레이터의 기본 문법

제너레이터의 메소드는 다음과 같다.

#### current

```
public Generator::current(): mixed
```

현제의 yielded 값을 반환한다.

#### getReturn

```
public Generator::getReturn(): mixed
```

함수의 반환 값을 반환한다.

```
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

만약 함수 내에 정의된 모든 yield를 밟는 절차가 끝나지 않은 상태에서 `getReturn()`를 반환하면 다음과 같은 에러가 반환된다.

> Fatal error: Uncaught Exception: Cannot get return value of a generator that hasn't returned 

#### key

```
public Generator::key(): mixed
```

yielded 값의 키를 반환한다. 이 때 키는 몇 번째의 yield인지를 파악하기 위해서 사용된다.

## 제너레이터의 예

```
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

## 함수형 프로그래밍

함수형 프로그래밍에서 지연 평가라는 개념이 존재한다. 원본 함수가 평가되기 위해 요구하는 모든 인자를 받지 않을 때 함수의 반환 값으로, 아직 받지 못한 인자를 받을 수 있는 함수를 반환하고, 이 반환된 함수가 아직 인자를 다 받지 못하면 마찬가지로 함수의 반환값으로 인자를 받을 수 있는 함수를 반환하고, 인자를 모두 받으면 평가되는 것이 지연 평가이다.

일반적으로 지연 평가는 커링이라는 방식을 통해서 이뤄지는데, 커링은 인자를 일부만 받고, 아직 함수를 평가하기에 필요한 충분한 인자가 전달되지 않은 경우, 인자를 받을 수 있는 함수를 반환한다.

# References

- https://www.php.net/manual/en/language.generators.overview.php
