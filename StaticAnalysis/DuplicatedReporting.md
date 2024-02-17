## 정적 분석 도구의 중복된 보고 제거
```php
<?php declare(strict_types=1);

class A {
	public ?string $value = null;
}

$aObj = new A();

var_dump($aObj->value ?? 'foo'); // value is always defined
var_dump($aObj->value ?: 'bar');
var_dump($aObj->value2 ?? 'foo'); // value is never defined
var_dump($aObj->value2 ?: 'bar');
```
> Access to an undefined property A::$value2.
- 기본적으로 정의되지 않은 속성에 접근하는 것을 문제로 보고 정적 분석 도구는 이를 보고한다.
- 문제의 요지는 `$aObj->value2 ?? 'foo'` 부분에서도 보고되어야 하는데, 왜 `$aObj->value2 ?: 'bar'`만 보고하냐는 뜻이다.
- 이는 최종 사용 대상만 보고하여 정적 분석 도구가 보고하는 리스트를 줄이기 위해서이다. 

## Reference
- https://github.com/phpstan/phpstan/issues/10418
