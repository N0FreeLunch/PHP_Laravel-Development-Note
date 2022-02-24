## start unit test
- 태스크 코드를 작성하기 위해서 `PHPUnit\Framework\TestCase` 클래스를 상속 받아야 한다.
- `test*` : 메소드명를 접미사가 test인 메소드를 작성하거나 `@test` 어노테이션을 주석을 달아 준다.
- `assertSame()`과 같은 [Assertions](https://phpunit.readthedocs.io/en/latest/assertions.html#appendixes-assertions) 메소드를 사용하여 실제값이 기대값과 같은지 확인한다.

```
<?php declare(strict_types=1);
use PHPUnit\Framework\TestCase;

final class StackTest extends TestCase
{
    public function testPushAndPop(): void
    {
        $stack = [];
        $this->assertSame(0, count($stack));

        array_push($stack, 'foo');
        $this->assertSame('foo', $stack[count($stack)-1]);
        $this->assertSame(1, count($stack));

        $this->assertSame('foo', array_pop($stack));
        $this->assertSame(0, count($stack));
    }
}
```
- `$this->assertSame(0, count($stack));` assertSame은 두 값이 같은지 확인한다. `0`과 `$stack`이란 배열의 길이인 `count($stack)`가 같은지 확인한다.
- `array_push($stack, 'foo');` 빈 배열 `$stack`에 `'foo'`를 넣는다.
- `$this->assertSame('foo', $stack[count($stack)-1]);` 배열의 마지막 인덱스인 `count($stack)-1`, `$stack` 배열의 마지막 원소인 `$stack[count($stack)-1]`와 `'foo'`가 같은지 확인한다.
- `$this->assertSame(1, count($stack));` `'foo'`라는 값이 들어 있는 배열 `$stack`의 길이가 1인지 확인한다.
- `$this->assertSame('foo', array_pop($stack));` `$stack` 배열의 원소를 마지막 원소 부터 뽑는 array_pop 함수를 사용했을 때 추출된 값이 `'foo'`인지 확인한다.
- `$this->assertSame(0, count($stack));` array_pop에 의해 추출되어 배열에서 `'foo'`가 날아간 배열 `$stack`의 배열의 길이가 0인지 확인한다.

```
<?php declare(strict_types=1);
use PHPUnit\Framework\TestCase;

final class StackTest extends TestCase
{
    public function testEmpty(): array
    {
        $stack = [];
        $this->assertEmpty($stack);

        return $stack;
    }

    /**
     * @depends testEmpty
     */
    public function testPush(array $stack): array
    {
        array_push($stack, 'foo');
        $this->assertSame('foo', $stack[count($stack)-1]);
        $this->assertNotEmpty($stack);

        return $stack;
    }

    /**
     * @depends testPush
     */
    public function testPop(array $stack): void
    {
        $this->assertSame('foo', array_pop($stack));
        $this->assertEmpty($stack);
    }
}
```
- `testEmpty` 메소드에서 빈 배열 `$stack`이 비어있는지 `assertEmpty($stack);`으로 확인하였다.
- `testPush` 메소드에서 `@depends testEmpty` 부분에 의해 `testEmpty`를 실행한 결과를 `testPush`의 인자로 주입받는다. 주입받은 배열 `$stack`에 `array_push`로 `'foo'`라는 값을 넣는다. 
- `$this->assertSame('foo', $stack[count($stack)-1]);`는 배열의 마지막 원소 `$stack[count($stack)-1]`가 `'foo'`인지 확인한다.
- `$this->assertNotEmpty($stack);` 배열이 비어 있지 않은지 확인한다.
- `testPop` 메소드는 `@depends testPush`에 의해 `testPush` 메소드에 의존성을 가진다. `testPush` 메소드의 태스트 결과를 `testPop` 메소드의 인자로 주입 받으므로 `$stack` 배열에는 `'foo'`가 들어 있다.
- ` $this->assertSame('foo', array_pop($stack));` `$stack` 배열에서 마지막 값 하나를 꺼냈을 때 값이 `'foo'`인지 확인한다.
- `$this->assertEmpty($stack);`은 `array_pop`으로 배열의 원소가 하나 추출되어서 빈 배열이 되었는지 확인한다.

## Reference
- https://phpunit.readthedocs.io/en/latest/writing-tests-for-phpunit.html
