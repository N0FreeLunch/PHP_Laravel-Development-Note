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

## Reference
- https://php.watch/articles/PHP-exception-hierarchy
