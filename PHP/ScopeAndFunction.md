## 스코프란?
- 스코프란 변수가 동작하는 유효 범위를 의미한다.

### php에서 스코프
- php에서 스코프는 함수 내부를 스코프로 한다.
- `function () { }`에서 중괄호 안이 스코프의 범위이다.
```php
function () {
    echo "no variable";
    $variable = "value";
    echo "variable has $variable";
}
```
- 위의 코드에서 `$variable`이란 변수의 스코프는 변수가 초기화 된 위치 `$variable = "value";` 부터 함수가 끝나는 `}`까지이다.
- 코드의 실행에 따라 변수가 살아 있는 범위를 스코프라고 한다. 하지만 일반적으로 어떤 변수를 선언할 수 있고, 선언된 변수가 사라지는 코드 실행 지점까지인 함수의 괄호 `{}`를 스코프라고 부른다.

### 상위 스코프의 변수 차단
- 많은 언어에서 함수의 스코프는 상위 스코프의 변수를 차단한다.
```php
$outerVariable = 'outer variable';
$writeOuterVariable = function () {
    echo "out of scope variable : ".$outerVariable;
};
$writeOuterVariable();
```
- 위의 코드에서 함수 내에서는 함수 스코프 바깥의 변수에 접근을 할 수 없다.
- 하지만 자바스크립트의 경우 상위 스코프의 변수를 하위 스코프에서 그대로 이용할 수 있다.
```js
const outerVariable = 'outer variable';
const writeOuterVariable = function () {
    console.log("out of scope scope variable : " + outerVariable);
};
writeOuterVariable();
```

### 상위 스코프의 변수 사용하기
- php에서 스코프 밖의 변수를 사용하기 위해서는 `use`을 사용한다.
```php
$outerVariable = 'outer variable';
$writeOuterVariable = function () use ($outerVariable) {
    echo "out of scope variable : ".$outerVariable;
};
$writeOuterVariable();
```
- `use`로 함수 스코프 밖의 변수 중에서 함수 스코프 안에서 사용할 변수를 지정하면 함수 내에서 사용할 수 있다.
- php에서 상위 스코프의 변수를 함수에서 사용하기 위해서는 **익명함수**로 만들어 줘야한다. 기명함수는 `use` 키워드를 사용할 수 없기 때문에 상위 스코프의 변수를 사용할 수 없다.

### 익명함수와 기명함수
- php에는 익명 함수와 기명함수가 있다. 익명 함수는 `function() {}`의 형태를 갖고 있으며, 기명함수는 `function name() {}`의 형태를 갖고 있다.
- 익명함수는 즉시 실행 함수의 형태 `(function() {})();`나 변수에 함수를 할당하는 형태 `$var = function () {};`로 사용한다.
- 익명함수의 함수는 php에서 값의 형태로 변수에 할당 가능하며 변수가 전달될 수 있는 모든 곳에 전달 가능한 일급 시민이다. php에서 일급 시민에 해당하는 값 뒤에는 `;` 문법의 종결 의미인 세미콜론을 붙여준다. 그에 반해서 기명함수의 경우에는 변수에 값으로 할당될 수 없는 특성을 가지며 변수에 할당하지 않으므로 변수를 사용한 호출인 `$`를 사용한 변수명으로 호출하지 않고 함수에 지어준 이름 그대로 사용한다. 이는 php에서 변수에 할당할 수 있는 값인 일급시민이 아닌 특수한 문법으로 뒤에 세미콜론을 붙이지 않는다. `function name() {}`는 맞지만 `function name() {};`는 맞지 않다.
- 기명함수는 외부 스코프의 변수를 사용할 수 없으며 `use` 키워드도 사용할 수 없다. 기명함수에서 사용할 수 있는 외부 스코프의 변수는 전역으로 사용되는 값이다. `const`로 선언된 상수 또는 글로벌 스코프에서 선언된 변수만 기명 함수 내부에서 사용할 수 있으며, 어떠한 지역변수도 기명함수 스코프 밖에 있다면 사용할 수 없다.

#### 기명함수
```php
$glovalVariable = 'hello';

function namedFunction() {
	$localVariable = 'world';
	
	function namedNestedFunction() {
		global $glovalVariable, $localVariable;
		echo "$glovalVariable $localVariable";
	};
	
	namedNestedFunction();
}

namedFunction();
```
- 글로벌 변수에는 `'hello'`라는 값의 변수가 있고, 지역 변수에는 `'world'`라는 값의 변수가 있다. 함수 안에 내포된 기명함수를 호출했을 때, 전역 변수의 값 `'hello'`는 출력 되는 반면, 지역 변수의 값 `'world'`의 값은 전역변수의 값이 아니기 때문에 가져올 수 없어서 `null`이 되어 출력되지 않는다.

#### 기명함수와 순수함수
```php
function factorial($n) {
    if ($n <= 1) {
        return 1;
    } else {
        return $n * factorial($n - 1);
    }
}

var_dump(factorial(5));

$factorial = function ($n) use (&$factorial) {
    if ($n <= 1) {
        return 1;
    } else {
        return $n * $factorial($n - 1);
    }
};

var_dump($factorial(5));
```
- 기명 함수는 함수 내에서 자기 이름의 함수를 호출하기 쉽기 때문에 재귀 코드를 좀 더 간결하게 짜는 기능을 제공한다. 익명함수를 할당한 변수명을 함수 내부로 전달하려면 참조 형식으로 전달해 줘야 하는데 이러한 문법을 사용하는 것은 직접 코드의 사용 방식을 견식하지 않는 이상 떠올리기 어렵다.
- 또한 `use` 키워드를 사용하려면 함수를 기명함수에서 익명함수로 바꿔야 하므로 외부 변수에 영향을 받지 않아야 한다는 것을 어필할 수 있는 문법적 구분을 제공할 수 있다.
- OOP나 오토로딩을 통한 네임스페이스를 적극 사용하고 있다면 전역변수를 거의 사용하지 않을 것이고 이러한 상황에서 글로벌 변수만 받을 수 있는 기명함수는 재귀코드의 사용도 쉽기 때문에 순수함수의 표현으로 사용될 수 있다. 심지어 클래스나 객체의 멤버로 접근하지 못하기 때문에 외부의 값으로 오염되기 어려운 문법적 특징을 갖고 있다. 따라서 기명 함수를 사용하여 순수 함수라는 것을 표현해 보는 것도 좋아보인다.
```php
class PureFunctionTest
{
    private string $hello = 'hello';

    public function method()
    {
        function namedFunction() {
            return $this->hello;
        }
        return namedFunction();
    }
}

echo (new PureFunctionTest)->method();
```
- 위 코드에서 기명 함수 내에서 `$this->hello`으로 객체의 멤버에 접근하는 코드는 에러를 발생시킨다.

### 함수와 스코프
- 자바스크립트의 변수는 `const`나 `let`으로 선언되면, 블록 스코프를 갖는다. 함수인지 관계 없이 중괄호 `{}`가 변수의 스코프가 된다. `var`나 선언 키워드 없이 사용된 변수의 경우 `function` 함수의 스코프를 갖는다. 그래서 조건문이나 반복문의 중괄호 안에 사용된 변수도 스코프를 갖는다.
- 자바스크립트와 달리, 그리고 대부분의 언어에서는 블록 스코프를 사용하지 않고, 함수 스코프를 사용한다. 조건문이나 반복문 안의 중괄호가 변수의 스코프가 되지 않는 것은, php의 변수가 함수 스코프를 갖기 때문이다.
```php
if(true) {
    $innerIf = 'hello';
}
echo $innerIf;
```
- 블록 내에서 선언한 변수가 소멸되지 않고 블록 밖에서도 소멸하지 않았는데, 이는 함수 스코프내에서 선언된 변수가 아니기 때문에 전역 변수로 선언된 것이라서 그렇다.

### 로컬 선언 키워드 없음
- php는 로컬 스코프에서 변수를 선언하는 키워드는 존재하지 않는다.
```php
(function () {
    const hello = 'hello';
    readonly string $world = 'world';
    echo hello + $world;
})();
```
- 글로벌 변수나 클래스의 멤버로 변수를 선언할 때는 `const`를 사용할 수 있고, 클래스의 멤버로 변수를 선언할 때는 재할당 불가 키워드인 `readonly`와 타입을 지정하는 키워드인 `string`을 사용할 수 있다.
- 그러나 로컬 스코프에서는 이러한 로컬 변수에 선언할 수 있는 키워드가 없다. php 언어 스펙을 결정하는 사람들은 로컬 스코프에서의 선언 키워드 도입에 부정적인데, 클래스의 멤버 변수로 변수 선언 키워드를 사용할 수 있기 때문에 로컬 스코프에서의 선언 키워드를 대체할 수 있는 상황에서 이러한 문법의 도입이 php 파서의 복잡성을 늘리는 것에 비해 이점이 크지 않다는 의견이다.
- 하지만 로컬 스코프에서 이러한 변수를 제한하는 키워드가 없다는 것은 코드가 길어지면 길어질수록 변수가 재선언 된다던지, 다른 타입의 값이 할당된다던지 하는 상황이 자주 일어날 수 있고, 변수의 변화 과정을 추적하기 어려워지는 등의 문제를 마주할 수 있다.
- 심지어 자바스크립트의 경우, 함수 또는 블록의 바깥의 모든 상위 스코프의 변수를 내부에서 사용할 수 있기 때문에 재할당 방지 키워드가 없으면 어느 순간 로컬 스코프의 변수 조작으로 상위 스코프의 값이 바뀔 가능성이 높다. 자바스크립트의 경우는 재할당 방지 키워드가 필수이다.
- php의 경우 함수 스코프에 상위 스코프의 변수를 사용하기 위해서는 `use` 키워드로 가져와야 하기 때문에 상위 스코프의 변수를 함부로 바꾸게 되는 경우가 드물다. 그리고 `use` 키워드로 가져온 값은 객체가 아니라면 일부러 참조 문법을 사용하지 않는 한 복사가 되므로 의도적으로 참조를 사용하거나 객체를 변경하는 작업을 하지 않는다면 상위 스코프의 변수가 함부로 변경되지 않는다.
```php
$message = ['hello'];
$function = function () use ($message) {
    array_push($message, 'world');
};

$function();

var_dump($message);
```
- 위의 예제에서 배열을 `use` 키워드로 함수 내부로 가져와서 변경을 했지만, 함수 외부에서 출력되는 결과값은 `['hello']`으로 `'world'`가 원소로 추가되지 않은 것을 알 수 있다. 이는 `use` 키워드로 함수 내부로 값을 가져올 때 복사가 되었기 때문에 함수 내부에서는 변경 되었지만, 함수 외부에서는 변경되지 않은 것이다.
- php는 자바스크립트에 비해 상위 스코프의 변수의 오염의 염려가 적고 통제 가능하기 때문에 특별한 문제가 없을 수는 있지만, 로컬 스코프 변수 선언 키워드가 없기 때문에 한 스코프 내의 코드가 길수록 변수를 통제하기 어려운 특성이 있다. 따라서 변수에 특별한 제약을 하지 않더라도 충분히 통제할 수 있는 단위의 코드를 작성할 수 있도록 함수의 기능을 작은 단위로 쪼개어 사용해야 한다.

### 작은 단위의 함수
- php는 로컬 스코프에서 변수를 제한하는 기능을 제공하지 않기 때문에 한 스코프에 많은 코드를 넣게 되면 변수의 상태를 추적하기 어려워진다. 따라서 php에서 권장하는 코딩 스타일은 하나의 스코프에 너무 많은 변수를 사용하지 말고 함수나 메소드를 사용해서 별도의 스코프로 분리될 수 있는 대상은 별도의 스코프에 넣어서 한 스코프에 존재하는 변수의 수를 줄이는 코딩이 필요하다.

### 화살표 함수
- php에서 화살표 함수는 자바스크립트 처럼 상위 스코프의 변수를 자유롭게 받을 수 있는 특징이 있다.
```php
$hello = 'hello';
echo (fn($var) => "$hello $var")('world');
```
- 화살표 함수는 `return`을 하기 전에 미리 처리하는 기능 없이 `=>`이후의 코드를 `return`하는 함수이며 스코프를 나타내는 중괄호 `{}`가 없는 코드이다.
- 함수의 스코프가 없는 대신에 화살표 함수를 사용하고 있는 스코프의 변수를 그대로 사용할 수 있다.
- 단, 스코프가 존재하는 대상이 있는데, 화살표 함수의 매개변수는 스코프가 존재하며 화살표 함수가 반환된 이후 화살표 함수의 매개변수는 수명을 다한다. 물론 클로저가 반환되면 매개변수는 좀 더 오래 생존할 수 있다.