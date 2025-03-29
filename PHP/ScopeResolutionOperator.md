# 범위 결정 연산자

> The Scope Resolution Operator (also called Paamayim Nekudotayim) or in simpler terms, the double colon, is a token that allows access to a constant, static property, or static method of a class or one of its parents. Moreover, static properties or methods can be overriden via late static binding.

범위 결정 연산자는 php에서 `::` 구문을 사용한다. 범위 결정 연산자로 엑세스 가능한 것은 특정 클래스 또는 부모 클래스에 대해 (const 키워드로 클래스의 멤버를 정의하는) 상수, (클래스의 멤버를 static 키워드로 정의하는) 정적 멤버 프로퍼티 및 정적 메소드, 늦은 정적 바인딩을 통해 오버라이딩 된 정적 프로퍼티 및 메소드에 접근 가능하다.

코드르 정의하고 있는 클래스의 정적 멤버에 엑세스 하고 싶은 경우 `self`, 코드를 정의하고 있는 클래스를 상속한 클래스 엑세스 하고 싶은 경우 `static`, 코드를 정의하고 있는 클래스의 부모 클래스에 엑세스 하고 싶은 경우 `parent`를 사용해야 한다.

## 클래스에 엑세스 하는 다양한 방법

### 클래스명을 직접 사용

```php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

var_dump(MyClass::CONST_VALUE);
```

클래스명을 문자열 또는 변수에 담지 않고, 직접 사용하는 방법으로 다른 언어에서 흔히 볼 수 있는 엑세스 방식이다.

### 문자열로 클래스에 엑세스 하기

```php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

var_dump('MyClass'::CONST_VALUE);
```

클래스명의 문자열에 `::`을 붙여 정적 멤버에 엑세스 할 수 있다. 다른 언어에도 클래스명을 문자열로 접근할 수 있지만, php 처럼 간단하게 엑세스 하거나, php 처럼 클래스의 정적 멤버에 엑세스 하는 형태가 아니라 리플렉션 기능에 클래스를 지정하기 위해 사용하는 경우가 많다.

### 문자열을 변수에 담에 엑세스 하기

```php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

$classname = 'MyClass';
var_dump($classname::CONST_VALUE);

var_dump(MyClass::CONST_VALUE);
```

클래스명의 문자열을 변수에 담아서 `::`을 붙여 정적 멤버에 엑세스 할 수 있다.

## References
- https://www.php.net/manual/en/language.oop5.paamayim-nekudotayim.php
