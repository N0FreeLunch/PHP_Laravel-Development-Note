# checked uncheck 예외

## 자바의 예외 처리

자바의 에러처리는 컴파일 타임에서 잘못된 구문과 문법을 사용했을 때 알려 주는 컴파일 에러와 예외가 발생했을 때 try-catch를 반드시 사용해야 하는 checked 예외와, 예외가 발생하면 try-catch 처리를 하든 해당 예외가 발생하지 않도록 하는 unchecked 예외가 존재한다.

### checked 예외

런타임에서 예외가 발생해도 적절한 예외 처리가 반드시 구비되기 때문에 예외로 인해 로직의 처리가 중단되지 않는 에러이다. 예외가 발생하면 try...catch 문으로 잡아서 처리를 하기 때문에 런타임에서 예외로 인해서 코드의 실행이 중단되지 않으며, 개발자가 의도한 예외 처리 로직으로 처리된다. unchecked 예외는 최상위 예외 Exception의 상속을 받지만 RuntimeException과 그 하위 타입이 아닌 모든 예외가 반드시 try...catch로 처리해야 할 예외이며, 만약 예외 처리를 하지 않을 시 컴파일 에러가 발생하는 에러이다.

### unchecked 에러

컴파일러가 예외 처리를 강제하는 checked 예외와 달리 예외 처리를 강제하지 않는다. 적절한 사전 조건의 부재, 의도와 다른 객체의 메소드 조작, 잘못된 코드 사용 등에 의해 발생하는 사용자의 실수를 정정하기 해 남기는 에러로, 개발자는 필요에 따라 사전 조건을 잘 설정하여 예외가 발생하지 않게 하거나, 개발자의 판단에 따라 예외 처리하도록 하는 예외이다. 실제 서버에서 예외가 발생했을 때 에러 처리가 없다면 적절한 에러 처리 또는 적절한 사전 조건을 부여해서 처리하도록 한다.

### checked 예외의 문제

checked 에러는 적절한 사전조건을 설정하면 발생하지 않기 때문에, 반드시 이에 대한 예외를 처리해야 하는지에 대한 논란이 있다. 혹시라도 이런 문제가 발생했을 때, 예외처리를 어떻게 할까? 적절한 예외 처리 방법이 있을까? 사실상 억지로 예외를 처리하는 것이라고 볼 수 있다. 사실상 이런 예외가 발생하면, 통지나 로그를 하는 로직을 추가하는 것이 흔히 생각할 수 있는 처리하며, 다른 특별한 처리 방법을 떠올리기 어렵거나 없는 경우가 있다. 오히려 적절한 사전 조건을 설정하여 에러가 발생하지 않도록 하는 것만이 제대로 된 해결책이다.

보통 던져진 예외가 잡히지 않으면 언어나 프레임워크의 최종적인 에러 처리 위치까지 예외가 도달하게 되고 종료된다. 종료되기 전의 처리에서 sentry 같은 서비스를 통해서 로그와 통지를 맡기는 경우도 있다. 이런 서비스는 보통 모든 예외에 대한 로그 및 통지를 하는데 사전 조건을 잘 세팅해 주어야 하는 경우 이를 통해서 무엇이 잘못되었는지 확인하고 코드나 환경을 잘 설정해 주면 된다. 그래서 checked 에러로 굳이 처리할 필요가 없는데 반드시 try...catch를 구비하는 checked가 필요할까라는 의문점이 있을 수 밖에 없다.

### checked 예외가 있어야 할까?

php는 checked 예외가 존재하지 않으며, 현대화된 자바라는 코틀린 언어도 checked 예외가 존재하지 않는다. 하지만 잘못된 에러처리로 인해 대량의 리퀘스트가 대량의 예외를 생성할 때 예외 처리가 없다면, 무수한 스텍트레이스의 생성으로 인해서 시스템 리소스가 부족해 애플리케이션 서버가 터질 수 있다는 문제가 있다.

때때로 사전조건을 잘 설정하는 것만으로는 예외가 발생하지 않도록 할 수 없는 경우가 있다. 사전 조건이 설정되지 않는다는 것은, 사전에 미리 예방을 할 수 없는 런타임의 여러 조건에 따라 예외가 발생할 수도 있는 경우이다. 이 때 checked 예외와 같이 예외 처리를 강제하는 것이 좋다.

## checked 예외가 없는 php

php는 파일이 실행될 때 컴파일 타임이 있기는 하지만, 구문 및 문법 오류를 적극적으로 검사하는 컴파일 언어와 달리, php 코드를 파싱하면서 감지 가능한 최소한의 문법 및 구문 오류를 감지하는 정도의 역할만을 한다. 또한 하나의 파일에 대한 검사이기 때문에 여러 파일에 걸쳐서 이뤄져야 하는 구문 및 문법 오류는 감지할 수 없다.

php에서 checked 예외는 없는데, 이는 php가 발생한 예외를 컴파일 타임에 확인할 수 없기 때문이다. 예외를 발생시키는 어떤 코드를 여러 코드에서 사용할 경우 이를 사용하는 코드에 예외 처리 로직이 있는지 검사하는 것은 php의 구문 파싱의 단계를 넘는 적극적인 구문 및 문법 검사 기능으로 이는 성능 문제를 초래하므로 이뤄지지 않는다.

php도 자바처럼 [RuntimeException](https://www.php.net/manual/en/class.runtimeexception.php)을 가지고 있지만 이는 SPL 라이브러리의 에러 처리를 위한 예외 처리 분류로 이는 정적 분석이 없던 시절에 php5.x에 도입된 예외로 자바의 checked, unchecked와 같은 기능을 의도하고 만들어진 예외 분류는 아니다. 다음 예외는 [SPL 라이브러리의 하위](https://www.php.net/manual/en/spl.exceptions.php) 예외 분류이다.

```
Exceptions
    BadFunctionCallException — The BadFunctionCallException class
    BadMethodCallException — The BadMethodCallException class
    DomainException — The DomainException class
    InvalidArgumentException — The InvalidArgumentException class
    LengthException — The LengthException class
    LogicException — The LogicException class
    OutOfBoundsException — The OutOfBoundsException class
    OutOfRangeException — The OutOfRangeException class
    OverflowException — The OverflowException class
    RangeException — The RangeException class
    RuntimeException — The RuntimeException class
    UnderflowException — The UnderflowException class
    UnexpectedValueException — The UnexpectedValueException class
```

## 정적 분석과 checked 예외

기본적으로 phpstan을 사용하면, 모든 예외는 반드시 예외 처리를 해 주어야 하므로 checked 예외가 된다. phpstan에는 unchecked 예외로 분류할 예외와 checked 예외로 분류할 대상을 나눌 수 있다. phpstan이 무엇을 cheked로 할지 무엇을 unchecked로 할지 설정을 해 주어야 하는 반면, [phpstan-exception-rules](https://github.com/pepakriz/phpstan-exception-rules)라는 확장 기능을 사용하면, php에 내장된 예외 클래스를 디폴트로 LogicException 클래스는 checked 예외로, RuntimeException 클래스는 unchecked 예외로 사용하도록 한다.

### [LogicException](https://www.php.net/manual/en/class.logicexception.php)과 [RuntimeException](https://www.php.net/manual/en/class.runtimeexception.php)

#### RuntimeException

> Exception thrown if an error which can only be found on runtime occurs.

런타임에서만 확인할 수 있는 종류의 에러라고 설명한다. 이는 사전 조건을 부여하는 등의 방법으로 해결할 수 없고 런타임의 조건에 의해서만 에러의 발생 유무를 알 수 있는 것에 해당한다.

#### LogicException

> Exception that represents error in the program logic. This kind of exception should lead directly to a fix in your code.

발생해서는 안 되는 에러로, 이런 에러가 발생하지 않도록 전제 조건을 부여한다던지 잘못된 코드를 수정해야하는 종류의 에러를 의미한다. 이런 에러가 발생했다는 것은 코드를 잘못 만들었거나, 사전 조건을 잘못 세팅 했거나, 각종 설정 옵션들의 잘못 혹은 연동되는 외부 환경에 대한 엑세스 문제 등이 요인이 될 수 있기 때문에 이를 해결해 주어야 한다.

## Error과 LogicException

## References
- https://news.ycombinator.com/item?id=36708759
- https://php.watch/articles/PHP-exception-hierarchy
