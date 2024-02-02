## 조기 실패

### 동적 프로퍼티 생성 방지
- php9에서는 더 이상 동적 프로퍼티의 값에 접근을 할 수 없게 된다. 동적 프로퍼티란 객체의 정의되지 않은 멤버에 접근할 수 없도록 하는 것이다.
```php
class DynamicProperty {}
$instance = (new DynamicProperty);
$instance->property = 1;
var_dump($instance->property);
```
- 위 코드를 실행하면 일단 실행이된다. `var_dump`가 보여주는 결과 값은 `int(1)`이다. 하지만 다음과 같은 `Deprecated` 메시지를 나타낸다.
```
Deprecated: Creation of dynamic property DynamicProperty::$property is deprecated in /home/user/scripts/code.php on line 3
```
- 객체에 정의되지 않은 멤버를 생성하는 `$instance->property = 1;`와 같은 코드를 php9 부터는 더 이상 사용하지 못하게 하겠다는 내용이다.
- 이는 php 파서의 주요 공헌자 중의 하나인 Nikita에 의해 제안 되었는데 그 내용은 다음과 같다.
> The motivation for this change is twofold: To prevent mistakes (due to typos or renames) in the common case, and to make intentional uses explicit. The core problem is that reading from a non-existing property issues a diagnostic that makes the issue immediately apparent, while writing to a non-existing property is entirely silent. PHP gives no indication whatsoever that the programmer has made a mistake.
<sup>[RFC: deprecate_dynamic_properties](https://wiki.php.net/rfc/deprecate_dynamic_properties)</sup>
- 내용은 동적 프로퍼티를 할당하는 것은 오타가 생겨서 사용하던 멤버에 접근을 하는 것인지 새로운 멤버를 만드는 것인지에 대한 어떠한 징후도 제공하지 않기 때문에 이러한 실수를 만들 수 있는 동적 프로퍼티 생성기능을 사용했을 때 에러로 하여, 프로그래머에게 정의되지 않은 멤버를 사용했어라고 알려 주는 방식으로 바꾸겠다는 의미이다.

### 조기 실패(fail fast)
- 정적 언어는 컴파일시에 어떤 문제가 있는 컴파일 과정을 통해서 알려 준다. 그에 반해 동적인 언어는 직접 코드를 실행하면서 코드에 문제가 있다면 코드의 실행이 실패하고 에러를 발생시킨다.
- 어떤 문제의 소지가 있는 코드에 대한 에러를 발생시켜서 해당 코드를 사용하는 프로그래머가 코드를 실행하면서 무엇을 잘못 사용했는지 알게 하는 피드백을 제공하는 과정이 '조기 실패'라는 개념이다.

#### 동적 프로퍼티
- php9 부터는 동적 프로퍼티에 접근했을 때 에러를 발생시킨다. 이를 통해서 동적 프로퍼티를 생성하고 접근하려는 시도를 했을 때 정의되지 않은 속성이므로 에러를 발생시킨다.
- [RFC: undefined_property_error_promotion](https://wiki.php.net/rfc/undefined_property_error_promotion), 아마도 php9에서는 `throwable` 인터페이스의 구현체인 에러를 발생시키는 방식으로 구현될 것으로 예상된다.

### 정의되지 않은 변수의 에러 승격
- [RFC: Undefined Variable Error Promotion](https://wiki.php.net/rfc/undefined_variable_error_promotion)에서는 정의되지 않은 변수를 사용할 경우 에러가 발생하도록 오류로 승격한다는 내용이 있다. 위 RFC의 몇 가지 예제를 살펴 보자. 예제를 좀 더 이하하기 쉽게 변경 해 보았다.
```php
$user = new class {
	  public bool $admin = false;
};


if ($user->admin) { 
    $restricted = false;
}

if ($restricted) { 
    echo 'You do not have permission to be here';
}

echo 'end';
```
- 위 코드는 if 문이 2개 있다. 위의 `$restricted`라는 변수가 정의되기 위해서는 위쪽의 if 문이 실행 되어야 `$restricted` 변수의 값이 정의가 되고, 위쪽의 if문이 실행되지 않으면 `$restricted` 변수의 값은 정의되지 않는다. 이때 아랫쪽의 if문에서 정의된 변수를 사용할 때도 있고, 정의되지 않은 변수를 사용할 때도 있는데, php8에서는 Warning 에러를 내기만 하고 if문의 조건을 정의되지 않은 변수로 할당했을 때는 해당 구문을 무시하고 계속 코드를 실행시킨다. 따라서 위의 코드를 실행하면 `end`가 찍힌다. php9에서는 에러가 발생하므로 정의되지 않은 변수의 값을 접근하려는 시도를 하는 코드가 실행되면 멈추고 에러를 던지며 뒤의 코드는 더이상 실행되지 않는다.
- 구문을 스킵하고 다음 코드를 실행하는 것은 뒤의 코드가 동작하기 위한 조건이나 환경을 설정하는 것일 수 있기 때문에 스킵하는 것은 프로그래머가 의도하지 않은 동작을 일으킬 가능성을 높인다. 따라서 정의되지 않은 코드를 사용했을 때는 프로그램을 멈추고 이를 보고하도록 해야 한다. php에서는 Warning을 에러로 보고되도록 설정할 수 있는데 이를 사용하면 php8에서도 이러한 동작을 할 수 있다.
- 조건문에 따라 변수가 정의될 수도 있고, 정의되지 않을 수도 있는데, `if ($restricted)` 부분에서 변수 `$restricted`를 사용할 수 없도록 `$restricted`를 정의할 때의 조건을 함께 추가하여 `if ($user->admin && $restricted)`와 같은 코드를 사용한다면, `$restricted`가 정의되어 있을 때만 값에 접근하기 때문에 정상적인 동작이 된다. 이 때 `if (isset($restricted))`과 같은 방식은 추천하지 않는데 그 이유는 변수가 정의된 맥락을 보여주지 않기 때문이다. `if ($user->admin && $restricted)`는 유저가 어드민일 경우에만 권한이 제한되는지 확인하세요라는 의미를 갖고 있다. 이를 통해서 어드민 권한의 유저에게 뭔가 제한을 가한다는 의미를 프로그래머가 이해할 수 있지만, `if (isset($restricted))`는 정의되어 있지 않으니까 실행하지 않아야 하는구나의 의미만을 가지므로 문맥적인 의미를 주지 않는다.
- RFC에서는 위와 같은 코드의 사용 방식을 다음과 같이 변수를 정의하는 방식으로 사용할 것을 권장하고 있다.
```php
$user = new class {
	  public bool $admin = false;
};

$restricted = true;
if ($user->admin) { 
    $restricted = false;
}

if ($restricted) { 
    die('You do not have permission to be here');
}
```
- `$restricted = true;`의 코드가 추가 되었는데 유저가 어드민이 아닐 경우에는 항상 제한을 걸겠다는 의미를 가진다. 어드민일 경우에만 제한을 가하지 않도록 설정한다는 것이고 이러한 변수 선언 방식을 통해서 정의되지 않은 값에 접근을 방지할 수 있는 코딩 스타일을 제안하고 있다.
