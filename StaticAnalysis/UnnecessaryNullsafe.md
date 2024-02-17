## 불필요한 nullsafe 연산자 사용
```php
<?php declare(strict_types = 1);

class HelloWorld
{
    public function sayHello(): void
    {
        $object = null;
        $id = $object?->id;
    }
}
```
> Cannot access property $id on null.
- 명시적으로 null인 대상에 `?->`를 사용하는 것은 불필요하므로 정적 분석 도구에 의해 보고된다.

### 대응
- 불필요한 연산자를 사용하지 말자.
- 반드시 오브젝트만 할당되는 변수에 `?->`을 사용할 필요가 없고 `->`만 사용하면 된다. 불필요하게 nullsafe 연산자를 남용하지 말자.

## Reference
- https://github.com/phpstan/phpstan/issues/10411
