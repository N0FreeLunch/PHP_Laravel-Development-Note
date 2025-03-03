# 클래스 바인딩

클래스는 객체와 달리 그 자체로는 값으로 사용하거나 코드를 실행할 수 없다. 클래스를 주형으로 객체를 생성하여 사용을 하든지, 클래스의 정적 멤버에 접근하여 정적 변수의 값 또는 정적 메소드를 실행하든지의 방식을 사용할 수 있다.

객체를 통해서 클래스에 접근을 하려면 객체의 값에 클래스를 바인딩 해 주어야 한다. 기본적으로 A 클래스를 주형으로 생성된 객체는 A 클래스를 바인딩 하고 있고, B 클래스를 주형으로 하여 생성된 객체는 B 클래스를 바인딩한다.

## self와 static

클래스에서 `self`는 `self`의 코드가 기재된 클래스를 가리키며, 클래스내의 `static`은 `static` 코드가 실행된 클래스를 가리킨다.

객체를 생성할 때 `new self`는 해당 코드가 정의된 클래스를 주형으로 객체를 만들고, `new static`은 해당 코드가 정의된 클래스를 주형으로 객체를 만든다. 

```php
class A
{
    public function newSelf(): self
    {
        return new self;
    }

    public function newStatic(): static
    {
        return new static;
    }
}

var_dump((new class extends A {})->newSelf()); // object(A)#2 (0) {}
var_dump((new class extends A {})->newStatic()); // object(A@anonymous)#1 (0) {}
```

위의 예제에서 self는 해당 키워드가 사용된 클래스인 A 클래스의 오브젝트가 되지만, static은 해당 키워드가 실행되었을 때의 클래스인 익명함수 클래스의 오브젝트가 된다.

C#과 자바와 같은 다른 객체 지향 언어에서는 실행하고 있는 클래스를 주형으로 한 객체를 만들기 위해서는 객체를 생성하는 코드가 포함된 메소드를 오버라이딩하여 실행하고 있는 클래스가 정의된 클래스가 되도록 만들어야 하지만, php는 객체를 생성하는 코드가 정의된 클래스를 바인딩 할 클래스로 할 것인지 (self를 사용), 객체를 생성하는 코드가 실행된 클래스를 바인딩 할 클래스로 할 것인지 (static을 사용) 정할 수 있다.

## 정적 분석으로 보기

```php
class A
{
    public function __construct() {}

    public function newSelf(): self
    {
        return new self;
    }

    public function newStatic(): static
    {
        return new static; // Unsafe usage of new static().
    }
}

var_dump((new class extends A {})->newSelf()); // object(A)#2 (0) {}
var_dump((new class extends A {})->newStatic()); // object(A@anonymous)#1 (0) {}
```

[에러가 발생하는 이유](https://phpstan.org/blog/solving-phpstan-error-unsafe-usage-of-new-static)는 static은 객체를 생성하는 코드가 실행된 클래스를 주형으로 인스턴스를 생성하는데, 생성자가 고정이 되지 않기 때문에, 런타임에 주형이 되는 클래스의 생성자의 인터페이스가 맞지 않기 때문에 정적 분석 도구는 에러를 발생시킨다. 이런 문제를 회피하기 위한 방법으로는 생성자의 시그니처를 고정하여 객체를 생성하는 방법을 고정하는 방법이 존재한다. `final public function __construct() {}`으로 생성자에 final 키워드를 붙여주면 되는데, 상속하는 모든 클래스는 생성자의 코드가 고정되기 때문에 확장성이 너무 제한된다는 문제가 있다. 그래서 시그니처만 고정하고 생성자 내부 메소드는 변경가능하도록 추상 클래스, 인터페이스로 생성자의 시그니처만 고정하여 사용하는 것을 추천한다. 이러한 고정된 시그니처의 생성자를 consistent constructor라고 부른다.

### 추상 클래스를 사용하는 예

```php
abstract class Abs
{
	abstract public function __construct();
}

class A extends Abs
{
    public function __construct() {}
	
    public function newSelf(): self
    {
        return new self;
    }

    public function newStatic(): static
    {
        return new static;
    }
}

var_dump((new class extends A {})->newSelf());
var_dump((new class extends A {})->newStatic());
```

### 인터페이스를 사용하는 예

```php
interface I
{
	public function __construct();
}

class A implements I
{
    public function __construct() {}
	
    public function newSelf(): self
    {
        return new self;
    }

    public function newStatic(): static
    {
        return new static;
    }
}

var_dump((new class extends A {})->newSelf());
var_dump((new class extends A {})->newStatic());
```

## static의 문제점

static을 사용하면 정확히 어떤 타입을 사용하는지 static 부분만 보고서는 알 수 없고, 어떤 클래스에서 실행되었는지에 따라 주형이 되거나 바인딩하는 클래스가 달라진다는 문제점이 발생한다.

```php
class StaticTest
{
    final public function __construct() {}
	
    public function accessStaticProperty(): void
    {
        $static = new static;
        $static->propertyA;
    }
}
```

기본적으로 StaticTest 클래스에는 propertyA가 존재하지 않는다. 상속된 어떤 클래스에서는 propertyA를 가질 수 있지만, 해당 프로퍼티가 정의되지 않은 클래스도 존재할 수 있기 때문에 코드 정의단계에서 타입을 특정할 수 없는 단계에서 `$static->propertyA`라는 코드는 에러를 발생시킨다. 다음 코드와 같이 대상 프로퍼티의 존재를 확인하는 코드로 정적 분석의 지적을 해소할 수 있다.

```php
class Sample
{
    final public function __construct() {}
	
    public function accessStaticProperty(): void
    {
        $static = new static;
		if (property_exists($static, 'propertyA')) {
		    var_dump($static->propertyA);	
		}
    }
}
```

런타임 프로퍼티 확인은 IDE에 의해 프로퍼티가 정의된 곳으로 이동할 수 없고 프로퍼티명이 변경 되었을 때, 프포러티명이 문자열으로 되어 있어 파악하기 어렵다는 문제 때문에 권장되는 코딩스타일은 아니다. IDE 등에 의한 프로퍼티 추론을 하기 위해서는 실행 시점에서 선택되는 타입이 무엇인지 `assert($objFromStatic instanceof ExecutionClass)`등과 같은 코드로 타입을 체크해서 IDE에 알려 주어야 IDE의 코드 간 이동을 수행할 수 있다.

static을 사용했다면, static의 대상이 되는 클래스가 무엇인지 알 수 있는 맥락에서 사용을 해야 하는데, static의 대상 클래스가 무엇인지 모르는 상태에서는 정적 분석의 에러가 발생할 수 밖에 없다.

## References

- https://www.php.net/manual/en/language.oop5.late-static-bindings.php
- https://learn.microsoft.com/ko-kr/previous-versions/office/troubleshoot/office-developer/binding-type-available-to-automation-clients
- https://php.watch/versions/8.0/static-return-type
- https://wiki.php.net/rfc/static_return_type
