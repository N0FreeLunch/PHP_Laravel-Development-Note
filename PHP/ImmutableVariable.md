## php에서 지원하는 불변 변수

### final
- 상속시 더이상 오버라이딩을 할 수 없도록 제한을 거는 키워드이다. A->B->C 순으로 상속이 될 때 A에서 상속 받은 B의 멤버에 `final` 키워드를 걸어주면 C에서는 B에서 final을 건 멤버는 오버라이딩을 할 수 없다. 물론 A에서 멤버에 `final`을 사용하면 
- 자바의 final과 달리 상속시 오버라이딩을 막지만 재할당을 방지하는 기능을 가지고 있지는 않다. php에서 제할당을 방지하는 키워드는 php8버전 이후로 지원되는 readonly 키워드를 사용해야 한다.

#### 클래스에 붙여 줄 경우
```php
<?php
final class BaseClass {
   public function test() {
       echo "BaseClass::test() called\n";
   }

   // As the class is already final, the final keyword is redundant
   final public function moreTesting() {
       echo "BaseClass::moreTesting() called\n";
   }
}

class ChildClass extends BaseClass {
}
// Results in Fatal error: Class ChildClass may not inherit from final class (BaseClass)
?>
```
- BaseClass 클래스 앞에 final을 붙여 주었다.
- BaseClass 클래스를 상속하는 ChildClass를 만들려고 했으나 컴파일 타임(Fatal error)에 상속할 수 없다고 나온다.


#### 클래스 멤버에 붙여줄 경우
```php
<?php
class BaseClass {
   public function test() {
       echo "BaseClass::test() called\n";
   }
   
   final public function moreTesting() {
       echo "BaseClass::moreTesting() called\n";
   }
}

class ChildClass extends BaseClass {
   public function moreTesting() {
       echo "ChildClass::moreTesting() called\n";
   }
}
// Results in Fatal error: Cannot override final method BaseClass::moreTesting()
?>

```
- BaseClass 클래스의 moreTesting 메소드에 final 키워드를 붙였다. 
- ChildClass는 BaseClass를 상속할 수는 있지만 moreTesting을 오버라이딩 하려면 컴파일 타임에서 오버라이드 할 수 없다는 에러가 난다.

### CONST
- 클래스 내부에 상수를 정의하기 위한 방법으로 사용된다.
- 상수는 인터페이스가 가질 수도 있다.
- 글로벌 스코프에서 상수를 정의할 때 또는 객체 스코프에서 상수를 정의할 때 사용할 수 있으며 함수나 메소드 스코프에서의 상수 사용은 할 수 없다.
- 컴파일 타임에서 정의되어야 하므로 런타임 환경에서 정의해야 하는 함수의 반환 값, 여러 형태의 연산의 결과를 const 키워드에 할당할 수 없다.
- 자바 언어에서는 키워드 및 변수를 상수와 구분하기 위해 상수를 대문자와 언더바의 조합으로 명명하지만, php에서는 변수의 경우 `$` 표시를 사용하므로 대문자를 고집할 필요는 없다. 따라서 카멜케이스로 표기할 수 있다는 장점이 있다. 하지만 PSR-1에 따르면 클래스 상수는 대문자와 언더바의 조합으로 명명하도록 지시하기 때문에 특별한 이유가 없는 한 카멜케이스 표기를 사용하지 않도록 하자.
> Class constants MUST be declared in all upper case with underscore separators.

```php
<?php
class 
- MyClass 
{
    const CONSTANT = 'constant value';

    function showConstant() {
        echo  self::CONSTANT . "\n";
    }
}

echo MyClass::CONSTANT . "\n";

$classname = "MyClass";
echo $classname::CONSTANT . "\n";

$class = new MyClass();
$class->showConstant();

echo $class::CONSTANT."\n";
?>
```
- MyClass 내부에 CONSTANT라는 상수를 정의한다.
- 상수는 `self::CONSTANT` self 키워드를 사용해서 정적으로 호출할 수도 있다.


#### php 7.1 버전 부터 상수에 접근 제한자를 붙일 수 있다.
```php
<?php
class Foo {
    public const BAR = 'bar';
    private const BAZ = 'baz';
}
echo Foo::BAR, PHP_EOL;
echo Foo::BAZ, PHP_EOL;
?>
```

- result
```
bar

Fatal error: Uncaught Error: Cannot access private const Foo::BAZ in …
```
- public으로 된 상수는 접근가능하지만, private로 된 상수는 접근 불가하다.
- 접근 제한자가 따로 붙는 키워드이기 때문에 접근 제한자에 따라 접근 및 상속 후 다룰 수 있는 특성이 달라진다.


#### 추상 클래스의 내부 멤버에 붙일 수 있다.
```php
abstract class dbObject
{   
    const TABLE_NAME='undefined';
   
    public static function GetAll()
    {
        $c = get_called_class();
        return "SELECT * FROM `".$c::TABLE_NAME."`";
    }   
}

class dbPerson extends dbObject
{
    const TABLE_NAME='persons';
}

class dbAdmin extends dbPerson
{
    const TABLE_NAME='admins';
}

echo dbPerson::GetAll()."<br>";//output: "SELECT * FROM `persons`"
echo dbAdmin::GetAll()."<br>";//output: "SELECT * FROM `admins`"
```

- 추상 클래스를 상속 받아 클래스화 할 때 추상 메서드의 const 멤버를 불러와서 사용할 수 있다.

## 익명 클래스
- 함수나 메소드 스코프에서 상수를 정의할 수 없고, 재할당을 방지하는 `final` 등의 키워드를 제공하지 않기 때문에 함수나 메소드 등의 내부에서 고정값 및 상수를 정의하기 위해서는 익명 클래스를 사용하여 변수나상수를 정의하는 방법이 있다.

### 익명 클래스로 상수 선언하기
```php
class SampleClass
{
   public function percentToPoint(int $percent)
   {
      $consts = new class {
         const dividend = 100;
      };
      return $a / $consts::dividend;
   }
}
```
- 어떤 함수나 메소드의 범위 내에서만 사용되는 상수의 경우 위와 같은 방식으로 상수를 정의해서 사용하면 해당 스코프 범위에서만 사용되는 값이구나를 알 수 있고 다른 곳에서는 사용되지 않는 것임을 알 수 있기 때문에 인지의 부하를 줄여준다.

### 런타임에서의 사용
```php
class SampleClass
{
   public function introduceMessage($id)
   {
      $userInfo = new Class (User::find($id)) {
         public readonly string $name;
         public readonly string $address;
         public readonly string $birthDate;
         public function __construct(User $user) {
            $this->name = $user->name;
            $this->address = $user->address;
            $this->birthDate = $user->birthDate;
         }
      };
      echo "{$user->address}지역에 사는 {$userInfo->name}님의 생년월일은 {$userInfo->birthDate} 입니다.";
   }
}
```
- 위 예제에서 런타임에 변수 할당이 필요한 대상에 `readonly`를 사용하여 익명 클래스 선언 이후의 코드에서 `address`, `name`, `birthDate`를 변경할 수 없도록 하였다.
- 위의 예제에서는 한 줄로 표현했지만 `address`, `name`, `birthDate`를 사용하는 코드가 많아질수록 어디서 변경이 되었을 가능성을 코드를 해석할 때 염두해 두어야 한다. 하지만 위와 같이 불변값으로 고정해 둔다면 코드가 많아지더라도 불변이기 때문에 이런 가능성을 염두해 두지 않아도 되는 장점이 있다.
- `readonly` 키워드는 php8.1 부터 사용할 수 있는 키워드이다.
- 런타임에서 함수나 메소드 스코프의 불변값을 선언하는 것은 익명클래스 + 생성자 + 멤버 변수를 선언해야 하는 작업이 들기 때문에 지나치게 보일러 플레이트가 많이 필요한 코드가 된다. 언제나 불변값을 선언하는 것이 좋지만, 해당 스코프에서 코드의 길이가 많아지는 경우에만 사용하고 비교적 짧은 코드에서는 사용하지 않는 편이 좋다는 것을 기억하자.

## Reference
- https://www.php.net/manual/en/language.oop5.final.php
- https://www.php.net/manual/en/language.oop5.constants.php
- https://www.php.net/manual/en/language.oop5.anonymous.php
