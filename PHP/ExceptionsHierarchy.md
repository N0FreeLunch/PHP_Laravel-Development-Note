# 예외 계층

```
Throwable
  ├── Error
  │     ├── ArithmeticError
  │     │       └── DivisionByZeroError
  │     ├── AssertionError
  │     ├── CompileError
  │     │       └── ParseError
  │     ├── TypeError
  │     │       └── ArgumentCountError
  │     ├── ValueError
  │     ├── UnhandledMatchError
  |     ├── FiberError
  |     └── Random\RandomError
  │     │       └── BrokenRandomEngineError
  |     └── DateError
  |             ├── DateObjectError
  │             └── DateRangeError
  └── Exception
        ├── ClosedGeneratorException
        ├── DOMException
        ├── ErrorException
        ├── IntlException
        ├── JsonException
        ├── LogicException
        │       ├── BadFunctionCallException
        │       │        └── BadMethodCallException
        │       ├── DomainException
        │       ├── InvalidArgumentException
        │       ├── LengthException
        │       └── OutOfRangeException
        ├── PharException
        ├── ReflectionException
        ├── RuntimeException
        │       ├── OutOfBoundsException
        │       ├── OverflowException
        │       ├── RangeException
        │       ├── UnderflowException
        │       ├── UnexpectedValueException
        │       └── PDOException
        ├── SodiumException
        ├── FiberExit
        ├── Random\RandomException
        ├── SQLite3Exception
        ├── DateException
        │       ├── DateInvalidTimeZoneException
        │       ├── DateInvalidOperationException
        │       ├── DateMalformedStringException
        │       ├── DateMalformedIntervalStringException
        │       └── DateMalformedPeriodStringException
        └── RequestParseBodyException
```

php에는 아주 많은 문법과 기능들이 있지만, 그 수에 비해 예외나 에러의 수는 비교적 적다. 이는 예외가 아주 신중하게 정의되며, 무분별하게 정의하는 것은 아니며, 에러가 예외가 나오는 상황은 가능한 피할 것을 권장하는 압력이 있는 것이다.

## 자바의 예외 처리

자바의 에러처리는 컴파일 타임에서 잘못된 구문과 문법을 사용했을 때 알려 주는 컴파일 에러와 예외가 발생했을 때 try-catch를 반드시 사용해야 하는 checked 예외와, 예외가 발생하면 try-catch 처리를 하든 해당 예외가 발생하지 않도록 하는 unchecked 예외가 존재한다.

### checked 예외

런타임에서 예외가 발생해도 적절한 예외 처리가 반드시 구비되기 때문에 예외로 인해 로직의 처리가 중단되지 않는 에러이다. 예외가 발생하면 try...catch 문으로 잡아서 처리를 하기 때문에 런타임에서 예외로 인해서 코드의 실행이 중단되지 않으며, 개발자가 의도한 예외 처리 로직으로 처리된다. unchecked 예외는 최상위 예외 Exception의 상속을 받지만 RuntimeException과 그 하위 타입이 아닌 모든 예외가 반드시 try...catch로 처리해야 할 예외이며, 만약 예외 처리를 하지 않을 시 컴파일 에러가 발생하는 에러이다.

### unchecked 에러

컴파일러가 예외 처리를 강제하는 checked 예외와 달리 예외 처리를 강제하지 않는다. 적절한 사전 조건의 부재, 의도와 다른 객체의 메소드 조작, 잘못된 코드 사용 등에 의해 발생하는 사용자의 실수를 정정하기 해 남기는 에러로, 개발자는 필요에 따라 사전 조건을 잘 설정하여 예외가 발생하지 않게 하거나, 개발자의 판단에 따라 예외 처리하도록 하는 예외이다. 실제 서버에서 예외가 발생했을 때 에러 처리가 없다면 적절한 에러 처리 또는 적절한 사전 조건을 부여해서 처리하도록 한다.

### checked 예외의 문제

checked 에러는 적절한 사전조건을 설정하면 발생하지 않기 때문에, 반드시 이에 대한 예외를 처리해야 하는지에 대한 논란이 있다. 혹시라도 이런 문제가 발생했을 때, 예외처리를 어떻게 할까? 적절한 예외 처리 방법이 있을까? 사실상 억지로 예외를 처리하는 것이라고 볼 수 있다. 사실상 이런 예외가 발생하면, 통지나 로그를 하는 로직을 추가하는 것이 흔히 생각할 수 있는 처리 방식이고 다른 특별한 처리 방법을 떠올리기 어렵고 적절한 사전 조건을 설정하는 것을 통해서만 제대로 된 해결을 할 수 있다.

보통 던져진 예외가 잡히지 않으면 언어나 프레임워크의 최종적인 에러 처리 위치까지 예외가 도달하게 되고 종료된다. 종료되기 전의 처리에서 sentry 같은 서비스를 통해서 로그와 통지를 맡기는 경우도 있다. 보통은 모든 예외에 대한 로그나 통지 로직을 갖추는데 사전 조건을 잘 세팅해 주어야 하는 경우 이를 통해서 무엇이 잘못되었는지 확인하고 코드나 환경을 잘 설정해 주면 된다. 그래서 checked 에러로 굳이 처리할 필요가 없는데 반드시 try...catch를 구비하는 checked가 필요할까라는 의문점이 있을 수 밖에 없다.

php는 checked 예외가 존재하지 않으며, 현대화된 자바라는 코틀린 언어도 checked 예외가 존재하지 않는다. 하지만 잘못된 에러처리로 인해 대량의 리퀘스트가 대량의 예외를 생성할 때 예외 처리가 없다면, 무수한 스텍트레이스의 생성으로 인해서 시스템 리소스가 부족해 애플리케이션 서버가 터질 수 있다는 문제가 있다.

### unchecked 예외

## Reference
- https://php.watch/articles/PHP-exception-hierarchy
