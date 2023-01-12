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
var_dump($foo);
?>
```
- 함수의 리턴값을 참조로 사용하기 위해서 함수는 반드시 값을 반환해야 한다. 값을 반환하지 않는 함수의 리턴 유형이 `void`인 경우에는 `$foo`값이 `null`이 되겠지만 `undefined`를 받아서 디폴트 값인 `null`을 세팅했기 때문에 `undefined`값을 참조로 할당하지 않도록 시정을 요구하는 메시지가 나온다.
- `Warning: Undefined variable` `Notice: Only variables should be assigned by reference`라는 에러 메시지가 나타난다.

### 함수 내에서 글로벌 변수 참조
- 기본적으로 함수는 클로저를 설정하지 않는 한 함수 내부에서 정의한 변수를 함수 밖으로 내 보낼 수 없다. 함수 바깥 스코프에서 정의한 값을 함수 내부에서 사용하여 바깥 스코프에서 정의한 변수의 값을 바꾸는 것은 가능하다.
- 만약 함수 내부에서 함수 바깥 스코프에서 정의된 변수에 참조를 사용하여 값을 할당한다면 어떻게 될까? 만약 어떤 변수가 함수 내부에서 정의한 값을 참조하고 있다면 함수가 종료되더라도 해당 참조값을 유지할 수 있어야 한다.
- 그런데 함수의 라이프 사이클은 함수가 종료되면서 끝난다. 클로저를 사용한 경우를 제외하고는 함수 내부에서 선언한 모든 값과 변수들을 외부로 가져갈 수 없다는 것이 함수의 기본 개념이다.
- 함수의 라이프 사이클이 종료 되어도 참조 값을 유지하기 위해서는 함수 내부에서 정의한 값이 함수 외부로 클로저 없이 전달되어야 한다. 함수가 종료됨과 동시에 가비지 컬렉터를 처리하지 않고 함수 내부의 선언한 값을 보존할 것인지 보존하지 않을 것인지 결정하는 로직을 위해서 함수 단위의 처리가 아닌 함수 내에서 참조가 이뤄진 경우 변수 단위로 가비지 컬렉터 처리를 하여 참조이므로 가비지 컬렉터에 의해 제거되지 않는다는 로직을 사용해야한다.
- php는 인터프리터 언어이다. 어떤 변수를 읽을 때 변수의 스코프가 아직 끝나지 않았다면 해당 변수는 사용할 가능성이 있는 변수이기 때문에 가비지 컬렉터에 의해 삭제되지 않는다. 언젠가 참조될 가능성이 있는 값으로 남아 있기 때문에 메모리에 계속 남아있게 되고 이는 메모리릭을 유도할 가능성이 있다.
- 따라서 함수 내에서 정의된 값을 참조로 하는 경우 함수 내부에서 참조되는 값을 외부 스코프로 가지고 나갈 수 없게 만들어 준 것으로 보인다.
```php
<?php
$var1 = "Example variable";
$var2 = "";

function global_references($use_globals)
{
    global $var1, $var2;
    if (!$use_globals) {
        $var2 =& $var1; // visible only inside the function
        echo $var2.PHP_EOL; // 'Example variable'
        echo $GLOBALS["var2"].PHP_EOL; // ''
    } else {
        $GLOBALS["var2"] =& $var1; // visible also in global context
        echo $GLOBALS["var2"].PHP_EOL; // 'Example variable'
    }
}

global_references(false);
echo "var2 is set to '$var2'\n"; // var2 is set to ''
global_references(true);
echo "var2 is set to '$var2'\n"; // var2 is set to 'Example variable'
?>
```
- global 키워드의 경우 글로벌 스코프에 정의된 변수를 함수 내에서 사용하게 해 주는 키워드이다. 참조기호(`&`)가 없어도 `global $var1, $var2`으로 가져온 값은 참조로 대상을 가리키고 있다.
- `$var2`는 함수 내부에서 `$var1`의 값으로 변경되었으나 글로벌 스코프의 `$var2`의 값은 변하지 않았다는 것을 `echo $GLOBALS["var2"].PHP_EOL;` 부분을 통해서 알 수 있다. 곧 `global` 키워드를 통해서 함수 외부의 변수를 가져왔다고 하더라도 참조 할당자 `=&`를 사용한 경우에는 지역변수로 사용된다는 것을 알 수 있다.
- 그에 반해 `$GLOBALS["var2"]`에 참조 할당을 한 경우에는 글로벌 스코프의 `$var2`의 값도 변경된다는 것을 알 수 있다.
- 함수 내에서 전역 변수(외부 스코프를 가진)를 사용할 때 `$GLOBALS["변수명"]`을 사용하는 것 보다는 `global $변수명`을 사용하는 것이 안전하게 참조를 해제하기 때문에 메모리 릭을 줄일 수 있는 방법이다. 함수의 라이프 사이클이 종료되고 함수 내부에서 사용한 참조를 깨끗하게 정리하기 위해 `$GLOBALS["변수명"]` 보다는 `global $변수명`을 사용하는 것을 권장하며, 함수의 내부에서 전역변수의 참조를 꼭 바꿔야 하는 일이 있는 경우에만 `$GLOBALS["변수명"]`을 사용하는 코딩을 하는 편이 좋아 보인다.

### foreach와 참조
```php
<?php
$ref = 0;
$row =& $ref;
foreach (array(1, 2, 3) as $row) {
    // do something
}
echo $ref; // 3 - last element of the iterated array
?>
```
- `foreach (이터레이터값 as $원소)` 문법에서 이터레이터의 원소에 해당하는 변수가 처음 만들어질 때 참조로 만들어진 경우이다.
- `foreach (array(1, 2, 3) as $row)`에서 `$row`변수가 처음 선언된 것이 아니라 `$row =& $ref;`에서 변수가 이미 선언되었다.
- 변수가 선언될 시점에 그냥 변수가 아니라 참조로 변수를 선언하였기 때문에 해당 변수에 다른 값을 할당하지 않는다면 계속 이 변수는 참조변수로 사용된다.
- `foreach (이터레이터값 as $원소)`에서 `$원소`에는 값을 할당하는 `=` 연산자가 사용되지 않았다. `as $원소`의 표현은 값을 할당하는 것은 아니며 참조로 변경한다.
- foreach문이 순회를 할 때 각 원소의 값을 `$원소`에 집어 넣으며 집어 넣는 공간이 `$ref = 0`의 값 0이 있는 공간이다. 이 공간의 값을 계속 변경시킨다. 따라서 `echo $ref;`의 값도 3이 된다.
- 이 때 `$ref = 0;`에서 `0`이란 값이 있는 공간은 바뀌지 않는다. 예를 들어 이 공간을 `int`가 아닌 `string`으로 선언하면 `foreach (array(1, 2, 3) as $row)`에서 문자열 공간을 가리키는 `$row`에 수가 할당되므로 에러가 발생한다. 
```php
class Test
{
	public string $ref = 0;
	
	public function __construct()
	{
		$row =& $this->ref;
		foreach (array(1, 2, 3) as $row) {
    	// do something
		}
		echo $ref;
	}
}

new Test
```
- `public string $ref = 0;` 부분을 `public int $ref = 0;`로 바꾸면 애러가 발생하지 않는다.

## Reference
- https://www.php.net/manual/en/language.references.whatdo.php
