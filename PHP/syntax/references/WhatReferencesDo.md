## 참조의 종류
- assigning by reference (참조에 의한 할당)
- passing by reference (참조에 의한 전달)
- returning by reference (참조에 의한 반환)

## 참조에 의한 할당
```php
<?php
$a =& $b;
?>
```
> it means that $a and $b point to the same content.
- 변수 `$a`가 변수 `$b`를 가리키고 있다는 것이 아니라 `$b`가 갖고 있거나 참조하는 값의 원본을 가리킨다. 따라서 `$a`나 `$b`는 동일한 원본 값을 갖고 있다.
> Note. $a and $b are completely equal here. $a is not pointing to $b or vice versa. $a and $b are pointing to the same place.
- 위 예시에서 변수 `$a`와 변수 `$b`는 완전히 동일하다. `$a`는 변수 `$b`를 가리지키 않고 그 반대도 마찬가지이다. `$a`와 `$b`는 동일한 장소를 가리킨다. (여기서 장소라는 번역은 공간이라는 의미로 쓰일수도 있고 어떤 영역이란 의미로 해석될 수도 있다. 물리적인 장소만을 뜻하는 것은 아니며 장소라는 것은 변수가 가리키는 주소에 대응하기 위한 표현으로 보인다.)

### 해석
- 변수 `$b`는 어떤 값을 가지고 있다. `& $b`는 변수 `$b`의 공간에 할당되어 있거나 참조하고 있는 참조 원형을 의미한다.
- 변수 `$a`는 변수 $b를 참조한다. 다른 변수를 참조한다는 것은 다른 변수가 가지고 있는 변수를 참조하는 것이 아니라 해당 변수가 가지고 있거나 참조하는 값의 원본을 가리킨다.

### undefined 변수의 참조
```php
<?php
function foo(&$var) { }

foo($a); // $a is "created" and assigned to null

$b = array();
foo($b['b']);
var_dump(array_key_exists('b', $b)); // bool(true)

$c = new StdClass;
foo($c->d);
var_dump(property_exists($c, 'd')); // bool(true)
```
> If you assign, pass, or return an undefined variable by reference, it will get created.
- `undefined` 변수를 참조로 할당, 전달, 리턴할 때 기본값으로 `null`이 할당된다. 

#### 해석
- `foo($a)`에서 `$a`는 위 코드에서 정의되지 않았으므로 `undefined`이다. 정의되지 않은 변수를 함수의 인자로 넘겨주었을 때 함수의 내부에서는 넘겨 받은 값을 `null`으로 취급한다. 이 때 함수의 파라메터가 참조일 경우에는 null을 내부로 전달하며, 함수의 파라메터가 참조가 아닐 때는 null을 전달하지만 `Warning: Undefined variable $a`메시지를 띄운다.
- 기본적으로 참조란 어떤 값을 가리키고 있어야 하는데 `undefined`의 경우 가리키는 값이 없는 상태이다. 가리키는 값이 없다면 default로 가리키는 값을 설정해 주어야 되므로 `undefined` 값이 들어올 경우 기본적으로 `null`을 가리키게 만든다. 또한 함수는 주어진 인자를 반드시 받아야 한다. `undefined`는 인자가 존재하지 않는 상태이므로 기본적으로 `null`을 세팅하지만 원래는 정의되지 않는 값을 받아서는 안 되기 때문에 이에 대한 시정을 요구하는 warning 메시지가 나온다.

#### 함수 리턴값에 대한 참조 할당
```php
<?php
$foo =& find_var($bar);
?>
```


## Reference
- https://www.php.net/manual/en/language.references.whatdo.php

