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

(new PureFunctionTest)->method();
```
- 위 코드에서 기명 함수 내에서 `$this->hello`으로 객체의 멤버에 접근하는 코드는 에러를 발생시킨다.

### 함수와 스코프

