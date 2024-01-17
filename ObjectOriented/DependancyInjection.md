## 의존성 주입
- 의존성 주입은 객체의 외부에서 객체 내부에서 사용할 대상을 주입하겠다는 의미를 가진다.

### 의존성이란?
- 의존성이 있다는 것은 하나의 객체가 객체 외부의 다른 대상에 의존하고 있어서, 외부의 의존하는 대상이 없다면 코드가 동작하지 않는 경우를 의미한다.
- 의존성은 객체 뿐만 아니라 대상을 구분할 수 있는 어떤 단위에 대해 해당 대상이 아닌 다른 부분의 코드를 의존하고 있을 때 의존성이 있다고 한다.
- 객체지향에서 의존성이 있다는 의미는 하나의 객체가 다른 객체를 사용하므로, 의존하는 외부 객체가 없다면 동작할 수 없는 객체를 의미한다.

### 의존성 주입의 종류
- 객체가 외부로 부터 객체를 전달 받는 방식은 객체를 생성할 때 생성자에 객체를 인자로 전달하여 주입할 수 있고, `setter` 메소드를 통해서 주입할 수도 있다.

### 의존성 주입을 사용해야 하는 이유
- 객체는 클래스를 기준으로 만들어진다. 클래스를 인스턴스화 할 때 생성자에 값을 주입받고 객체가 만들어지고, 객체가 가진 각종 메소드들은 값을 받아서 객체 내부의 상태를 갖는다. 하나의 클래스로 만들어진 객체 각각은 다양한 상태를 가지고 있다. 이런 다양한 상태를 가진 객체를 의존하고 있다면, 어떤 상태인 객체를 의존하는지 뭔가 정해져야 의존성을 가진 객체의 상태를 추적 및 예측할 수 있다.
- 의존성 주입은 특정한 상태의 객체를 객체 외부로 부터 객체 내부로 전달하여, 전달된 의존 대상의 결정된 상태를 통해서 의존성이 있는 대상의 상태를 좀 더 정확하게 알 수 있다.
- 이렇게 의존 대상의 상태를 결정해서 주입하는 것을 통해서, 의존성이 있는 대상의 상태를 예측할 수 있는 범위로 만들 수 있고, 이로서 테스트 가능한 코드를 만들 수 있다.

### `new`와 의존성 주입
- 객체 내에서 `new` 키워드를 통해서 외부의 클래스를 사용할 수 있지만, 만약 객체 내부에서 선언된 클래스가 상태를 가지는 것이라면, `new` 키워드로 만든 대상은 객체의 상태를 적절히 표현하지 못할 수 있다.
- 많은 웹 프레임워크에서 생성자 주입을 사용하는데, 데이터베이스의 정보를 가져오는 모델 객체, 그리고 이 모델 객체를 통해서 만드는 서비스 계층의 객체를 생성자 주입으로 할당한다. 왜냐하면 데이터베이스의 정보를 가져오는 객체는 상태를 가지고 있기 때문이다. 이 상태는 `new` 키워드로 객체 내부에서 선언해서 결정할 수 없기 때문에 이를 사용하는 객체의 상태를 예측 불가능하게 만든다. 따라서 생성자 주입을 모델 객체를 외부에서 넣어 주는 방식으로 만들게 되면, 테스트 코드를 만들 때 외부에서 결정된 상태의 모의 객체의 모델을 할당하는 방식으로 의존하는 대상 객체의 상태를 예측할 수 있게 한다.
- 일반적으로 `new` 키워드를 사용해서 객체 내부에서 만드는 것은 해당 객체의 상태를 선언하는 시점에서 결정할 수 있을 때 사용한다. 이는 의존 대상 객체의 상태를 의존하는 객체에서 정할 수 있기 때문에 의존하는 객체의 상태를 예측하는데 아무런 지장을 주지 않기 때문이다.

### 프레임워크의 의존성 주입
- 어떤 객체가 많은 객체에 대한 의존성을 가지고 있는 경우, 이를 모두 의존성 주입을 하게 되면 객체를 생성하는 부분마다 의존하는 코드를 다 적어 주어야 한다. 생성자나 `setter`의 파라메터를 정의하는 것 뿐만 아니라 객체를 생성하는 코드에서 인자로 의존 대상을 넣는 코드를 작성해야 한다.
- 프레임워크는 어느 정도 자동으로 의존성 주입을 해 주는 기능을 갖고 있는데, 생성자나 setter의 파라메터만 정해주면, 객체를 생성하는 코드를 작성하여 의존 대상을 직접 넣어주지 않아도 자동으로 주입하는 기능이 있다. 라라벨의 예를 들면, 라라벨에서 클래스를 인스턴스화 하는 코드를 직접 적어주지 않고 실행되는 많은 라라벨의 기능에서 파라메터로 클래스를 타입힌트로 적어주는 것으로 실행시 객체를 자동으로 주입해 준다. 이러한 프레임워크의 의존성 주입 기능을 통해서 객체에 일일이 의존 대상을 주입하는 코드를 적지 않아도 되는 편리함을 제공한다.
- 라라벨의 기능과 비슷하게 타입힌트로 의존 대상을 주입하는 [php 라이브러리](https://php-di.org/)가 있다.

### Factory pattern
- 의존성 주입을 하게 되면, `new` 키워드로 객체를 생성하지 않고 파라메터를 통해서 생성된 객체를 전달 받기 때문에 주입된 단 하나의 객체만 사용할 수 있다. 이 때문에 주입된 객체를 `clone`하는 코드를 통해서 여러번 재사용하려는 시도를 할 수 있다.
- 이러한 재사용의 문제점을 해결하기 위한 방법으로 펙토리 패턴을 사용한다. 펙토리 객체는 객체를 복사 또는 생성하는 기능을 갖고 있다. 펙토리 객체가 가진 `create` 접미사의 메소드를 사용해서 객체를 반복적으로 찍어내며 의존성 주입할 대상을 펙토리 객체로 받고, `create` 메소드를 호출하는 것으로 객체를 여러번 재사용할 수 있는 코드를 만들 수 있다.
```php
class Service
{
    public function run()
    {
        echo "Service".PHP_EOL;
    }
}

class ServiceFactory
{
    public function __construct(private readonly Service $service) {}

    public function create()
    {
        return clone $this->service;
    }
}

class Controller
{
    public function action(ServiceFactory $seviceFactory)
    {
        $seviceFactory->create()->run();
        $seviceFactory->create()->run();
        $seviceFactory->create()->run();
    }
}

function runImitationIoC() {
    (new Controller)->action(new ServiceFactory(new Service));
}

runImitationIoC();
```

### 생성자 사용 불가
- 프레임워크의 IoC 기능을 사용하면, 프레임워크에 의해 의존성이 자동으로 주입된다. 라라벨 프레임워크의 많은 기능에서 타입힌트를 통해서 생성자 주입을 사용할 수 있다.
- 생성자를 주입하게 되면, 일반적인 코드 정의 영역에서 생성자를 사용하지 못하게 된다. 보통 인스턴스를 만들 때, 객체의 상태를 만들 때 멤버를 초기화 할 때 필수적인 값들은 생성자를 통해서 받는 것이 일반적이다. 필수적인 값은 생성자를 통해서 받고, 옵션 값은 메소드를 통해서 받는 식이다. 하지만, 생성자를 실행할 권한을 프레임워크가 가져갔기 때문에 메소드를 통해서 필수 멤버와 옵션 멤버를 받아야 한다.
```php
class NoConstructorClass
{
    private readonly mixed $optionalMember;
    private readonly mixed $requiredMember;
	
    public function optionalSetter($value): self
    {
        $this->optionalMember = $value;
        return $this;
    }
	
    public function requiredSetter(): self
    {
        $this->requiredMember = $value;
        return $this;
    }
	
    public function runSomething()
    {
        $this->defineOptionalMemeber();
        echo "run something".PHP_EOL;
        echo "optionalMember : ".PHP_EOL;
        var_dump($this->optionalMember);
        echo "requiredMember : ".PHP_EOL;
        var_dump($this->requiredMember);
	}
	
    private function defineOptionalMemeber()
    {
        $this->optionalMember ?? null;
    }
}

// (new NoConstructorClass)->runSomething(); // error
(new NoConstructorClass)->optionalSetter('a')->requiredSetter('b')->runSomething();
```
- php에서 멤버 변수를 선언할 때 멤버 변수의 타입을 지정하지 않으면 기본적으로 `null`이 할당된다. 멤버의 타입을 선언하면 반드시 값이 할당되어야 하며 기본값 `null`이 할당되지 않는다. 이를 이용하여, 정의가 되지 않은 멤버 변수를 사용할 때는 에러가 발생하는 것을 이용해서 필수로 받아야 하는 `setter`를 만들 수 있다. `optional`으로 만들기 위해서는 객체의 핵심 로직을 실행하기 전에 옵션 멤버의 경우 기본값을 할당하는 방식을 사용하면 된다. 필수 메소드의 경우 정의되지 않았기 때문에 실행되지 않는 메시지 보다는 'not defined required member'와 같은 에러 메시지를 던져주는 편이 좋다. 하지만 이것은 사용상의 편의를 위한 조치로 시간적인 개발 시간적인 여유를 고려하여 추가하도록 하자.
```php
private function checkRequiredMember()
{
    $this->requiredMember ?? throw new Exception("not defined required member");
}
```
