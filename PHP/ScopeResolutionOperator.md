# 범위 결정 연산자

> The Scope Resolution Operator (also called Paamayim Nekudotayim) or in simpler terms, the double colon, is a token that allows access to a constant, static property, or static method of a class or one of its parents. Moreover, static properties or methods can be overriden via late static binding.

범위 결정 연산자는 php에서 `::` 구문을 사용한다. 범위 결정 연산자로 엑세스 가능한 것은 특정 클래스 또는 부모 클래스에 대해 (const 키워드로 클래스의 멤버를 정의하는) 상수, (클래스의 멤버를 static 키워드로 정의하는) 정적 멤버 프로퍼티 및 정적 메소드, 늦은 정적 바인딩을 통해 오버라이딩 된 정적 프로퍼티 및 메소드에 접근 가능하다.

코드르 정의하고 있는 클래스의 정적 멤버에 엑세스 하고 싶은 경우 `self`, 코드를 정의하고 있는 클래스를 상속한 클래스 엑세스 하고 싶은 경우 `static`, 코드를 정의하고 있는 클래스의 부모 클래스에 엑세스 하고 싶은 경우 `parent`를 사용해야 한다.

- 주의할 점 : 기본적으로 정적 대상(상수, 멤버)에 대한 엑세스를 제공하지만, 인스턴스에서 사용될 경우 비정적 메소드에 대한 접근을 할 수 있다. 자바에서 `super.nonStaticMethod()`의 코드를 php에서는 `parent::nonStaticMethod()`으로 접근할 수 있다.

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

### 정적 멤버에 엑세스 하기

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

#### 1. 정적 멤버 변수 접근시 $요구

php의 정적 멤버 변수에 접근할 때는 $를 붙여 줘야 한다. $를 붙이지 않으면 상수(`const`) 키워드로 선언된 값에 엑세스하는 사양이다.

`parent::$parentStatic`: 부모 클래스의 정적 멤버에 접근할 수 있다.

정적 멤버의 경우, `parent::parentStatic`가 아닌 멤버명에 `$`를 붙여서 접근해야 한다. `parent::$parentStatic` 이런 문법은 마치 변수 안에 담긴 문자열에 해당하는 멤버로 접근하는 느낌을 준다. 하지만, 변수 안에 담긴 문자열 멤버로 접근할 때는 `parent::$$prarentStaticProperty`, `parent::${$prarentStaticProperty}`와 같은 방식을 사용해야 한다.

#### 2. `self`로 상위 클래스 멤버 엑세스

`self`라는 키워드는 정의한 클래스의 멤버만 접근할 수 있다는 느낌을 준다. 하지만, 현재 클래스에 접근하려는 정적 대상(멤버, 상수)이 없는 경우, 상위 클래스의 정적 대상(멤버, 상수)에 엑세스한다.

`self::$parentStatic)`는 `ChildClass` 클래스에서 정의되었지만, `ChildClass`의 정적 멤버에서 `$parentStatic`는 존재하지 않기 때문에, 상위 클래스인 `ParentClass` 클래스의 정적 멤버 `$parentStatic`에 접근한다.

#### 3-1. 정적 멤버 엑세스와 정적 메소드 엑세스의 차이

정적 멤버 번수를 호출할 때는 `SomeClass::$someProperty`인 반면, 정적 멤버 메소드를 호출할 때는 `SomeClass::someMethod`로 `$`를 사용하지 않는다.

#### 3-2. self vs static 엑세스

부모클래스에서 정의된 정적 멤버 엑세스 기능을 자식 클래스에서 사용할 때 self과 static의 차이

`ChildClass::accessSameNameConstBySelf()`: `string(26) "parent same constant value"`라는 결과가 나온다. 부모 클래스에서 메소드에서 `self::SAME_NAME_CONST`를 사용하므로 부모 클래스에서 정의된 정적 대상 `SAME_NAME_CONST`에 접근한다.

`ChildClass::accessSameNameConstByStatic()`: `string(25) "child same constant value"`라는 결과가 나온다. 부모 클래스에서 정의가 되었지만, `static::SAME_NAME_CONST`를 사용하므로 자식 클래스에서 정의된 정적 대상 `SAME_NAME_CONST`에 접근한다.

#### 4. 클래스 내부라도 `self`, `static`, `parent`가 아닌 클래스 이름에 직접 엑세스 할 수 있다.

`ParentClass::$parentStatic`: 부모 클래스의 정적 멤버에 접근한다.

`ChildClass::$childStatic`: 자식 클래스의 정적 멤버에 접근한다.

### 부모 클래스의 (비 정적) 메소드에 접근

```php
class ParentClass
{    
    protected function nonStaticMethod()
    {
        echo "call ParentClass's nonStaticMethod()\n";
    }
}

class ChildClass extends ParentClass
{
    public function parentNonStaticMethod()
    {
        parent::nonStaticMethod();
    }

    /**
     * @override
     */
    protected function nonStaticMethod()
    {
        echo "call ChildClass's nonStaticMethod()\n";
    }
}

$class = new ChildClass();
$class->parentNonStaticMethod();
```

`ChildClass`를 인스턴스화 해서 `parentNonStaticMethod` 메소드를 호출하였다. 이 메소드는 자식 클래스에서 정의된 메소드로 부모 클래스의 `nonStaticMethod` 메소드로 엑세스한다.

중요한 점은, `::` 연산자로 정적 멤버가 아닌 비 정적 멤버에 엑세스 할 수 있다는 점이다. `::` 키워드는 정적 대상에만 접근할 수 있어 보이는데, `parent::nonStaticMethod()`의 코드는 부모 클래스의 비정적 멤버에 엑세스한다.

만약에 동일한 이름의 정적 멤버와 비정적 멤버가 존재한다면? 이런 일은 없다. 왜냐하면 기본적으로 php의 메소드는 이름이 중복되면 안되고, 메소드 이름에 단지 `static`을 붙여서 정적과 비정적을 구분해 주었을 뿐이기 때문이다. 정적 멤버든 비정적 멤버든 이름이 중복될 수 없기 때문에, 정적 멤버에 접근하는 `::`과 동일한 방식을 비정적 메소드에 사용하더라도 문법상의 논리적인 문제점은 발생하지 않는다.

### 비 정적 메소드 접근

```php
class ParentClass
{    
    public function accessNonStaticMethodByStatic()
    {
        self::nonStaticMethod();
    }
    
    protected function nonStaticMethod()
    {
        echo "call ParentClass's nonStaticMethod()\n";
    }
}

class ChildClass extends ParentClass
{
    /**
     * @override
     */
    protected function nonStaticMethod()
    {
        echo "call ChildClass's nonStaticMethod()\n";
    }
}

$class = new ChildClass();
$class->accessNonStaticMethodByStatic();
```

자식 클래스를 인스턴스화 해서 부모 클래스에서 정의된 메소드를 호출하였다. `self::nonStaticMethod()`는 부모 클래스의 `nonStaticMethod` 멤버에 접근하라는 의미로 비정적 멤버 메소드이지만 엑세스 할 수 있다.

### 비 정적 멤버 변수 접근 불가

```php
class ParentClass
{    
    public string $nonStaticMember = 'access ParentClass\'s $nonStaticMember';
}

class ChildClass extends ParentClass
{
    public string $nonStaticMember = 'access ChildClass\'s $nonStaticMember';
	
    public function accessNonStaticMemberWithoutDollar()
    {
        parent::nonStaticMember; // Fatal error: Uncaught Error: Undefined constant ParentClass::nonStaticMember
    }
    
    public function accessNonStaticMemberWithDollar()
    {
    	parent::$nonStaticMember; // Fatal error: Uncaught Error: Access to undeclared static property ParentClass::$nonStaticMember
    }
}

$class = new ChildClass();
$class->accessNonStaticMemberWithoutDollar();
$class->accessNonStaticMemberWithDollar();
```

비정적 멤버 변수로 접근하는 위와 같은 코드는 불가능하다. `::$`는 정적 멤버 변수에 접근할 때, `$`가 없는 `::`는 상수(`const`)에 접근할 때만 사용할 수 있으며, `::` 키워드를 통해서는 비 정적 메소드에만 접근할 수 있다는 것을 알아두자.

#### 상수와 메소드의 구분

```php
class SomeClass
{
    private const sameNameConstAndMethod = 'constant value';

    public function accessDoubleColone()
    {
        var_dump(self::sameNameConstAndMethod);
        var_dump(self::sameNameConstAndMethod());
    }

    private function sameNameConstAndMethod()
    {
        return "non static method";
    }
}

(new SomeClass)->accessDoubleColone();
```

`::target`으로 접근할 때 상수인지, 메소드인지는 `()`를 통해서 구분할 수 있으므로 문법상의 논리적인 문제점은 없다.

#### 상위 클래스의 멤버 변수에 접근하고 싶을 때는?

```php
class ParentClass
{    
    protected string $nonStaticMember = 'access ParentClass\'s $nonStaticMember';

    public function getParentClassNonStaticMemberVariable()
    {
        return $this->nonStaticMember;
    }
}

class ChildClass extends ParentClass
{
    protected string $nonStaticMember = 'access ChildClass\'s $nonStaticMember';

    public function getParentClassNonStaticMemberVariable()
    {
        return parent::getParentClassNonStaticMemberVariable();
    }
}

$class = new ChildClass();
var_dump($class->getParentClassNonStaticMemberVariable());
```

상위 클래스에서 멤버 변수에 접근할 수 있는 메소드를 만들어 자식 클래스에게 건네주는 방법을 사용해야 한다. 

## 부모 클래스의 멤버에 접근하게 할 경우 생기는 일

자바에서는 상위 클래스의 멤버 변수에 `super.memberVariable`으로 접근할 수 있는데, 이런 코드를 사용할 때 생각지도 못한 버그를 발생시킬 수 있다.

```java
Parent p = new Child();
System.out.println(p.value); // Parent's value
```

자바의 상속 관계에서 필드는 오버라이딩하는 대상이 아니라, 상위 클래스의 변수를 가린다.

`p`라는 변수는 부모 클래스 타입이다. 상위 클래스 타입의 변수에 하위 클래스의 인스턴스를 넣을 수 있는 것은 리스코프 치환 원칙을 만족하기 때문에 가능하다. 이 때 타입은 상위 클래스이므로 하위 클래스로 만든 인스턴스를 할당하더라도 상위 클래스가 제공하는 인터페이스만을 하위 클래스가 사용할 수 있다.

인터페이스는 공개 메소드에 적용되는 시그니처인데, 멤버 변수는 인터페이스의 대상이 아니다. 상위, 하위 타입의 관계는 (인터페이스를 사용하지 않더라도 공개 맴버가 같은) 상위 타입의 인터페이스를 하위타입에서 이용한다는 것이다.

상위 타입의 변수에 하위 타입의 인스턴스를 할당하면 해당 변수에서 사용할 수 있는 인터페이스는 동일하고, 해당 인터페이스에 대한 구현만이 다르다. 그에 반해, 멤버 변수는 오버라이딩을 하지 않기 때문에 구현이 달라진다는 개념이 적용이 안 된다. 따라서 동일한 멤버 시그니처 그대로 엑세스를 하고, 하위 인터스턴스에서 달라진 구현을 적용하지 않게 되고 상위 타압의 클래스의 멤버 값 그대로 이용한다.

이게 문제가 되는 이유는, 기본적으로 인스턴스화 된 객체의 멤버 변수를 변경하는데, 멤버 메소드에 의해 상태 변경이 여러 차례 이뤄진 후의 멤버 변수를 접근하려고 할 때, 상위 타입의 멤버 변수에만 접근을 하게 되어 원하는 결과를 획득할 수 없게 된다. 이로 인해서 실수를 할 수 있다. 따라서 캡슐화를 통해서 의도된 방식으로만 멤버 값에 엑세스하는 코드를 작성하도록 하는 것이 좋아 php에서는 부모 클래스의 비정적 멤버 변수에 엑세스 할 수 없게 하였다.

## References

- https://www.php.net/manual/en/language.oop5.paamayim-nekudotayim.php
