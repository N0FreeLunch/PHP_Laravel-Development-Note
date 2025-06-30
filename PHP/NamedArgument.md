# 명명된 인자

명명된 인자란 인자를 전달할 때 파라메터 이름을 전달하여, 함수의 파라메터가 정의된 순서에 관계 없이 인자를 전달할 수 있도록 하는 문법이다.

php 8 부터 명명된 인자를 사용할 수 있게 되었다.

# 도입

# 본론

## 예제 코드

```php
$fn = function ($a, $b, $c) {};
$fn(c:1, a:2, b:3);
```

라는 함수가 있다면 파라메터의 순서대로 인자를 전달하는 것이 아니라, 파라메터명을 사용하여 파라메터의 순서와 인자의 순서를 다르게 전달할 수 있는 기능이다.

## php 함수 파라메터의 특징

### 가변인자의 문제

php에는 제네릭이 없기 때문에, 일관된 타입의 배열을 함수의 파라메터로 전달하고자 할 때, 네이티브 문법으로 나타내기 위해서 가변 인자를 주로 사용한다.

물론 docblock generic을 사용할 수도 있지만, 개인적으로 네이티브 문법을 선호하기 때문에 특별한 일이 있지 않다면 docblock을 사용하기 보다는 가변 인자를 사용하는 코딩 스타일을 쓰고 있다.

그런데 가변 인자는 파라메터의 가장 마지막에 위치해야 하고, 하나의 함수 당 하나의 가변 인자만 사용할 수 있다는 단점이 있다.

### 디폴트 값을 가진 파라메터의 위치 문제

```php
function ($param1, $paramWithDefault = 100, ...$variadicParams);
```

위의 코드는 가능하지만, 다음 코드는 불가능하다.

```php
function ($paramWithDefault = 100, $param1, ...$variadicParams);
```

```php
function (...$variadicParams, $paramWithDefault = 100, $param1);
```

왜냐하면, 파라메터에 디폴트 값이 있는 경우, 디폴트 값이 없는 파라메터가 디폴트 값이 부여된 파라메터 다음에 위치할 수 없다는 룰이 있기 때문이다.

## 명명된 인자를 사용해야 할 때

1. 명명된 인자는 사용하고자 하는 인자의 전달 방식이 파라메터의 순서와 다를 때 사용한다.
2. 코드를 사용할 때 파라메터 명을 표시하여 함수의 정의를 읽지 않고도 어떤 인자들이 전달되어야 하는 함수인지 간단히 알 수 있다는 장점이 있다.

## 반드시 명명된 인자를 사용할 필요가 없다.

```
function getUserById(int $id): User {}
```

위와 같은 함수 시그니처의 경우, 함수 명에 `byId`가 있어 id를 사용하는 것을 알 수 있기 때문에 명명된 파라메터를 굳이 사용할 필요가 없다.

## 예제를 통해서 이해하기

다음과 같은 시그니처가 있다고 하자.

```php
function isUpdatedBy(int $userId, bool $excluded = false) {}
```

`isUpdatedBy`를 사용할 때는 `isUpdatedBy(userId: $id)`, `isUpdatedBy(userId: $id, excluded: true)`로 사용하는 것이 좋다.

```php
enum StatusEnum
{
    case Active;
    case Deleted;
    case Stopped;
}

function isInStatus(bool $excluded = false, ...$statuses) {}
```

`isInStatus`를 사용할 때는 `isInStatus(statuses: [StatusEnum::Deleted])`, `isInStatus(statuses: [StatusEnum::Deleted], excluded: true)`으로 사용할 수 있다.

문제는 동일하게 `bool $excluded = false`를 정의하는데, 어떤 경우는 두 번째 파라메터가 되고, 어떤 경우는 첫 번째 파라메터가 된다는 것이다. 만약 명명된 인자가 없다면,

`isInStatus(false, ...[StatusEnum::Deleted, StatusEnum::Stopped])`으로 사용해야 한다. `excluded`는 기본 `false`라서 생략하고 싶은데, 파라메터가 정의된 순서대로 적어야 하기 때문에 코드가 우아해지지 못한다는 문제가 있다.

명명된 인자를 사용하면 `excluded` 인자를 뒤로 배치하여 생략할 수 있다는 장점이 있다.

## 명명된 인자의 문제

명명된 인자를 사용하는 쪽에서는 함수의 파라메터를 쓰기 때문에, 함수 파라메터명이 달라지게 되면, 해당 함수를 사용하는 모든 코드를 바꿔야 한다는 문제점이 있다.

## 몀명된 인자 코딩 스타일 채용

명명된 인자를 사용하면, 함수의 인자에서 디폴트 값이 있는 것을 생략할 수 있고, `isUpdatedBy` 처럼 영문법상 `id`가 먼저 나오고, `excluded`가 나중에 나오는 것이 자연스러운데, 인자의 전달 순서를 바꿀 수 있어서 자연스런 영문법 표현이 가능하다.
