## 배열에서 수의 형태로 된 문자열 키는 정수로 변환된다.
```php
class HelloWorld
{
    private const GROUPS = [
       '123'    => 0.0,
       '123k' => 0.0,
    ];

    /**
    * @return array<string,float>
    */
    public function sayHello(): array
    {
        return self::GROUPS;
    }
}
```
> Method HelloWorld::sayHello() should return array<string, float> but returns array<int|string, float>
- `array<string,float>` 주석에 의해서 키를 문자열로, 값을 float 타입으로 반환한다고 되어 있다.
- 배열의 키가 Numeric인 경우 php는 배열의 키를 수로 변환한다.

### 대응
- php에서 배열의 키 값은 항상 문자열이 되는 것이 아니며, 정수 Numeric일 경우 키가 정수로 변환되기 때문에 값의 타입을 신뢰할 수 없다는 문제점이 있다.
- 따라서 키는 구분의 역할을 하는 용도로 사용해야 하고, 변환되면 안되는 값을 배열의 키로서 사용하면 안 된다는 것을 알 수 있다.

## Reference
- https://github.com/phpstan/phpstan/issues/10486
