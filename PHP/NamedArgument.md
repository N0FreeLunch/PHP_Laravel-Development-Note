# 명명된 인자

명명된 인자란 인자를 전달할 때 파라메터 이름을 전달하여, 함수의 파라메터가 정의된 순서에 관계 없이 인자를 전달할 수 있도록 하는 문법이다.

php 8 부터 명명된 인자를 사용할 수 있게 되었다.

# 도입

명명된 인자는 코드의 가독성을 올리지만, 파라메터의 이름을 나열해야 하기 때문에 타이핑이 귀찮은 단점이 있다. 명명된 인자를 사용하면 좋을 때와 나쁠 때를 생각해 보는 것으로 좀 더 가독성 좋은 코드를 작성할 수 있다.

# 본론

## 예제 코드

```php
$fn = function ($a, $b, $c) {};
$fn(c:1, a:2, b:3);
```

라는 함수가 있다면 파라메터의 순서대로 인자를 전달하는 것이 아니라, 파라메터명을 사용하여 파라메터의 순서와 인자의 순서를 다르게 전달할 수 있는 기능이다.

## php 함수 파라메터의 특징

명명된 인자의 사용에 대해 생각하기 전에 php 함수의 파라메터 특징을 좀 더 생각해 보자.

### 가변인자의 문제

php에서 데이터 세트를 함수나 메소드에 전달하고 싶을 때는 배열 타입의 파라메터를 사용한다. 하지만, 배열 파라메터의 경우, 배열 내부의 데이터 구조를 알 수 없기 때문에 dockbloc generic이나 array shape 등의 주석을 추가하는 편이 좋다.

개인적으로 dockbloc은 보일러 플레이트가 있다보니, 여러 배열을 전달하거나 대량의 데이터를 함수에 전달하는 것이 아니라면, 약간의 성능 저하가 있더라도 가변 파라메터를 사용하는 코딩 스타일을 선호한다.

가변 파라메터를 사용하면, `fn(...['a', 'b', 'c'])`으로 가변 파라메터에 배열로 인자를 전달할 수도 있고, `fn('a', 'b', 'c')` 그저 나열하는 방법으로 파라메터를 전달할 수 있으며, 문법적인 간략함과 더불어 런타임 타입체크를 활용할 수 있다.

가변 파라메터는 동일한 타입의 데이터로 이뤄진 데이터 세트를 전달할 때 간단한 장점이 있지만, 함수를 정의할 때, 파라메터의 가장 마지막에 위치해야 하고, 하나의 함수 당 하나의 가변 인자만 사용할 수 있다는 단점이 있다.

### 디폴트 값을 가진 파라메터의 위치 문제

```php
function ($param1, $paramWithDefault = 100, ...$variadicParams);
```

위의 코드는 가능하지만, 다음 두 코드는 불가능하다.

```php
function ($paramWithDefault = 100, $param1, ...$variadicParams);
```

```php
function (...$variadicParams, $paramWithDefault = 100, $param1);
```

왜냐하면, 파라메터에 디폴트 값이 있는 경우, 디폴트 값이 없는 파라메터가 디폴트 값이 부여된 파라메터 다음에 위치할 수 없다는 룰이 있기 때문이다.

## 언제 명명된 인자를 사용하면 좋을까?

1. 함수를 사용할 때 인자의 전달 순서를 파라메터의 순서와 다르게 하고 싶을 때 사용한다.
2. 코드를 읽을 때 파라메터 명을 표시하여 함수가 정의된 부분을 확인하지 않고도 어떤 인자들이 전달되는지 표기하고 싶을 때 사용한다.
3. 가변 인자에 데이터를 전달할 때, 디폴트 값이 부여된 파라메터의 인자 전달을 생략하고 싶을 때 사용한다.

### 항상 명명된 인자를 사용하는 것이 좋지 않을 이유

```php
function getUserById(int $id): User {}
```

위의 함수 시그니처의 경우, 함수 명에 `byId`가 있어 id를 전달하는 것을 직관적으로 알 수 있는데, 굳이 추가 정보인 명명된 인자를 사용할 필요는 없다.

```php
function biggerThan(int $a, int $b): bool {}
```

위의 함수 시그니처의 경우, `A is bigger than B`의 구문을 떠올리기 때문에, 첫 번째 인자가 두 번째 인자 보다 큰지를 판별하는 함수라고 생각할 수 있다. 만약 두 번째 인자 보다 첫 번째 인자가 큰지를 판별하는 함수였다면 함수를 상식적으로 만들라는 조언을 해 줄 수 있을 것이다.

```php
function subtract(int $a, int $b): int {}
```

위의 함수 시그니처의 경우, 첫 번째 인자에서 두 번째 인자를 빼는 함수라고 생각할 수 있다. 만약 두 번째 인자에서 첫 번째 인자를 빼는 함수였다면 함수를 상식적으로 만들라는 코드리뷰를 할 것이다.

#### 알 수 있는 것

이렇듯 명명된 파라메터 없이도 함수의 이름만으로 역할을 하는지 알 수 있기 때문에 굳이 정보를 추가하는 명명된 인자를 사용할 필요는 없다.

## 명명된 인자를 사용해야 할 때

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

`isInStatus(false, ...[StatusEnum::Deleted, StatusEnum::Stopped])`으로 사용해야 한다. `excluded`는 기본 `false`라서 생략하고 싶은데, 파라메터가 정의된 순서대로 적어야 하기 때문에 코드가 우아해지지 못하며, `excluded`가 어떤 옵션으로 쓰였는지 함수의 정의를 확인해야 한다.

명명된 인자를 사용하면 `excluded` 인자를 뒤로 배치하여 디폴트 값이 부여된 파라메터에 대한 인자 전달을 생략할 수 있다는 장점이 있다. 또한 전달하려는 인자의 의미를 분명히 하여, 함수의 정의된 파라메터를 직접 읽지 않고서도 어떤 의미의 인자를 전달하는지 알 수 있다.

디폴트 값이 붙은 파라메터는 생략할 수 있으므로 다음과 같이 전달하려는 인자를 생략하여 불필요한 정보를 제거한 코드를 만들 수 있다.

```php
isInStatus(statuses: ...[StatusEnum::Deleted, StatusEnum::Stopped], excluded: false);
```

```php
isInStatus(statuses: ...[StatusEnum::Deleted, StatusEnum::Stopped]);
```

## 명명된 인자의 문제

명명된 인자를 사용하는 쪽에서는 함수의 파라메터를 쓰기 때문에, 함수 파라메터명이 달라지게 되면, 해당 함수를 사용하는 모든 코드를 바꿔야 한다는 문제점이 있다.

그러므로 모든 함수에 명명된 인자를 사용하기 보다는 함수명으로 어떤 인자를 전달해야 하는지 알 수 있는 경우는 굳이 사용하지 않는 편이 좋으며, 함수명으로 전달하려는 파라메터가 무엇인지 몇 번째 인자로 전달해야 하는지 알기 어려운 경우에는 명명된 인자를 사용하는 편이 좋다.

## 인간의 언어처럼 표현하기

명명된 인자를 사용하면, 함수의 인자에서 디폴트 값이 있는 것을 생략할 수 있고, `isUpdatedBy` 처럼 영문법상 `id`가 먼저 나오고, `excluded`가 나중에 나오는 것이 자연스러운데, 인자의 전달 순서를 바꿀 수 있어서 자연스런 영문법 표현이 가능하다는 장점이 있다.

## 싱글 엑션 클래스와 비교

명명된 인자가 없던 시절에는 코드를 읽을 때 함수의 정의로 이동하거나, 변수에 파라메터로 전달하려는 대상을 담아서 전달하는 방식 등을 사용했다. 또 다른 방법으로는 setter로 필요한 값을 전달받고, action 함수를 하나 정의하여 객체를 함수 대신에 사용하는 방법이 있었다.

하나의 엑션을 가진 다양한 클래스를 정의할 수 있지만, 대표적인 예로 빌드 패턴을 들 수 있다.

```php
class User {
    private $name;
    private $age;
    private Gender $gender;

    public function setName(string $name): self {
        $this->name = $name;
        return $this;
    }

    public function setAge(int $age): self {
        $this->age = $age;
        return $this;
    }

    public function setGender(Gender $gender) {
        $this->age = $gender;
        return $this;
    }

    public function build(): User {
        return $this;
    }
}

(new User)->setName('yamada')->setAge(30)->setGender(Gender::Male)->build();
```

위와 같이 action은 build 메소드 하나이며 필요한 값은 setter를 통해서 받았다.

# 마지막으로

php에 명명된 인자가 없던 시절에는, 싱글 엑션 클래스 또는 인자를 파라메터의 의미의 변수에 담아서 전달했지만, php에 명명된 인자가 생기고 나서는 좀 더 간단하게 가독성 좋은 함수 인자 전달 방식을 사용할 수 있게 되었다.

명명된 인자를 사용하면 인자의 순서를 달리 전달할 수 있어, 영문법의 수순을 따르는 코드를 작성할 수 있기 때문에 코드를 읽을 때 글을 읽는 듯한 편안함을 준다. 필요에 따라 적극 사용하는 것으로 가독성을 높일 수 있다.
