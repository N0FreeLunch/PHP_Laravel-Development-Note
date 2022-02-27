# php에서 세미콜론을 강제하는 이유
- 가장 큰 문제는 세미콜론을 명시적으로 처리하지 않고 생략하게 되면 파서가 처리하는 코드와 유저가 생각하는 코드의 불일치가 생길 가능성이 있다.
- 대부분의 일반적인 사용에서는 문제가 없을 수 있지만 세미콜론을 생략함으로 인해서 예측 불가능한 잘못된 코드 실행이 일어날 수 있다.
- 언어의 문법이 확장되면서 세미콜론을 생략하는 것이 문제가 될 여지가 있기 때문에 JS의 표준에서는 세미콜론을 생략하지 않는 코딩 스타일을 추천한다고 한다. PHP도 이런 문제점을 인식하고 일부로 복잡한 세미콜론 생략 기능을 만들지 않을 것으로 보인다.
- JS에서 세미콜론 생략 기능을 구현할 때 파서의 복잡성이 증가한다는 이야기가 많이 있었다. 파싱 엔진을 만들 때 여러 문법 각각에 명령의 단위가 끊어지는지 끊어지지 않는지 체크하는 로직을 넣는 것 보다는 일괄적으로 세미콜론 단위로 끊을 수 있다면 확실히 속도면에서 유리하고 엔진의 파싱과정을 만드는 것이 간단할 수 있다는 장점이 있기 때문에 세미콜론을 강제하는 쪽으로 언어의 방향을 정한 것으로 보인다.

## 자바스크립트에서 세미콜론을 생략할 때 발생하는 문제
#### 사용자가 작성한 코드
```
function getData() {
    return
        {
            title: 'Maintainable Javascript',
            author: 'Nicholas C. Zakas'
        }
}
```

#### 파서가 처리하는 코드
```
function getData() {
    return;
        {
            title: 'Maintainable Javascript',
            author: 'Nicholas C. Zakas'
        };
}
```
- Reference : '읽기 좋은 자바스크립트 코딩 기법'라는 책
- 위와 같은 파서가 처리하는 코드와 유저가 생각하는 코드의 불일치 문제점 때문에 세미콜론을 명시적으로 사용하는 것을 자바스크립트에서는 권장한다.

## 언어의 확장에 따라 세미콜론 생략이 버그를 유발할 가능성이 있다는 주장
> As new syntactic features are added to ECMAScript, additional automatic semicolon insertion hazard's may be introduced. Automatic tooling such as linters or formatters are recommended to warn against or fix common ASI hazards and enforce a consistent usage style.
- Reference : https://github.com/tc39/ecma262/pull/1062#issuecomment-357036281
> As new syntactic features are added to ECMAScript, additional automatic semicolon insertion hazard's may be introduced. As a result, the number of ASI may increase, and heavy reliance upon ASI may become more complicated.
- Reference : https://github.com/tc39/ecma262/pull/1062#issuecomment-357039126
- JS 쪽에서도 표준을 세미콜론을 쓰는 것으로 할지 쓰지 않는 것으로 할지 여러가지 논의가 있는 것으로 보이지만, 세미콜론을 사용하지 않았을 때 문제 발생의 여지가 있을 수 있다는 점은 타당하다고 생각하기 때문에 가능한 세미콜론을 사용하는 편이 좋지 않을까 생각이 된다.

## 자바스크립트의 세미콜론 표기의 자유도
- 자바스크립트의 세미콜론은 명령 단위의 한 줄 표현을 가능하게 하지만, 여러 줄이 되는 경우 자동으로 명령 단위가 끊기게 된다. 세미콜론을 사용한 한 줄 표현은 자바스크립트 코드 압축화를 위해 유용하지만 세미콜론으로 코드를 늘리는 것은 코드 압축화를 위해 쓰이지 않는 측면과 문법 작성에 새미 콜론을 강제하게 되면 불편함을 초래하기 때문에 사용하지 않도록 컨셉으로 만들어진 특성으로 보인다. 자바스크립트에서 세미콜론을 자동으로 넣어주는 기능을 ASL(automatic semicolon insertion) 이라고 한다.
- php도 마찬가지로 세미콜론을 사용함으로써 한 줄에 여러 단위의 명령을 작성하는 것이 가능하다는 것이 장점이다. 물론 가독성을 위해서 그렇게 코드를 작성하지는 않는다.

## 명령 단위의 종료를 구조적으로 나타내는 언어와의 차이
- 파이썬, 고 언어와 같은 언어는 개행을 통해서 명령 단위의 종료를 한다.
- 하지만 자바스크립트, php와 같은 언어는 세미콜론을 통해서 코드의 표현을 다양하게 사용할 수 있다. 한줄에 코드를 압축하는 방식, 여러 줄에 걸쳐 구분을 해서 작성하는 방식 등등
- 물론 이러한 방식이 코드 가독성에 좋은 요소는 아니기 때문에 PSR 등을 통해서 표준적인 코딩 스타일을 권장하고 있다. 하지만 표준 코딩 스타일을 벗어나야 할 때가 가끔 생기기도 한다.

## 세미콜론의 역할
- 세미콜론의 역할은 명령어 종료이다.
- https://www.php.net/manual/en/language.basic-syntax.instruction-separation.php

## 명령과 종결의 
### php 태그를 열고 닫지 않는 경우
```
<?php echo 'Ending tag excluded';
```
- 위 코드는 동작한다. 세미콜론은 명령의 종결이기 때문에 뒤에 php 코드가 더 있을 수 있음을 암시한다.
- 하지만 파일이 끝났기 때문에 더이상 php 코드가 있을 것을 염두하지 않을 수 있다.
```
<div>
<?php echo 'Ending tag excluded';
</div>
```
- 위의 코드는 에러구문이다. html과 섞어 쓸 때는 ?>을 반드시 적어 줘야 한다. 
- 왜냐하면 세미콜론은 뒤에 php 코드가 더 있음을 염두하는데 php 태그가 닫히지 않았다면 \<\/div\>를 php 코드로 보고 해석을 해 버리기 때문에 에러가 난다.
- HTML 태그와 함께 쓰기 위해서는 ?>으로 php 태그를 닫아 줘야 한다.
- 명령의 종결과 php 태그의 종결을 구분하자.

## 세미콜론을 생략할 수 있는 특별한 경우
### 파일의 마지막 코드
- 하나의 php 파일의 마지막 코드에서는 세미콜론을 생략할 수 있다. 마지막 코드는 어차피 명령을 끝내는 것이라서 세미콜론 없이도 명령 수행이 가능한 것으로 보인다.
- https://stackoverflow.com/questions/29284075/why-is-the-semicolon-optional-in-the-last-statement-in-php/29284131

### PHP 태그를 닫을 때
```
<?php echo "test"; ?>
```
뿐만 아니라
```
<?php echo "test" ?>
```
도 가능하다.
- 이유는 ?>가 명령의 끝을 의미하기 때문에 세미콜론을 생략할 수 있다.

## 세미콜론을 표기 해야 하는 경우
### 일급 시민의 경우에는 세미콜론을 표기해야 한다.
```
class Sample
{
    $nestedObj = new class {
    };
}
```
- 익명 클래스는 세미콜론을 요구한다. 그러나 클래스는 세미콜론을 요구하지 않는다.
- 클래스, 메소드, if 문이나 while 문의 중괄호를 닫을 때는 세미콜론을 요구하지 않는다. (이것은 자바스크립트에서도 마찬가지 ASL이 적용되지 않은 자바스크립트는 세미콜론을 요구한다.)
- 클래스, 메소드, if문 while 문 등은 하나의 독립적인 단위로 쓴다. 반면 익명 클래스의 경우에는 `$nestedObj = new class {};`으로 할당하는 방식 곧 일급 시민으로 사용된다. 일급 시민의 역할을 가진다면 다른 문법들과 함께 사용했을 때 명령의 끝이 어디서 끝나는지 알려 줄 필요가 있기 때문에 종결을 나타내는 세미콜론을 요구한다.

### 문자열 키워드는 종결의 의미라도 세미콜론을 요구
```
if (true):
 echo "<p>Hello World !!!</p>";
endif;
```
- 위의 경우 endif는 명시적으로 if문을 닫는다는 역할을 가지고 있다. 그럼에도 불구하고 꼭 세미콜론을 강조하고 있다.
- 문자열 문법 키워드는 문법적인 종료 역할을 하지 않는다는 관점을 가지고 있어서 endif 다음에 세미콜론을 쓰게 만든 것으로 보인다.

## 세미콜론 사용의 문제
- 문제는 php에 대한 교육을 할 때 언제 세미콜론을 써야하고 언제 세미콜론을 쓰지 말아야 할지 명확하게 알려주지 않는다는 것이다.
- 초보자들의 경우 이 문법을 사용할 때는 세미콜론을 쓰지 않는구나라고 예외를 기억해야 한다. 세미콜론을 제대로 쓰지 않으면 에러가 나는 건 덤 IDE가 잡아 주지 않는다면 초반 학습 코스트가 되는 부분이다.
- 위의 원리를 안다면 조금은 더 쉽게 세미콜론의 사용에 대해 잘 알고 쓸 수 있을 듯

## Reference
- https://bakyeono.net/post/2018-01-19-javascript-use-semicolon-or-not.html
