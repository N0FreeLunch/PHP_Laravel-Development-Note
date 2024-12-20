# 에러의 원인을 발견할 수 있는 로직 만들기

## 흔히 하는 실수

초보 개발자들이 흔히 하는 실수는 에러를 만들지 말아야 한다는 관념에 사로잡힌 것이다. 하지만 의도하지 않았던 로직이 발생했을 때는 에러가 나도록 만들어야 좀 더 안전하게 프로그램을 개선시켜 나갈 수 있다. 의도하지 않은 로직에 대해서 에러가 일어나지 않도록 짜는 것은 잘못되거나 의도하지 않은 로직에 대한 피드백을 받지 못하므로 로직이 잘못되었는지 파악하기 어려울 수 있다.

에러가 발생하면 스텍 트레이스에 의해서 에러가 발생한 부분을 실행하기 위해서 호출된 모든 함수들을 기록한 결과를 보여주므로 개발자는 어떤 로직으로 실행되었을 때 에러가 발생했는지 손쉽게 확인할 수 있다.

## 왜 에러가 나도록 프로그래밍 해야 하는가?

에러가 발생하는 것은 의도하지 않은 로직의 전개가 일어나는 것을 의미한다. 의도하지 않은 로직이 발생했을 경우 가능한 에러를 보고하도록 하여 어떤 결과를 초래할지 모르는 로직을 미연에 차단해야 한다.

항상 의도하지 않은 로직, 설계되지 않은 동작이 일어나지 않도록 목적 의식을 가지고 코드를 만들며, 명확한 설계가 되지 않았는데 겉보기에 잘 작동하는 양상은 회의적인 태도로 코드를 살펴 보자.

## 에러가 나는 프로그래밍 보다 더 중요한 것

가능한 한 의도하지 않은 로직의 일어날 가능성을 막고 혹여라도 의도하지 않은 로직이 일어났다면 에러가 날 가능성을 남겨두는 것이 필요하다.

## 어떻게 에러를 나게 할 것인가?

1. 타입을 사용하여 데이터의 형식을 제한한다.
2. 변수 이름을 명확하게 지어준다.
3. 변수의 타입을 명확하게 체크한다.
4. 타입힌트를 사용하여 코드 작성한다.
5. 함수를 사용하여 코드를 작성한다.
6. 객체를 사용하여 코드를 작성한다.
7. 때로는 데이터가 정의 되었는지 정의되지 않았는지 체크하는 로직을 만들어야 할 때와 만들지 말아야 할 때
8. 의도한 로직 이외의 동작을 할 때는 에러가 날 수 있도록 만든다.

### 타입 사용하기

어떤 타입을 사용할지 명확하게 정의한다. 타입을 정의하는 것은 다른 형식의 데이터가 들어갈 경우 에러가 발생하게 되므로 잘못된 데이터가 들어갈 수 있는 여지를 어느정도 방지한다.

클래스의 멤버 변수, 함수의 파라메터, 함수의 리턴 값에는 타입힌트를 통해서 타입을 지정할 수 있다. 하지만 이외의 변수에는 php는 타입을 지정할 수 없다. 또한 변수에 값을 재할당 할 수 있다. 

불변의 변수 타입을 정할 수는 없지만, IDE에 의해서 변수의 타입이 분석되도록 만들 수 있다. phpdoc을 사용하는 방식, 즉시 실행 함수의 리턴 타입을 이용하는 방식, `assert` 함수를 이용하는 방식이 있으며, 일반적으로 사용의 우선도는 assert 함수 이용 방식 > phpdoc을 이용하는 방식 > 즉시 실행 함수의 리턴값을 이용하는 방식 순이다.

#### phpdoc으로 타입 지정

```php
/**
 * @var ?string $var
 */
 $var = 'value';
```

IDE의 타입추론을 이용할 수 있지만, 코드가 실행될 때 타입 불일치를 잡아주지 않기 때문에, 개발 환경에서의 타입이 일치하지 않을 때 에러를 감지할 수 있는 assert를 추천한다.

#### 즉시 실행 함수의 리턴값 이용하기

```php
$var = (fn(): ?string => 'value')();
```

IDE의 타입추론도 이용할 수 있으며, 런타임에서 타입이 불일치하면 에러를 발생시킨다는 장점이 있다. 하지만 이 방식은 사용 예제를 찾아보기 힘들다. 특별한 추상화 없이 함수의 반환 값을 타입을 제공하기 위해 사용한다는 것은 다른 언어에서 찾아보기 어려운 방식이고, 런타임에 타입 체크를 추가하므로 속도 저하의 문제나 코드가 지저분해 질 수 있는 등의 단점이 있다.

#### assert 함수를 사용하기

```php
$var = 'value';
assert(is_string($var) || is_null($var));
```

php에서 IDE의 타입추론을 적극적으로 활용하기 위해서는 assert를 사용하자. assert는 개발환경에서의 에러를 잡아주기 때문에 의도하지 않은 조건의 동작이 일어날 때 assert 함수의 에러로 문제를 쉽게 찾을 수 있으며, 프로덕션에서 assert는 동작하지 않기 때문에 런타임의 리소스 소모의 걱정도 없다. 또한 다른 많은 언어에서 활용되는 코드이기 때문에 일반적인 코딩 스타일에 적용해도 좋다.

### 변수 이름을 명확하게 지어주기

#### 변수의 역할에 맞는 이름을 지어주기

변수가 하는 역할과 다른 역할을 하는 이름을 지어주지 않도록 하자. 또한 실제 변수가 하는 역할과 미묘하게 다른 역할을 갖는 이름을 피하도록 하자. 변수에 담기는 값에 의미상의 변화가 있다면 변수의 이름도 함께 수정하는 것이 올바르다. 변수에 어떤 값이 들어 갔는지 변수의 이름을 통해서 확인을 해야 하고, 변수에 들어간 값이 어떤 값인지 추축하기 위해 코드를 추적하는 일이 발생하지 않도록 적절한 이름을 지어주는 것이 중요하다.

#### 변수가 가질 수 있는 값의 범위를 제한하는 이름 사용하기

변수가 가질 수 있는 범위를 예측할 수 있게 만들어 주는 것이 중요하다.

예를 들어 `$date`라고 했을 때 어떤 종류의 포멧인지 모를 수 있다. 그럴 경우 `$yyyymmdd`와 같은 방식으로 적어주면 값의 포멧이 어떤지 알 수 있으므로 더 좋은 변수명이 될 수 있다. `$date`라고 했을 때는 포멧에 관계없이 다양한 날짜값을 받을 수 있는 경우라고 할 수 있다. 예를 들어

```php
use Carbon\Exceptions\InvalidFormatException;

class Sample
{
    private Carbon $date;

    /**
     * @throws InvalidFormatException
     */
    public function setDate(string $date): self
    {
        $this->date = Carbon::parse($date);
        return $this;
    }
}

(new Sample)->setDate('2024-12-21');
(new Sample)->setDate('12212024'); // Failed to parse time string (12212024) at position 7 (4): Unexpected character'
```

```php
class Sample
{
    private string $date;

    public function setDate(string $yyyymmdd): self
    {
        $year = intval(substr($yyyymmdd, 0, 4));
        $month = intval(substr($yyyymmdd, 4, 2));
        $day = intval(substr($yyyymmdd, 6, 2));
        if (checkdate($month, $day, $year)) {
             $this->date = $yyyymmdd;
        } else {
             throw new Exception ('invalid date format');
        }
        return $this;
    }
}

(new Sample())->setDate('20241221');
(new Sample())->setDate('2024-12-21'); // Fatal error: Uncaught Exception: invalid date format
```

### 변수 타입을 명확하게 체크하기

코드에 어떤 타입인지 명확하게 체크하는 로직을 만든다. `empty`, `!$var`와 같이 여러 타입을 동시에 확인하는 로직은 꼭 필요할 때만 사용하고 가능하면 `is_null`, `is_int`, `is_float`... 등 어떤 타입이 사용되는지 명확하게 알 수 있는 타입 체크 기능을 이용하자. 코드에서 타입이 확인될 수 있기 때문에 잘못된 타입이 들어가서 의도하지 않은 연산을 하게 될 여지를 줄인다. 이를 통해서 다른 부분의 코드를 살피지 않고 직접 디버깅을 하지 않더라도 어떤 타입을 갖는지 명확하게 알 수 있도록 한다. 코드를 짤 때 어떤 타입의 값이 할당되는지 명확하게 알 수 있기 때문에 로직의 추가 변경이 수월해진다.

변수가 사용될 때 복수의 타입의 변수가 함께 처리되는 로직이 만들어지는 것을 피한다. 타입이 많으면 분기처리가 생기게 되고, 그 만큼 코드 해석의 복잡도가 추가된다. php에서는 가능한 변수에 하나의 타입만 사용하거나 하나이 타입과 null을 허용하는 방식을 사용한다. `is_int`, `is_string`, `is_bool` ...을 사용하면 미할당을 null으로 할당을 하나의 타입으로 사용한다는 의미가 되고, `$var !== 0`, `$var !== ''`, `$var !== []`는 초기값을 빈값으로 할당을 빈값이 아닌 값으로 쓴다는 의미로 해석할 수 있다.

## 타입힌트를 사용하여 코드 작성하기

php7.0 부터는 int, float, string, bool 등의 스칼라 타입힌트 및 함수의 리턴 타입힌트가 도입되었다.

php7.2 부터는 함수에 타입힌트를 지정할 수 있다. 함수의 파라메터와 함수의 리턴값에 타입힌트를 지정할 수 있다.

php7.3 부터는 `?type` 형식으로 nullable 문법을 사용할 수 있다.

php7.4 부터는 객체의 멤버 변수에 타입을 지정할 수 있게 되었다.

php8 부터는 다중 타입 `int|string`과 같은 유니온 타입을 사용할 수 있다.

유니온 타입을 사용할 수 있다고 해서 유니온 타입을 남발하는 것은 좋은 코딩 스타일이 아니다. 가능한 하나의 타입, 하나의 타입과 null 타입을 사용하도록 하고 특별히 여러 타입을 사용해야 할 필요가 있을 때만 사용하자.

타입을 사용한다는 것은 값의 범위를 줄일 수 있다는 장점이 있다. 따라서 잘못된 범위의 값이 들어 올 때 특정 타입 범위에 해당하는 값만 받아들이므로 예상하지 로직이 실행될 경우 타입이 달라진다면 에러가 발생하기 때문에 에러를 확인하고 오류를 개선할 수 있다. 또한 IDE에 의한 추론이 이뤄져 변수의 값을 확인하기 수월해진다.

## 함수를 사용하여 코드 작성하기

### 타입힌트 사용하기

php에서 멤버 변수가 아닌 일반 변수에는 타입을 지정할 수 없지만, 함수의 매개변수와 리턴 값에 타입힌트를 지정할 수 있다.

```php
$add = function(int $arg1, int $arg2): int {
    return $arg1 + $arg2;
};

var_dump($add(1,2));
```

함수에 전달된 인자의 타입이 다를 경우 에러가 발생하기 때문에 의도하지 않은 타입이 전달되고 반환되는 오류를 줄여준다. 함수에 전달하는 값과 함수의 리턴값의 타입이 정해져 있기 때문에 함수를 사용한 연산을 구성할 때 함수의 리턴 값의 유형을 정확하게 알게되므로 로직의 사이 사이에 존재하는 함수를 통해서 타입이 잘못 변경되었을 때의 오류를 줄여 준다.

### 스코프

함수를 사용하면, 코드를 쓰는 공간이 다양한 변수가 널려 있는 문제를 방지한다. 방 안에 물건이 이리저리 쌓여 있는 것 보다는 서랍이나 박스 안에 분류되어 있는 것이 좋은 것과 같이, 함수를 이용해서 개념 단위로 코드의 종류를 분류할 수 있다. 

함수 내부에서 사용하는 변수는 유효 범위를 갖는다. 매개변수 또는 `use` 키워드로 변수를 캡쳐할 때 `&`와 함께 또는 객체라서 참조로 전달되는 값이 아니라면, 함수 내의 변수는 함수가 끝나면 함수 외부에서 사용할 수 없다. 함수 내부의 로직은 참조로 전달되는 변수가 아닌 고려하지 않아도 되기 때문에 한 개념 단위의 코드에만 사용될 변수라면 함수를 이용해서 스코프를 분리하는 것을 통해서 로직의 전개 과정에서 고려되어야 할 변수의 가짓수를 줄일 수 있다. 또한 함수 내에서는 사용되는 변수의 변경이 함수 범위 내부에 한정되기 때문에 더욱 정확한 로직을 만들 수 있어 실수의 여지를 줄여준다.

### 의도치 않은 로직이 안 만들어지도록 코드를 작성하는 방법

함수의 명칭을 가능한한 어떤 기능을 하는 것인지 명확하게 정하는 편이 좋다. 함수 명과 매개변수의 이름과 타입을 보고 함수가 어떤 동작을 하는지 예측할 수 있는 코드를 만드는 것이 좋다. php8 부터는 명명된 인자를 사용할 수 있기 때문에 적절한 매개변수의 이름이 명명된 인자로 적혀 있다면 코드의 가독성에 상당한 도움이 된다.

함수가 어떤 기능을 담당하는지 쉽게 알기 위해서는 함수에 매개변수를 잔뜩 넣지 않는 방식을 사용해야 한다. 매개 변수 및 캡쳐로 전달되는 변수들의 조합으로 함수의 값은 결정되는데 이런 조건들의 상호작용이 많을 수록 함수의 동작을 추측할 수 없게 되는 문제가 발생한다. 결과 값이 함수 외부의 값에 영향을 받는 것이 아닌 함수 내부에서 고정된 것이라면 이러한 값을 함수의 매개 변수나 use 키워드를 통한 캡쳐로 전달 받지 않도록 한다.

함수의 인자가 많아질 경우 함수 보다는 객체를 이용하는 편이 좋다. php의 경우 함수형 프로그래밍의 커링 기능을 이용하여 매개 변수를 분할해서 받는 코딩 스타일이 좋지않다. 매개변수를 분할해서 받기 위해서는 객체의 메소드를 통해서 로직의 실행에 필요한 조건을 세팅을 하고 로직을 실행하는 메소드를 만들도록 하자.

함수에 전달되는 값에 객체나 배열을 전달하는 코딩 스타일은 가능하면 피하는 것이 좋은데, 이는 객체나 배열의 어떤 값이 함수의 동작에 영향을 주는지 알기 어렵기 때문이다. 꼭 필요한 인자만을 받도록 함수를 정의하는 것이 좋은 스타일이다.

## 객체를 사용하여 코드 작성하기

### 객체 멤버 타입힌트

php7.4 부터는 객체의 멥버 변수를 선언할 때 타입을 지정할 수 있기 때문에 의도치 않은 다른 타입의 값이 멤버 변수에 할당되면 에러가 발생한다. 이를 통해서 잘못된 값이 들어갈 여지를 방지할 수 있다.

이미 짜여진 코드에 타입이 없다면 다른 유형의 타입이 들어가 있는지 멤버변수에 사용하는 모든 코드를 전수조사 해야 하는 문제가 생긴다. 타입보다 좀 더 정확한 범위를 지정하기 위해서 멤버 변수명도 타입에 맞고 의도에 맞는 것으로 지어주면 어떤 값이 들어가는지 멤버 변수에 할당되는 코드들을 확인하지 않아도 되는 장점이 있다.

### 객체타입

언어에서 제공하는 기본 타입이외에 OOP언어는 클래스를 타입으로 하여 객체의 타입을 확인할 수 있다. 객체 타입을 지정하는 것은 해당 객체가 가진 논리의 제한에 따라 값을 만들어간다는 의미이다. 객체 타입을 사용하는 것은 값의 범위와 양상을 통제하겠다는 의미를 가지고 있다. 하지만 객체 타입을 사용한다는 것은 코드를 만드는 비용을 추가한다. 객체화 하는 것이 여러모로 이득이라고 판단될 때 사용하는 것이 좋으며 모든 로직을 다 객체화 한다는 생각은 좋지 않다.

객체를 통해서 값의 범위와 양상을 통제하기 위해서는 메소드를 사용해야 한다. 메소드를 통해서 값의 변화를 통제하고, 값의 범위를 통제하는 방식의 코드를 짜야 하는 것이다. 메소드를 통해서 입력된 값이 적절한지 평가하고 메소드를 통해저 객체 내부에 저장된 값의 적절성을 평가하여 결과 값을 얻는 방식으로 구성한다. 만약 적절하지 못한 값이 객체에 주입되었다면 에러가 발생하는 코드도 만드는 필요가 있다.

### 의미론적 추상화

객체화를 하는 것은 의미론적인 로직을 짜겠다는 것이다. 따라서 객체 외부에서 사용할 메소드를 정하는 것, 객체 내부에서 사용할 메소드를 정하는 것, 메소드 간의 체이닝 관계를 구성하고 메소드의 사용순서를 잘 알 수 있는 로직을 만드는 등은 문법적인 이해만으로 정의하고 구성하고 사용할 수 있는 것이 아니다. 객체의 사용법을 의미론적으로 이해할 수 있게 만드는 것이 중요하다. 객체에 의미론적인 관념과 사상을 담을 때 해당 객체를 사용자가 이해할 수 있고, 이해한다면 그 객체의 내부 구성을 어떻게 수정할 수 있고 확장할 수 있고 사용할 수 있는지 알게 된다.

객체의 사용법을 이해한다는 것은 객체가 만들어진 관념과 사상을 이해하는 것이다. 객체에 담긴 의미론적인 구성을 모른다면 객체를 잘못 사용하게 되고 객체를 잘못 사용했을 때 에러가 발생하는 코드를 만드는 것이 잘못된 로직의 실행을 막는 방법이 된다.

### 객체를 잘못 사용했을 때 에러가 나는 코드 만들기

메소드에는 타입힌트를 정할 수 있고, 타입힌트를 통해서 함수의 인풋과 아웃풋의 범위를 제한할 수 있다. 메소드들을 사용하여 로직을 만들 때 함수의 타입힌트에 따라 잘못된 값이 사용되었을 경우 에러를 발생시킬 수 있다.

메소드는 들어온 값과 나가는 값의 범위를 조절할 수 있는 로직을 내부에 정의할 수 있다. 잘못된 범위의 값이 들어왔을 때 커스텀 에러나 예외를 만들어 값이 의도한 대로 전개되지 않은 경우를 포착하는 제한을 넣을 수 있다.

## 데이터가 정의 되었는지 정의되지 않았는지 체크하는 로직을 만들어야 할 때와 만들지 말아야 할 때
### null의 역할
- null 체크는 일반적으로 데이터가 할당 되었는지 할당되지 않았는지 파악할 때 사용한다.
- 데이터가 할당되지 않았다는 의미로 null을 사용하는 것이 일반적이며, null을 하나의 값으로 취급하지 않도록 코딩에 주의해야 한다. null은 데이터의 할당이 일어나지 않은 것이므로 로직을 구성할 때 데이터 요구사항이 충족되어 있는지 확인하는 용도록 쓰이는 것이다.
- 연산 로직에서는 미리 연산을 할 수 있게 데이터가 할당되어 있어야 하기 때문에 null이 들어간 데이터가 들어가면 안 된다. 연산 로직에 이르기 전에 미리 연산에 필요한 데이터의 할당을 체크하는 로직을 거쳐야 한다.

### 데이터가 정의 되었는지 체크하지 않는 로직을 만들어야 할 때
- 만약 반드시 실행되어야 할 연산로직에 선행되는 데이터가 할당되지 않은 경우에는 에러가 발생하도록 로직을 짜야 한다. 그래야 왜 데이터가 할당되지 않은 원인을 찾을 수 있기 때문이다. 하지만 할당되지 않은 경우에는 데이터에 접근이 안 되거나 null과 다른 타입간의 연산이 일어나면서 대부분 에러가 나게 되므로 데이터 할당이 되었는지 체크하는 로직을 미리 짜지 않는 편이 생산성을 높일 수 있다.
- 하지만 같은 타입에서 특정 범위로 값이 할당 되어야 연산되는 로직의 경우 타입간의 다름으로 연산 에러가 발생하지 않기 때문에 연산 로직에 적절한 값이 제대로 할당되지 않는 경우 수동으로 에러를 발생시키는 방법을 사용하는 편이 좋다.

### 데이터가 정의 되지 않았는지 체크하는 로직을 만들어야 할 때
- null을 체크할 때 `is_null`을 통해서 데이터가 할당되었는지 체크를 한다. `isset` `empty`를 사용하기 보다는 `is_null`을 사용한다. `isset`의 경우 각 타입의 뭔가 없는 느낌의 수치가 할당되었는지를 보는 것이다. null, 0, false, "" 등이다. 하지만 `is_null`과 달리 `isset`의 경우 타입의 빈값을 모두 포함하기 때문에 연산에 문제가 없는 경우가 많다. 따라서 이 값이 반드시 할당되지 않았다고 보장할 수 없는 것이다. 그래서 할당 되었는지 할당되지 않았는지를 체크할 때는 `is_null`을 사용한다. 그리고 `empty`의 경우는 해당 변수가 존재하는지 곧 undefined가 아닌지, 만약 존재하는 변수라면 bool으로 변환했을 때 true로 평가되는지를 확인한다. 따라서 이 또한 null, 0, false, "" 모두를 포함하기 때문에 변수가 할당되었는지 아닌지 판단하는 함수로 적절하지 못하다. null이 아닌 타입에서의 빈 값은 연산의 대상이 될 수 있는 값이다. 연산 로직을 짜기 위한 수단으로 `empty`도 적절한 선택이 될 수 없는 이유이다.
- 어차피 할당되지 않은 값에 대한 연산을 할 경우 에러가 발생하기 때문에 대부분의 상황에서 데이터가 할당 되었는지를 체크할 필요가 없다. 하지만 데이터의 할당여부를 체크해야 할 때는 데이터가 할당되었을 때의 연산 로직과 데이터가 할당되지 않았을 때의 연산 로직이 다른 경우이다. 각각의 경우에 대한 로직처리가 다를 경우 데이터의 할당 여부를 체크한 후 각각의 상황에 따른 로직을 정의해 준다.

### nullable
- 타 언어에는 var과 val 이라는 변수 선언 키워드가 있다. val은 null을 허용하지 않는 변수이고, var는 null을 허용하는 변수이다. null을 허용하게 될 경우 연산과정에서 null 때문에 에러가 발생한다. 그래서 연산이 에러 없이 이뤄져야 하는 보증이 필요한 경우에 val 변수를 사용하여 null 체크의 부담을 덜 수 있다. 하지만 val를 무분별하게 사용해서는 안 된다. val을 사용할 경우 실제 할당되지 않은 경우 기본값으로 각 타입의 빈 값을 사용하게 되면서 실제 값이 할당이 되었는지 되지 않았는지 알 수 없게 되는 경우가 있다. 각 타입의 기본값을 사용하는 것으로 연산에 아무런 문제가 없다면 괜찮지만 할당 여부를 꼭 확인해야 하는 로직이라면 val가 아닌 var를 사용하도록 한다.
- php7.3 부터는 nullable 타입을 지정한다. `?타입힌트` 방식으로 사용하며 null과 지정한 타입을 할당할 수 있다는 의미이다. 다른 언어의 var의 역할을 하고 ?를 사용하지 않는 경우는 val의 역할을 하게 되면서 val과 var라는 키워드를 굳이 만들 필요가 없다. 물론 타입힌트를 선언할 수 있는 부분이 멤버 변수의 선언과 함수의 매개변수와 반환값에 제한적으로 사용할 수 있는 단점이 있긴하다.
- nullable 타입을 통해서 변수의 타입을 지정하면서도 값의 할당 여부를 체크할 수 있는 장점이 있다.

## 의도한 로직 이외의 동작을 할 때는 에러가 날 수 있도록 만든다.
- 의도한 로직 이외의 동작에서 에러가 나게 하려면 의도한 값과 다른 범위의 값, 의도한 값과 다른 타입의 값이 할당 될 경우 에러를 발생시키도록 한다.
- 하지만 의도한 값의 타입과 같고 나올 수 있는 범위가 같은 경우 논리 에러의 문제는 피할 수 없다. 이런 논리 에러를 방지하기 위해서는 사이드이펙트가 없는 기능을 만들어서 작은 기능 단위로 만들어 조합하는 방식으로 만드는 방식으로 로직이 명확하게 드러나게 만드는 것이 좋다. 또한 하나의 기능은 가능한 단순하게 만들어 어떤 역할을 담당하는지를 명확하게 하고 인풋과 아웃풋을 예상가능하고 통제가능한 방식으로 만들어야 한다.
- 같은 타입에서 범위를 제한하는 방식은 커스텀으로 작성해야 하는 로직이 많아지기 때문에 생산성이 줄어든다. 따라서 가능한 타입 의존적인 방식을 사용하고 의미적으로 명확한 코드를 만들어 잘못된 범위의 값이 들어가지 않도록 신경쓸 필요가 있다. 곧 버그를 최소화 할 수 있는 코드를 만드는 것이 요구된다.
