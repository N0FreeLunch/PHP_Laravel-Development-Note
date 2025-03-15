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

static을 사용하여 객체를 생성할 때는 생성자의 시그니처를 고정해 주어야 한다는 단점이 있다. 생성자의 시그니처를 고정한다는 판단을 하고 싶지 않은 경고, 간단하게 클래스를 정의할 때는 static 보다 self를 사용하는 편이 좋다.

## static의 특징

```php
class StaticTest
{
    private string $selfProperty = 'self property';

    final public function __construct() {}
	
    public function accessStaticProperty(): void
    {
        $static = new static;
        var_dump($static->selfProperty);
    }
}

(new StaticTest)->accessStaticProperty();
```

static은 self타입의 서브타입이다. 따라서 self의 프로퍼티에 static으로 인스턴스화된 객체가 접근할 수 있다.

## static의 문제점

static을 사용하면 정확히 어떤 타입을 사용하는지 static 부분만 보고서는 알 수 없고, 어떤 클래스에서 실행되었는지에 따라 주형이 되거나 바인딩하는 클래스가 달라진다는 문제점이 발생한다.

```php
class StaticTest
{
    private string $selfProperty = 'self property';

    final public function __construct() {}
	
    public function accessStaticProperty(): void
    {
        $static = new static;
        var_dump($static->staticProperty);
    }
}

(new StaticTest)->accessStaticProperty();
```

기본적으로 StaticTest 클래스에는 staticProperty가 존재하지 않는다. 상속된 어떤 클래스에서는 staticProperty를 가질 수 있지만, 해당 프로퍼티가 정의되지 않은 클래스도 존재할 수 있기 때문에 코드 정의단계에서 타입을 특정할 수 없는 단계에서 `$static->staticProperty`라는 코드는 정적 분석에 지적을 당한다. 다음 코드와 같이 대상 프로퍼티의 존재를 확인하는 코드로 정적 분석의 지적을 해소할 수 있다.

```php
class StaticTest
{
    private string $selfProperty = 'self property';

    final public function __construct() {}
	
    public function accessStaticProperty(): void
    {
        $static = new static;
        if (property_exists($static, 'staticProperty')) {
	    var_dump($static->staticProperty);	
	}
    }
}

(new StaticTest)->accessStaticProperty();
```

`property_exists`와 같은 런타임 프로퍼티 확인 기능은 IDE에 의해 프로퍼티가 정의된 곳으로 이동할 수 없고 프로퍼티명이 변경 되었을 때, 프로퍼티명이 문자열으로 되어 있어 파악하기 어렵다는 문제 때문에 권장되는 코딩스타일은 아니다. 

static을 사용했다면, static의 대상이 되는 클래스가 무엇인지 알 수 있는 맥락에서 사용을 해야 하는데, static의 대상 클래스가 무엇인지 모르는 상태에서는 정적 추론으로 지적된다. php는 정적 추론이 가능한 코드를 만들 수도 있고, 정적 추론이 불가능한 코드를 만들 수도 있다. 컴파일 언어가 컴파일 타임에 에러를 발견할 수 있는 것 처럼, php는 컴파일을 하지는 않지만 IDE나 정적 분석 툴에 의해 잘못된 코드를 감지할 수 있도록 가능하면 정적 추론이 가능한 코드를 만들기를 장려한다. static은 경우에 따라 정적 추론이 될 수도 있고 정적 추론이 되지 않을 수도 있는 코드이다.

self는 정의된 지점에서 어떤 클래스에서 정의되었는지에 따라 타입이 바로 결정되므로 정적 추론으로 타입이 확인되지만, static은 정의된 지점에서는 self 타입이나 self의 하위 타입이란 것만 알 뿐 구체적으로 어떤 클래스라는 것인지 정적 추론으로 확인할 수 없다. static을 사용한 코드는 사용한 지점에서 어떤 클래스에서 사용되었느냐를 보고 정적 추론으로 어떤 타입이 되는지를 결정한다. 대부분의 코드에서 static을 사용한 클래스를 알 수 있기 때문에 static을 써도 대부분 타입추론이 잘 된다.

## final 클래스에서의 static

```php
abstract class Abs
{
    abstract public function __construct();
    
    abstract public function newSelf(): self;
    
    abstract public function newStatic(): static;
}

final class FinalClass extends Abs
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

var_dump((new class extends FinalClass {})->newSelf());
var_dump((new class extends FinalClass {})->newStatic());
```

final 클래스인 경우, 상속이 없으므로 항상 self를 반환할텐데 static을 반환타입으로 적어야 하는 것이 이상한 느낌을 줄 수 있다는 [의견](https://github.com/php/php-src/issues/17725)이 있다. 위의 예에서 추상 클래스의 메소드 반환 타입으로 static을 정의했기 때문에 이를 상속하는 클래스도 self가 아닌 static을 반환해야 하는데, final 클래스인 경우 굳이 static을 반환할 필요가 없는데 문법상 상속을 받는 클래스에서 static 반환 타입을 바꿀 수 없는 제약이 이상하다는 것이다.

하지만 final 클래스를 사용하는 시점에서 static으로 반환된 타입은 FinalClass 클래스라는 것을 정적 추론으로 잘 판단되기 때문에 굳이 self로 바꿀 필요가 있을까?라는 의문이 있다. self로 하면 확실하게 특정 클래스인 것을 알 수 있기 때문에 좀 더 강력한 정적 추론이 일어날 수 있다는 의견이 있지만, 이런 추론이 강력하게 일어날 수 있는 예가 존재하는지도 의문이고 아직 제시되지 않았기 때문에 굳이 self로 강제할 필요는 없을 것 같다.

### static 유형과 클래스 유형의 불일치

```php
abstract class Abs
{
    abstract public function __construct();
    
    abstract public function newSelf(): self;
    
    abstract public function newStatic(): static;
}

final class FinalClass extends Abs
{
    public function __construct() {}
	
    public function newSelf(): self
    {
        return new self;
    }

    public function newStatic(): static
    {
        return new FinalClass;
    }
}

var_dump((new class extends FinalClass {})->newSelf());
var_dump((new class extends FinalClass {})->newStatic());
```

위 코드에서 static 반환 유형에 클래스 FinalClass로 객체를 만들어 반환하면 `Fatal error: Class FinalClass@anonymous cannot extend final class FinalClass`라는 에러가 발생한다. 스테틱 반환 유형은 `A@anonymous`라는 `@anonymous`가 붙는 특수한 반환 반환유형 곧 `new static`으로 선언된 대상만 받고, `new A`로 선언한 객체는 타입 불일치로 에러이다. 사실상 반환된 후의 값의 타입은 FinalClass로 동일할 것이지만, 반환하기 전 상태에서는 `new static` 과 `new FinalClass`을 서로 다른 타입으로 판단한다.

이는 상속을 고려했을 때는 다른 클래스이기 때문에 static이 가리키는 타입과 FinalClass가 다른 클래스가 되므로 타당하지만, 상속을 할 수 없는 final을 사용한 클래스인 경우 같은 클래스 타입이므로 논리상 에러를 발생시키지 않아도 된다. 물론 `new FinalClass`을 쓰지않고 `new static`으로 그냥 사용해도 아무런 문제는 없다.

## static:: self::

반환 유형이 아닌 주형 클래스에 접근하기 위해 쓰는 '대상클래스::프로퍼티'의 문법의 경우, `static::` `self::`을 사용할 때는 반환 유형의 static과 달리 객체를 꼭 생성해야 하는 것이 아니므로 생성자의 제약이 없다. 이 경우, static과 self는 정의된 클래스를 가리키느냐 사용된 클래스를 가리키느냐의 차이만을 갖는다.

```php
class ParentClass
{
    const PARENT_CONSTANT = 'parent class constant';
}

class ChildClass
{
    const CHILD_CONSTANT = 'child class constnat';
}
```

## self vs static

self도 static도 self의 프로퍼티에 접근할 수 있다. 정적 추론이 가능한 코드를 만들 때 둘은 슈퍼 타입과 서브 타입인 것을 빼고는 동일하다. 기본적으로 static을 사용한 코드는 self 프로퍼티에 접근할 수 있지만, 서브타입인 경우 어떤 타입인지 IDE가 모르는 경우가 생길 수 있다. 하지만 대부분의 경우 static 타입의 값이 반환될 때 어떤 클래스를 주형으로 한 인스턴스인지 알 수 있으므로 static 타입힌트로 반환된 타입의 객체가 어떤 클래스 타입인지 알 수 있다. (static 타입의 반환 값의 클래스를 추론하지 못하는 케이스가 있다는 내용도 찾을 수 없었다.)

static은 self가 할 수 있는 모든 것을 할 수 있는데 self를 사용할 필요가 있을까? self는 확실하게 클래스 하나를 가리킨다. self가 정의된 클래스를 가리키므로 어떤 클래스인지 정의한 지점에서 알 수 있다. 반면에 static은 정의한 시점에서는 어떤 객체인지 모를 수 있기 때문에 사용한 지점의 코드를 봐야 한다. 정의한 지점에서 꼭 타입을 고정해야 하는 경우라면 self, 정의한 지점에서 self가 가진 시그니처로 접근할 필요성이 있을 때는 static을 써도 상위 타입인 self의 프로퍼티로 접근할 수 있기 때문에 static, 사용한 지점에서 타입을 확인해도 되는 경우 static을 사용하는 것이 좋다.

static은 클래스에서 프로퍼티 내부의 정적 멤버 또는 일반 멤버에 접근할 때 주의 깊게 사용하는 편이 좋다.

static 반환 타입으로 반환된 객체의 경우는 프로퍼티에 접근하는 것과는 다르다. 어떤 방식으로든 객체를 생성할 것이고 객체를 생성하기 위해서는 시그니처의 고정이 필수적이다. 반환 유형에서 static을 사용하는 것은 `new static`과 같은 코드를 만든다. 생성자의 시그니처를 고정하지 않는다면 정적 분석에 의한 잘못된 사용이라는 지적을 당한다. 대부분의 클래스 작성에서 생성자의 시그니처를 고정하는 코드를 쓰는 것은 보일러 플레이트를 발생시키므로, 기본적으로 self 반환값을 사용하도록 하며, 상속을 고려한 객체 반환(메소드 체이닝과 같은 것을 고려할 때)이 필요한 경우, 생성자의 시그니처 고정과 함께 static 반환유형을 쓰도록 하자.

## References

- https://www.php.net/manual/en/language.oop5.late-static-bindings.php
- https://learn.microsoft.com/ko-kr/previous-versions/office/troubleshoot/office-developer/binding-type-available-to-automation-clients
- https://php.watch/versions/8.0/static-return-type
- https://wiki.php.net/rfc/static_return_type
- https://wiki.php.net/rfc/lsb_parentself_forwarding
