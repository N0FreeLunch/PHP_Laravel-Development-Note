## 리프코프 치환 원리
```php
class ParentClass {
    function runParentMethod(): string
    {
        return "parent\n";
    }
}

class ChildClass extends ParentClass {
    function runChildMethod(): string
    {
        return "child\n";
    }
}

function testLiskovSubstitution(ParentClass $obj): void {
    echo $obj->runParentMethod();
    // echo $obj->runChildMethod(); // not error in php engine but error in static analysis
    if(method_exists($obj, 'runChildMethod')) {
        echo $obj->runChildMethod(); // not error in php engine and static analysis
    }
}

testLiskovSubstitution(new ChildClass);
```
위 코드의 결과는
```
parent
child
```
이다.

### php 엔진에 의한 실행
- `testLiskovSubstitution(new ChildClass)` 코드를 보자. 부모 클래스를 받는 파라메터 `ParentClass $obj`에 자식 클래스의 오브젝트가 전달 되었다.
- `testLiskovSubstitution` 함수 내부에 `$obj->runChildMethod()` 코드를 보자. 만약 자식 클래스의 인스턴스가 파라메터로 전달 되었다면 `runChildMethod` 메소드는 자식 클래스에 정의된 메소드이기 때문에 실행이 될 수 있다. 그러나 부모 클래스의 인스턴스가 파라메터로 전달되었다면, `runChildMethod` 메소드는 부모 클래스의 인스턴스에는 존재하지 않는 메소드이기 때문에, `$obj->runChildMethod()` 코드가 실행될 때 에러가 발생한다.

### 정적 타입 언어라면?
- 정적 타입 언어의 경우 php와 같이 동작하지 않는다. 부모 클래스의 타입을 매개변수로 하였다면, 해당 매개 변수를 통해서 함수 내부로 전달된 값은 부모 클래스의 인스턴스가 가진 메소드의 시그니처만을 이용할 수 있다.
- 왜냐하면, 부모의 타입의 경우 부모 클래스의 상속을 받는 모든 자식, 자손 클래스의 인스턴스를 전달 받을 수 있는데, 모든 자식-자손 인스턴스에 대해서 함수 내부의 동작이 성립하기 위해서는 부모-자식-자손 인스턴스가 공통으로 가진 인터페이스만을 이용해야 하기 때문이다.

### php와 정적 타입 언어의 차이
- php에서는 매개변수 타입으로 지정한 부모 클래스에는 정의되지 않았지만, 자식 클래스의 인스턴스가 전달되었을 때 자식 클래스에 정의된 메소드를 함수 내부에서 사용할 수 있고, 만약 정의되지 않은 메소드인 경우 객체의 프로퍼티에 엑세스하는 지점에서 에러가 발생하는 것에 반해, 정적 타입 언어의 경우 함수의 매개변수로는 부모 타입으로 지정하고 함수 내부로 전달된 값은 자식 클래스인 경우, 매개변수 타입(부모 클래스의 타입)으로 정의되지 않은 메소드를 사용하는 것은 컴파일 또는 타입체크 타이밍에 오류를 발생시킨다. php와 달리 원천적으로 이러한 방식의 코딩을 차단한다. 이를 리스코프 치환 원칙이라고 부른다.
- php에서 정적 타입 언어와 같은 방식의 코딩 방식을 사용하면, 리스코프 치환 원칙을 만족하지 않아 런타임에 발생하는 에러를 미리 방지할 수 있다는 장점이 있다. 정적 분석 도구로 이를 가능하게 하며, `testLiskovSubstitution` 함수 내의 `$obj->runChildMethod()`의 접근을 phpstan에서는 오류로 감지한다. 하지만 오브젝트에 메소드가 존재하는지 확인하는 `if(method_exists($obj, 'runChildMethod'))`와 함께 사용하면, 부모 클래스 타입의 매개변수로 자식 인스턴스가 전달되었을 때 부모 클래스에는 정의되지 않은 자식-자손 클래스에 정의된 메소드를 접근할 수 있도록 한다. 이는 정적 타입 언어와 다른 런타임 언어이기 때문에 가능한 특징이다.

### 리스코프 치환 원칙이란?
- OOP 언어에서 어떤 클래스 타입의 객체를 전달했다면 전달된 곳에서는 타입으로 지정한 클래스가 가진 공개 멤버만을 사용해야 한다.
