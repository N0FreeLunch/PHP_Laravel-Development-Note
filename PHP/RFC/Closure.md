## PHP의 특징
- PHP의 함수는 다른 언어의 스코프와 다르다. PHP에서의 함수는 스코프 밖의 대상을 감지할 수 없다.
- 외부 스코프의 대상을 호출할 수 없는 이유는 PHP의 기존 구조적인 문제 때문이다.

## 람다 함수 도입을 찬성한 이유
- '재사용할 일 없는 한 번만 쓰고 버리는 함수'(throw-away functions)를 정의할 때 굉장히 유용하여 프로그래밍을 더 편리하게 하기 때문

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


## Reference
- https://wiki.php.net/rfc/closures
