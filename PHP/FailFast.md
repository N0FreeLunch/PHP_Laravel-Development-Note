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
