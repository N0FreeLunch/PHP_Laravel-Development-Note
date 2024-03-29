## 0으로 배우는 프로그래밍 원리

### 변수의 초기값에 null을 사용하자는 주의
- 어떤 변수의 초기값을 설정하는데 null이 좋을지 빈 값이 좋을지에 대해 생각을 해 보자.
- php의 다양한 문법 또는 함수에서 처리할 대상 타입이 아닌 null 타입이 들어오면 에러 또는 경고(Warning)을 발생시킨다. 경고는 php 버전이 업그레이드 될 때 에러로 변경될 예정이라는 의미이므로 null을 사용하지 않도록 로직을 짜 두라는 의미를 갖고 있다.
- 다른 타입의 값이 와야 하는데 null 타입이라서 불일치를 통해서 에러가 발생하면, 할당되지 않은 변수가 로직에 전달되었으므로 처리할 수 없음을 알려주기 때문에 비할당된 값이 전달되었다는 것을 알 수 있다. 이런 면을 보았을 때 빈 값 보다는 null을 초기값으로 할당하는 것이 할당 비할당을 명시적으로 구분할 수 있기 때문에 좋아 보일 수 있다.

### 변수의 초기값에 가능하면 빈 값을 사용하자는 주의
- 변수의 초기값에 빈 값을 사용하는 경우 비할당된 값의 타입이 할당된 타입과 일치하기 때문에 비할당된 값이 에러 발생 없이 처리되기 때문에 코딩에서 실수를 방지하기 어렵다는 생각을 할 수 있다.
- 또한 `변수 === null` 또는 `is_null(변수)`을 사용하는 것이나, `변수 === 0` `변수 === ''` `변수 === []`이나 어차피 분기는 똑같이 처리해야 하기 때문에 빈 값을 할당하는 것이 로직의 분기 복잡성을 줄여주지 않는다고 생각할 수 있다.
- 하지만 빈 값인 경우 아무런 처리도 일어나지 않는 스타일의 코딩을 통해서 로직의 분기를 줄일 수 있다. 이를 이해하기 위해 먼저 숫자 0에 대한 예를 생각해 보자.

### 숫자 0
#### 0에 대한 수 n의 연산
- 0에 어떤 값(n)을 더했을 때, 결과는 n이 된다.
- 0에 어떤 값(n)을 뺐을 때, 결과는 n이 된다.
- 0에 어떤 값(n)을 곱했을 때, 결과는 0이 된다.
- 0애 어떤 값(n)을 나누었을 때, 결과는 0이 된다.
- 0에 대한 곲셈과 나눗셈을 생각해 보자. **0에 어떤 값을 연산 했을 때, 그 결과는 0으로 어떤 값(n)과 연산은 존재하지 않았던 것과 다름없다.**

#### n에 대한 수 0의 연산
- 어떤 값(n)애 0을 더했을 때 결과는 n이 된다.
- 어떤 값(n)애 0을 뺐을 때 결과는 n이 된다.
- 어떤 값(n)에 0을 곱했을 때 결과는 0이 된다.
- 어던 값(n)에 0을 나누었을 때 결과는 무한대가 된다. php에서는 에러가 발생한다.
- 어떤 값(n)에 대한 덧셈과 뺄셈을 생각해 보자. **n에 0을 연산 했을 때, 그 결과는 n으로 0의 연산은 존재하지 않았던 것과 다름없다.**

### 빈 값
- 빈 값도 0의 예시 처럼 아무런 영향을 주지 않는 값으로 사용하는 코드를 만들 수 있다.
- 빈 값에 대한 로직의 처리 전후가 아무런 결과의 변경이 없다면, 빈 값은 사용되지 않은 것과 다름없다.
- 많은 경우 비할당인 경우에 로직을 처리하지 않는 코드를 많이 작성하는데, 빈 값의 사용을 통해서 로직의 처리 전후의 결과가 동일하다면 비할당인 경우 로직을 처리하지 않는 것과 동일한 효과를 가진다. 이를 통해서 변수가 비할당인지를 체크하는 if 문의 작성을 줄일 수 있다.

### 빈 값이 처리의 영향을 주지 않는 예제 코드

#### dot
- dot(.)은 문자열을 결합하는 연산이다. 임의의 문자열에 대해서 빈 문자열을 dot(.)으로 결합하면 그 결과는 이전의 문자열과 동일하다. 빈 문자열의 `.` 연산을 통한 연결은 처리 전후의 결과가 동일한 결과를 얻는다.
```php
$var = "";
echo "hello".$var;
```
```php
$var = null;
if(!is_null($var)) {
    echo "hello".$var;
}
```
- 빈 문자열을 사용하는 것을 통해서 if문의 사용을 줄일 수 있다.

#### str_replace
- 먼저 `str_replace`의 문법은 다음과 같다. `str_replace(array|string $search, array|string $replace, string|array $subject, int &$count = null): string|array`
```php
$search = "";
$subject = "subject";
echo str_replace($search, "*", $subject);
```
- `str_replace`에서 `$search` 변수에 해당하는 문자열 값을 찾아서 `*`으로 바꾸는 코드를 실행한다고 하자.
- `$search`가 빈 문자열인 경우 대체할 대상을 아무것도 찾지 않으므로 대체할 대상이 없어서 위의 결과는 `"subject"`가 그대로 나온다.
```php
$search = null;
$subject = "subject";
if(!is_null($search)) {
    echo str_replace($search, "*", $subject);
} else {
    echo $subject;
}
```
- 만약 `$search`에 빈 값이 아닌 `null`을 할당했다면, 위와 같이 분기 처리가 늘어날 것이다.

#### 스프레드 연산자
```php
$oneToFive = [1, 2, 3, 4, 5];
var_dump([10, 11, 12, ...$oneToFive]);
```
- `[10, 11, 12, ...$oneToFive]`은 `[10, 11, 12]` 배열 안에 스프레드 연산자 형태의 배열을 포함될 때 `$oneToFive` 배열 안의 원소들이 `[10, 11, 12]`의 배열에 포함되어 나온다.
```php
$arr = [];
var_dump([10, 11, 12, ...$arr]);
```
- 빈 배열을 스프레드 연산자로 포함하면, `[10, 11, 12]`으로 아무 원소도 추가되지 않은 결과가 나온다.
```php
$arr = null;
if(!is_null($arr)) {
    var_dump([10, 11, 12, ...$arr]);
} else {
    var_dump([10, 11, 12]);
}
```
- 만약 `$arr`에 빈 값이 아닌 `null`을 할당했다면, 위와 같이 분기 처리가 늘어날 것이다.
- 빈 배열을 사용하는 것을 통해서 if문의 사용을 줄일 수 있다.

### 행위의 유무를 구분하는 값
- php에서 기본타입은 정수, 소수점 수(float이나 double), 배열, 문자열, 불리언, 오브젝트, callable(호출 가능한 값), iterable(반복 가능한 값), null, resource(어떤 타입으로 정의할 수 없는 나머지 타입)이 존재한다.
- 정수, 소수점 수, 배열, 문자열, 불리언은 행위를 하지 않는 의미로 쓰는 값이 존재한다. 정수의 경우 `0`, 소수점 수의 경우 `0.0`, 배열의 경우 `[]`, 불리언의 경우 `false` 등은 어떤 동작을 실행하지 않는 의미로 사용될 경우가 있다.
- 하지만 이들 행위의 유무는 어떤 연산인지에 따라 달라지며 무조건 빈값이라고 해서 결정되는 것은 아니다. 하지만 많은 경우 빈 값일 때 행위의 일어나지 않음을 표현할 수 있다.

### if문을 통한 구분의 필요 없음
- 빈 값을 변수의 초기값으로 쓰게 되면 어떤 연산에 대해서 if 문을 통해서 연산에 앞서 사용할 값인지 사용하지 말아야 할 값인지 구분할 필요가 없어진다. 어차피 초기값이 항등원 또는 빈 값이 되는 자기동형이므로 연산을 해도 동일한 값이 되기 때문에 로직을 실행하지 않은 것과 동일한 결과를 얻는다.
- if문 없이도 실행하지 않은 결과를 얻을 수 있기 때문에 어떤 연산에 대해 분기가 줄어들어 좀 더 깔끔한 로직을 만들 수 있다는 장점이 있다.

### 배열의 항등원

#### array_walk
- `array_walk(array|object &$array, callable $callback, mixed $arg = null): bool`를 생각해 보자.
- 배열 그 자체를 변경하고자 하는 경우 `&$array`으로 `&` 참조 기호를 사용하며, 배열 안에 든 값만 이용하고 싶다면 `$array`으로 참조 기호 없이 사용한다.
- 이 함수는 반환값을 갖는 함수가 아니다. 원본 값 그 자체를 변화 시키거나 원본 값을 활용한 다른 값을 만드는 것인데, 주어진 배열이 빈 배열 `[]`인 경우 주어진 콜백함수를 실행하지 않는다. 곧 결과값이 원본 그대로이다. 따라서 빈 배열은 `[]`은 `array_walk` 내장 함수에 대해 항등원의 역할을 한다.

#### array_map
- `array_map(?callable $callback, array $array, array ...$arrays): array`을 생각해 보자.
- 배열을 받아 콜백함수에 정의된 방식을 통해서 배열의 각 원소를 변경하고 새로운 배열을 만드는 함수이다.
- 빈 배열이 주어질 경우 `array_map`은 각각의 원소에 대해 콜백함수를 적용해야 하는데 원소가 없으므로 콜백함수를 적용하지 않고 원소가 없는 빈 배열을 반환한다. `array_map`은 빈 배열에 대해 항등원의 역할을 한다.

### 항등원이라고 말하기 어려울 때는?
```php
function addFive(int $num) {
    return $num;
};
echo addFive(0);
```
- `5 + 0 = 5`에서 연산 `+`에 대한 항등원은 0이다. `addFive(0)`에서 0은 항등원일까? 인자로 5를 받고 반환값으로 5를 내보냈을 때 이를 항등원이라고 하지 `addFive(0)`에서 0을 항등원이라고 하기에는 이상한 구석이 있다. 이는 연산 이전의 값이 숨겨져 있기 때문이다. 분명 `5 + 0`의 식일 때는 연산 이전의 5가 보였지만, `addFive(0)`에서는 5와 덧셈 연산이 합쳐져 있기 때문에 숨겨져 있기 때문이다. 하지만 `addOne(0) === 1`, `addTwo(0) === 2`, `addThree(0) === 3`, `addFour(0) === 4` 단순 덧셈 연산을 하는 함수에 대해서 0은 더하는 값을 결과 값으로 하는 항등원의 성격을 보여주고 있다.
- `addFive(0) == 5`, `addFive(1) == 6`, `addFive(2) == 7`, `addFive(3) == 8`, `addFive(4) == 9`, `addFive(5) == 10`의 코드를 보면 항등원이 있는 것인지 판단하기 어려울 수 있다. 이것은 함수가 의미하는 연산이 `+ 5`이기 때문이다. 항등원을 생각하기 위해서는 `5`와 `+ 0`으로 구분이 되어야 하는데 `0`과 `+ 5`으로 인식이 되기 때문이다.
```php
function addFive(array $nums) {
    return array_map(fn($e) => $e+5, $nums);
};
var_dump(addFive([]));
```
- 배열의 각 원소에 콜백 함수라는 연산을 적용해야 하는데, 빈 배열은 원소가 없으므로 연산을 적용할 수가 없다. `5 + 0`에서 `+ 0`은 값을 포함한 연산을 수행해도 결과 값이 달라지는 측면이 없다는 점이 특징이다.

### 빈 값
- 프로그래밍 언어에서 다루는 많은 경우에서 빈 값은 여러 연산의 항등원에 해당한다. 따라서 초기값으로 빈 값을 할당하는 관습이 생겼다.
- 언어의 많은 기능들이 빈 값이 들어갔을 때 연산의 대상이 되는 값과 같은 결과를 내거나, 빈값에 대해서는 아무런 작용을 하지 않는 처리를 하는 것이 많다. 따라서 아무런 처리를 하지 않거나 영향이 없는 처리를 할 때 빈 값을 사용할 수 있다.

### 빈값, 항등원, null
- 어떤 변수의 기본값으로 빈값을 할당해야 할까? 항등원을 할당해야 할까? null을 할당해야 할까?

### null의 이점
- null은 다른 타입과 병용해서 비할당을 나타낼 때 사용한다.
- 어떤 연산을 할 때 타입이 다르면 에러가 발생하는 경우가 많다. php7 이후에는 타입을 적극적으로 사용하고 있고, php에서 제공하는 각종 기능들도 특정 타입에만 동작하는 경우가 많다.
- 어떤 타입의 값이 할당되어야 로직의 동작이 가능한데 아직 미할당을 `null`으로 두면 어떤 기능이 동작할 때 잘못된 값이 들어왔다며 에러를 발생 시키므로 로직의 전개에 앞서 할당되지 않은 실수를 예방할 수 있다.

### 빈 값의 이점
- 빈 값은 어떤 연산을 함에 있어서 영향을 끼치지 않는 경우가 많다. 따라서 `null`인 경우 따로 처리를 하는 로직을 적을 필요 없이 그냥 빈 값을 그대로 사용해도 로직에 영향을 주지 않는 방법을 통해서 분기 처리를 줄일 수 있다는 장점이 있다.
- 하지만 일부 로직에서는 빈 값이 로직에 영향을 주는 경우가 있을 수 있는데, 로직을 만들 때 빈 값은 코드에 영향을 주지 않도록 코드를 짜면 해결되는 일이긴 하다. 그러면 빈 값을 연산이나 로직의 항등원으로 이용할 수 있다.
- 기본적으로 각 타입의 빈값을 비할당으로 사용해도 괜찮은데 `null` 타입을 병용하는 것은 빈 값을 두 개 사용하는 것으로 어떤 것을 빈 값으로 사용하는지 모호하게 한다.
- 값이 할당되지 않았을 경우 에러를 발생시킬 수 있는 `null`을 병용하여, 각 타입의 빈값을 사용하지 않고 `null`만을 사용하도록 하자고 할 수도 있지만, 로직의 동작 과정에서 `null`이 아닌 빈 값이 변수에 할당되는 경우가 생길 수도 있고 이 경우 다시 `null`으로 바꾸어 처리해야 하는 문제점이 생긴다. `null`인 경우만 따로 처리하는 로직에 빈 값이 들어가서 미할당에 대한 경우를 따로 처리하는 로직이 동작하지 않을 수 있기 때문이다. 결국에는 `null`과 빈 값 모두를 체크하는 로직이 만들어지게 될 가능성이 있기 때문에 그냥 하나의 타입의 빈 값을 사용할 수 있을 때는 `null` 없이 빈 값만 사용해 주는 편이 좋아 보인다.

### 항등원
- 사실 로직을 빈 값을 항등원으로 이용할 수 있도록 만들어 주면 된다. 값을 로직에 맞추는 것이 아니라, 로직의 항등원을 빈 값이 되도록 로직을 만드는 편이 좋다.
- 예를 들어 수수료를 계산하는 로직에서 기본값을 `1`로 하고 수수료가 5%일 때 변수에 `1.05` 할당하고 곱하는 방식의 로직이 아닌, 계산할 때 `1+0`, 수수료가 5%일 때는 `1+0.05`와 같이 변수에 0이 기본값으로 할당하고 변수에 `0.05`를 넣는 방식의 코딩 스타일로 바꿀 수 있을 것이다.
- 일부 순수 함수형 코딩을 사용할 때는 항등원을 나타내는 값이 정해져 있고 어느 연산을 하더라도 항등원값은 항등원이기 때문에 기본값으로 사용하기 좋다. 하지만 순수 함수형 코딩이 좋지 않은 언어의 경우 일관된 항등원 값이 없기 때문에 빈 값을 항등원으로 쓸 수 있는 로직을 만드는 편이 좋다.

### null을 병용할 것인가?
- 빈 값을 우선적으로 사용하도록 한다.
- 빈 값을 로직에 사용할 때 항등원으로 작용하도록 로직을 구성하자. 곧 빈 값을 로직에 사용할 때는 연산을 거처도 아무런 처리도 일어나지 않은 결과와 동일한 결과가 나오게 로직을 구성하도록 하자.
- 만약 빈 값을 로직에 사용할 때 아무런 처리를 하지 않은 결과와 동일한 결과를 갖지 못하는 경우에는 `null`을 병용하는 방법을 생각해 볼 수 있을 것이다.
