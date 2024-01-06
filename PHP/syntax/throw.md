## throw

### statement와 expression
- 프로그래밍 언어에서 expression은 다른 값과 함께 사용되어 하나의 값으로 사용될 수 있는 문법을 의미한다. 함수, 연산기호는 값과 함께 사용되어 값을 결과값으로 가지며 변수도 값을 나타내기 때문에 표현식(expression)이다.
- 이에 반해, 프로그래밍에서 어떤 값을 나타내지 않는 대상인, 조건문, 반복문, 선언문은 결과값을 가지지 않기 때문에 구문(statement)에 해당한다.

### 함수는 statement와 expression 둘 다 될 수 있다.
- 함수가 값을 반환하는 경우 expression에 해당한다. 하지만 함수가 값을 반환하지 않는 경우 statement에 해당한다.
- 코드 단위의 최종적인 결과가 값이 되느냐 되지 않느냐가 statement와 expression를 구분한다.

### `statement throw`에서 `expression throw`
- php8 이전에 `throw`는 statement에 해당했다. 다음 [RFC](https://wiki.php.net/rfc/throw_expression)가 통과되고 나서 expression으로 바뀌었다.
- 이는 `throw` 구문을 php 언어에서 값이 들어갈 수 있는 자리에 넣을 수 있도록 한다는 의미를 갖고 있다.

### expression의 throw
- throw가 expression이 된다는 것은, `throw` 구문이 실제 값이 된다는 의미가 아니라, 값을 넣을 수 있는 부분에 `throw` 구문을 넣을 수 있도록 하겠다는 의미이다.
- expression은 그 자체 또는 요구되는 추가적인 조건으로 값으로 평가되지만, `throw`는 값이 되지는 않는 예외적인 케이스이지만, php 언어에서 값을 넣을 수 있는 부분에 사용될 수 있도록 하겠다는 의미이다.

### throw의 동작
```php
var_dump(throw new Exception());
```
- 위의 코드에서 `var_dump`는 던진 값의 결과를 얻을 수 없다. 왜냐하면, `throw`로 예외가 던져진 순간 `var_dump`의 실행 영역에서 벗어나 던져진 예외를 포착할 수 있는 대상을 php는 탐색을 하고, 포착할 수 있는 `catch` 구문이나 프레임워크의 예외 처리 기능 또는 php의 전역 예외 처리 기능을 탐색하고 없다면 에러를 보고하고 프로세스를 종료하기 때문에 `var_dump`가 결과 값을 취득할 수 없게 되기 때문이다.

### 예외 처리 구문의 이해
- `new Exception()`은 객체를 나타내는 표현이다.
- `throw`는 현재의 로직의 실행의 흐름을 버리고 던진 것을 포착할 수 있는 구문으로 코드의 실행 위치를 이동하겠다는 의미를 갖는다.
- `catch`는 던져진 예외를 포착하여 예외를 `catch` 문 안에서 다루겠다는 의미를 갖고 있다.
- `finally`는 던져진 예외를 포착하지는 않지만 `catch`에 의해 포착이 되었다면 `catch`의 처리 다음에 실행하고, `catch` 문이 없는 경우에는 `finally` 부분에서 코드를 실행하는 지점으로 하겠다는 의미를 갖는다.
