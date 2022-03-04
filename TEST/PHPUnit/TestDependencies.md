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
