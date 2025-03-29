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

클래스명의 문자열에 `::`을 붙여 정적 멤버에 엑세스 할 수 있다. 다른 언어에도 클래스명을 문자열로 접근할 수 있지만, php 처럼 간단하게 엑세스 할 수 있는 기능을 제공하지는 않는다. 보통은 php 처럼 클래스의 정적 멤버에 엑세스 하는 형태가 아니라 리플렉션 기능에 클래스를 지정하기 위해 사용하는 경우가 많다.

### 문자열을 변수에 담아 엑세스 하기

```php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

$classname = 'MyClass';
var_dump($classname::CONST_VALUE);

var_dump(MyClass::CONST_VALUE);
```

클래스명의 문자열을 변수에 담아서 `::`을 붙여 정적 멤버에 엑세스 할 수 있다. 다른 언어에서 클래스 이름을 문자열로 사용하는 경우, 클래스를 다루는 함수의 파라메터로 넘겨주는 등의 일반적인 값 사용 방식으로 쓰이는 것에 반해, php는 `::`이라는 독특한 방법으로 접근할 수 있게 한다.

## 정적 멤버에 엑세스 하기

```php
class ParentClass
{
    const SAME_NAME_CONST = 'parent same constant value';
    public static $parentStatic = 'parent static var';

    public static function accessSameNameConstBySelf()
    {
        return self::SAME_NAME_CONST;
    }

    public static function accessSameNameConstByStatic()
    {
        return static::SAME_NAME_CONST;
    }
}

class ChildClass extends ParentClass
{
    const SAME_NAME_CONST = 'child same constant value';

    public static $childStatic = 'child static var';

    public static function doubleColon() {
        $prarentStaticProperty = 'parentStatic';
        echo "1".PHP_EOL;
        var_dump(parent::$parentStatic);
        var_dump(parent::$$prarentStaticProperty);
        var_dump(parent::${$prarentStaticProperty});
        echo "2".PHP_EOL;
        var_dump(self::$parentStatic);
        echo "3".PHP_EOL;
        var_dump(ChildClass::accessSameNameConstBySelf());
        var_dump(ChildClass::accessSameNameConstByStatic());
        echo "4".PHP_EOL;
        var_dump(ParentClass::$parentStatic);
        var_dump(ChildClass::$childStatic);
    }
}

ChildClass::doubleColon();
```

### 1. 정적 멤버 변수 접근시 $요구

php의 정적 멤버 변수에 접근할 때는 $를 붙여 줘야 한다. $를 붙이지 않으면 상수(`const`) 키워드로 선언된 값에 엑세스하는 사양이다.

`parent::$parentStatic`: 부모 클래스의 정적 멤버에 접근할 수 있다.

정적 멤버의 경우, `parent::parentStatic`가 아닌 멤버명에 `$`를 붙여서 접근해야 한다. `parent::$parentStatic` 이런 문법은 마치 변수 안에 담긴 문자열에 해당하는 멤버로 접근하는 느낌을 준다. 하지만, 변수 안에 담긴 문자열 멤버로 접근할 때는 `parent::$$prarentStaticProperty`, `parent::${$prarentStaticProperty}`와 같은 방식을 사용해야 한다.

### 2. `self`로 상위 클래스 멤버 엑세스

`self`라는 키워드는 정의한 클래스의 멤버만 접근할 수 있다는 느낌을 준다. 하지만, 현재 클래스에 접근하려는 정적 대상(멤버, 상수)이 없는 경우, 상위 클래스의 정적 대상(멤버, 상수)에 엑세스한다.

`self::$parentStatic)`는 `ChildClass` 클래스에서 정의되었지만, `ChildClass`의 정적 멤버에서 `$parentStatic`는 존재하지 않기 때문에, 상위 클래스인 `ParentClass` 클래스의 정적 멤버 `$parentStatic`에 접근한다.

### 3. self vs static 엑세스

부모클래스에서 정의된 정적 멤버 엑세스 기능을 자식 클래스에서 사용할 때 self과 static의 차이

`ChildClass::accessSameNameConstBySelf()`: `string(26) "parent same constant value"`라는 결과가 나온다. 부모 클래스에서 메소드에서 `self::SAME_NAME_CONST`를 사용하므로 부모 클래스에서 정의된 정적 대상 `SAME_NAME_CONST`에 접근한다.

`ChildClass::accessSameNameConstByStatic()`: `string(25) "child same constant value"`라는 결과가 나온다. 부모 클래스에서 정의가 되었지만, `static::SAME_NAME_CONST`를 사용하므로 자식 클래스에서 정의된 정적 대상 `SAME_NAME_CONST`에 접근한다.

### 4. 클래스 내부라도 `self`, `static`, `parent`가 아닌 클래스 이름에 직접 엑세스 할 수 있다.

`ParentClass::$parentStatic`: 부모 클래스의 정적 멤버에 접근한다.

`ChildClass::$childStatic`: 자식 클래스의 정적 멤버에 접근한다.

## References

- https://www.php.net/manual/en/language.oop5.paamayim-nekudotayim.php
