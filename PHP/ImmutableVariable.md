## php에서 지원하는 불변 변수

### final
- https://www.php.net/manual/en/language.oop5.final.php
- 상속시 더이상 오버라이딩을 할 수 없도록 제한을 거는 키워드이다. A->B->C 순으로 상속이 될 때 A에서 상속 받은 B의 멤버에 `final` 키워드를 걸어주면 C에서는 B에서 final을 건 멤버는 오버라이딩을 할 수 없다. 물론 A에서 멤버에 `final`을 사용하면 
- 자바의 final과 달리 상속시 오버라이딩을 막지만 재할당을 방지하는 기능을 가지고 있지는 않다. php에서 제할당을 방지하는 키워드는 php8버전 이후로 지원되는 readonly 키워드를 사용해야 한다.

#### 클래스에 붙여 줄 경우
```
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
```
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
- https://www.php.net/manual/en/language.oop5.constants.php
- 클래스 내부에 상수를 정의하기 위한 방법
- 상수는 인터페이스가 가질 수도 있다.
- 객체의 멤버로 사용된다. (글로벌 상수를 정의하는 const 키워드도 있다.)

```
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
```
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
- public으로 된 상수는 접근가능하지만, private로 된 상수는 접근 불가
- 접근 제한자가 따로 붙는 키워드이기 때문에 접근 제한자에 따라 접근 및 상속 후 다룰 수 있는 특성이 달라진다.


#### 추상 클래스의 내부 멤버에 붙일 수 있다.
```
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

