## PHP의 특징
- PHP의 함수는 다른 언어의 스코프와 다르다. PHP에서의 함수는 스코프 밖의 대상을 감지할 수 없다.
- 외부 스코프의 대상을 호출할 수 없는 이유는 PHP의 기존 구조적인 문제 때문이다.
- 이런 PHP 함수의 특징 때문에 PHP에서는 함수 외부 스코프의 값을 보존할 수 있는 클로저 클래스라는 개념을 만들었다.

## 익명함수와 클로저
- 익명함수는 말 그대로 이름이 붙여지지 않은 함수이다.
- 일반적인 프로그래밍 언어에서 클로저는 함수가 선언된 범위 외부의 값 및 참조의 대상을 캡쳐하여 함수 내에 보존하는 것이다. 그러나 PHP에서는 함수 외부의 값 및 참조의 대상을 캡쳐할 수 있는 함수를 클로저라고 한다. 차이는 함수 내에서 보존된 대상을 클로저로 부를 것이냐, 함수 내에 외부 대상을 보존할 수 있다면 클로저라고 부를 것이냐의 차이이다.
- PHP에서 클로저는 클래스를 기반으로 만들었기 때문에 php의 모든 함수는 클로저 클래스의 구현이다.

## 람다함수와 클로저

## 람다 함수 도입을 찬성한 이유
- '재사용할 일 없는 한 번만 쓰고 버리는 함수'(throw-away functions)를 정의할 때 굉장히 유용하여 프로그래밍을 더 편리하게 하기 때문

### 람다 함수가 없을 때의 불편한 코딩의 예
```
function replace_spaces ($text) {
    if (!function_exists ('replace_spaces_helper')) {
        function replace_spaces_helper ($matches) {
            return str_replace ($matches[1], ' ', '&nbsp;').' ';
        }
    }
    return preg_replace_callback ('/( +) /', 'replace_spaces_helper', $text);
}
```
- preg_replace_callback의 외부에서 replace_spaces_helper라는 함수를 정의 한 후 사용한다.
- 만약 replace_spaces_helper라는 함수가 위 코드 외부에 어디선가 정의되어 있다면 함수명이 중복되므로 에러가 발생하므로 없는지 정의한 명칭의 함수명이 있는지 없는지 체크를 해야 한다.
- create_function 이라는 문자열로 php 코드를 인자로 넣어서 함수를 만드는 현재는 DEPRECATED 되어 있는 함수를 사용하여 람다함수 대신에 사용할 수 있는 방법이 있지만, 문자열 안에 코드를 넣어야 해서 구문 강조가 되지 않고, 런타임에 함수를 컴파일 하므로 opcode caches가 적용되지 않는 문제점이 있다.


## 클로저의 존재 이유
### 클로저의 존재이유 1
- create_function()라는 문자열로 eval 형식으로 함수를 만드는 방식을 대신하여 사용하기 위해서이다. 

#### create_function() 메소드의 한계
- create_function() 메소드는 인자로 문자열로 코드를 정의하는 방식으로 사용한다.
1. 문자열이 코드화 되기 때문에 사용자의 입력을 처리할 때 사용하기 어렵다.
2. 문자열을 기반으로 코드화를 하기 때문에 다양한 외부 대상을 전달하기 어렵다. (문자열, 정수, 부동 소수점과 같은 대상들만 사용할 수 있다.)

### 클로저의 존재 이유 2
- 전역변수를 사용하는 함수 작성법을 일부 대체하기 위해서이다.

#### 전역 변수 함수의 한계
- 전역 범위를 재사용하지 않는 함수들을 사용해 오염시킨다.
- 한 번만 사용할 대상이라면 어디서 어떻게 사용하는지 알기 힘들다. (IDE의 힘을 빌려야 함)

### 클로저의 존재 이유 3
- 클래스의 멤버 메소드를 콜백으로 전달하는 방법을 사용할 수 있다.

#### 멤버 메소드의 한계
- 한번만 사용할 목적으로 클래스를 만들고 멤버 함수를 클래스까지 만드는 수고를 해야하므로 효율적인 코딩을 할 수 없다.

### 클로저의 존재 이유 4
- array_map과 같은 클로저를 사용하는 것을 사용하지 말고, 수동으로 foreach를 한다.

#### foreach의 한계
- 때로는 foreach 보다 array_map과 같은 함수를 사용하는 것이 더 직관적이고 세련된 방식으로 만족감을 줄 수 있다.


## 렉시컬 변수
```
function getAdder($x) {
    return function ($y) use ($x) {
        // or: lexical $x;
        return $x + $y;
    };
}
```
- getAdder 함수에 의해 리턴된 익명함수는 변수 $x를 클로저로 가지고 있다. (일반적인 프로그래밍 언어에서의 클로저)
- getAdder 함수의 호출이 끝나도 리턴된 익명함수를 사용하는 대상은 변수 $x를 보존하고 있다. 
- getAdder 함수의 라이프사이클은 끝나더라도 리턴된 익명함수가 변수 $x를 보존하고 있기 때문에 PHP에서의 클로저(함수, 클래스)는 getAdder 보다 더 긴 라이프 사이클을 가진다.
- 클로저에 의해 보존된 $x를 '렉시컬(lexical) $x' 라고 부른다.


## 참조와 복사
- 기본적으로 클로저로 가져온 모든 대상은 복사를 통해 가져와서 보존한다. 이는 클로저가 외부 범위의 변수를 수정하는 것을 불가능하게 만든다.
- 하지만, use 선언에서 변수 이름 앞에 &를 추가하면 복사 대신 참조로 대상을 가져온다. 이 경우 클로저에서 보존한 대상을 바꾸면 외부 범위에 영향을 끼친다.

### 참조/복사의 예시
```
$x = 1;
$lambda1 = function () use ($x) {
    $x *= 2;
};
$lambda2 = function () use (&$x) {
    $x *= 3;
};
$lambda1 ();
var_dump ($x); // gives: 1
$lambda2 ();
var_dump ($x); // gives: 3
```

### 클로저에서 복사를 기본적으로 사용해야 하는 이유
- 클로저를 주로 사용하는 것은 고차함수에서 사용하는데, map, filter, reduce 등과 같은 이터레이터 형태의 고차함수에서 참조를 사용하면 로직이 복잡해 질 수 있기 때문에 가능한 복사를 사용하는 편이 좋은방법이다. 

### 클로저에서 참조가 필요한 이유
- 자바스크립트와 같이 부모 범위의 대상을 바꿀 수 있는 기능까지 지원하기 위해서

## OOP와의 상호작용
```
     class Example {
       private $search;
 
       public function __construct ($search) {
         $this->search = $search;
       }
 
       public function setSearch ($search) {
         $this->search = $search;
       }
 
       public function getReplacer ($replacement) {
         return function ($text) use ($replacement) {
           return str_replace ($this->search, $replacement, $text);
         };
       }
     }
 
     $example = new Example ('hello');
     $replacer = $example->getReplacer ('goodbye');
     echo $replacer ('hello world'); // goodbye world
     $example->setSearch ('world');
     echo $replacer ('hello world'); // hello goodbye
```
- `return str_replace ($this->search, $replacement, $text);` 부분에서 $this는 Example 클래스가 인스턴스화 된 대상을 가리킴
- 객체에서 사용된 클로저에서 클로저 내부의 $this는 객체를 가리킨다. 이때 클로저 함수 내부에서 $this는 use 키워드 없이 $this를 사용할 수 있으며, 현 객체에 접근할 수 있다.
- 객체 내에서 선언된 클로저 내부의 $this는 해당 객체의 private/protected 접근 제한자로 선언된 멤버 및 메소드에 접근할 수 있다. $this에 해당하는 객체에 대한 전체 액세스 권한을 갖는다. 이는 중첩된 클로저에도 적용된다.
- 하지만 객체 내부에 선언된 것이 아닌 함수의 인자로 사용되는 클로저의 경우 $this를 가지고 있지 않거나 $this를 가지고 있다고 해도 클로저가 선언된 객체의 $this가 아닌, 인자로 클로저를 사용하는 함수가 선언된 객체의 $this를 가지게 된다. 함수의 인자로 사용되는 클로저의 경우, 함수가 속한 객체 내로 클로저가 전달된다. 그러면 런타임에서 객체의 함수 내부로 클로저가 전달되기 때문에 클로저의 선언은 런타임 단계에서 인자로 클로저를 전달 받은 객체에서 사용된다. 따라서 클로저를 전달 받은 객체를 가리키는 $this가 된다.

## 정적 클로저
- 정적이라는 것은 클래스를 인스턴스화 되지 않은 클래스에서도 사용할 수 있도록 클래스의 맴버를 정의하는 것을 의미한다.
- 클래스 내에서 선언된 클로저의 내부에서 현재 객체를 참조할 필요가 없을 때 정적 클로저를 만들어 사용할 수 있다.
```
     class Example {
       public function doSomething () {
         $x = 4;
         $closure = static function ($y) use ($x) {
           return $x + $y;
         };
         return $closure (6);
       }
     }
```
- 정적 클로저는 오랫동안 참조 클래스가 있을 때 메모리를 절약할 목적으로 전략적인 사용을 할 수 있다.


## __invoke
```
class Example {
  public function __invoke () {
    echo "Hello World!\n";
  }
}
$foo = new Example;
$foo ();
```
- php에서 클로저는 실행할 수 있는 객체를 의미한다. 클로저 객체에 ()를 붙여 주면 클로저 함수가 실행이 된다.
- 이런 클로저 구현의 특징을 사용해 \_\_invoke 메소드를 만들었다.
- 따라서 \_\_invoke 메소드는 클로저와 동일한 방식으로 객체를 함수화 하는 역할을 한다.
- Example 클래스는 함수처럼 사용할 수 있으며 ()를 통해서 \_\_invoke 메소드를 실행한다.
- `$foo = new Example;` 부분을 보면 알 수 있겠지만, `new Example()`로만 사용해서 ()까지가 한 단위인 것으로 보이지만, `new Example`까지 한 단위이고 `(new Example)()`이렇게 실행할 수  있다는 것을 알 수 있다.

## Reference
- https://wiki.php.net/rfc/closures
- https://stackoverflow.com/questions/4912116/closure-vs-anonymous-function-difference
