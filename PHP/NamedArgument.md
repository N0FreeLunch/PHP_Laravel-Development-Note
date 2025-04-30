# 명명된 인자

명명된 인자란 인자를 전달할 때 파라메터 이름을 전달하여, 함수의 파라메터가 정의된 순서에 관계 없이 인자를 전달할 수 있도록 하는 문법이다.

## 함수 파라메터의 특징

php에서 함수 파라메터의 주요 특징으로는 가변 인자를 가장 마지막에 배치하고, 디폴트 값이 있는 파라메터 다음에는 디폴트 값이 없는 파라메터를 위치시킬 수 없다.

제네릭이 없기 때문에, 배열의 원소 타입을 명확하게 하기 위해서는 가변 인자를 주로 사용한다.

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

## 명명된 인자의 필요성

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
