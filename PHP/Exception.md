## 예외처리
- 모든 로직을 만들 때 일어날 수 있는 모든 경우의 수를 파악하고 그에 따른 처리를 할 수 있도록 만들면 어떠한 에러도 발생하지 않을 것이다. 어떤 로직이나 코드에 관해서는 모든 경우의 수를 파악할 수 없고 그로 인해서 미리 구비해 둔 로직으로는 처리할 수 없는 경우가 생긴다.
- 대부분의 프로그램은 미리 준비한 로직으로 처리를 할 수 없는 경우가 발생했을 때 예외 또는 에러를 발생시키며 해당 처리과정을 중단 시킨다. 이 때 프로그래머는 로그를 남기고 로그가 남겨졌다면, 에러나 예외 케이스에 대한 처리를 추가하여 해당 케이스에 대한 에러나 예외가 더 이상 발생하지 않도록 처리할 수 있다.

### 커스텀 예외
- 어떤 로직을 처리할 때 이러한 경우의 값이 전달되는 경우, 해당 로직에서는 충분한 처리 방안이나 로직이 갖춰져 있지 않기 때문에 더 이상 로직을 해당 맥락에서 처리하지 않고, 별도의 로직으로 전달하여 별개의 처리를 하는 방식을 사용할 때 커스텀 에러를 발생시킨다.
- 어떤 처리를 함에 있어서 로직을 처리하는 목적이나 맥락과는 다른 처리를 해야 할 때, 사용자 정의 예외를 발생 시켜서 별도의 로직에서 처리를 할 수 있다. 적절하게 예외를 사용함으로써 로직의 목적과 맥락에 맞지 않는 처리를 별도 뽑아내어 처리를 할 수 있으므로, 도메인 중심적인 로직을 나타내는데 불필요한 처리 과정을 함께 기술하지 않는 것으로 굉장히 깔끔한 로직을 만들 수 있다.

### 제공되는 커스텀 예외
- 프로그래밍 언어나 라이브러리에서 어떤 처리를 하는데 어떤 케이스가 발생했을 때, 언어나 라이브러리 내부에서 이러한 문제를 처리하지 않고 사용자에게 직접 에러를 처리하도록하기 위해서 일부러 커스텀 예외를 만들어 둔다.
- 물론 처리를 하는데 문제가 발생하면, 예외를 발생시키지 않고, 객체의 인터페이스나 센티널(`null` `false` 반환과 같은 구분값) 반환값을 사용할 수도 있을 것이다. 예외를 발생 시키면 일단 처리가 멈추기 때문에 사용자는 문제가 발생한 케이스에 대해 반드시 예외에 대한 처리를 해야하지만, 예외가 발생하지 않고 결과를 객체의 인터페이스 또는 함수의 반환값으로 주는 경우에는 이들 값에 대한 별도의 처리를 하지 않으면 처리가 멈추지 않기 때문에 문제가 발생했지만 문제가 발생하지 않았을 때의 로직을 그대로 실행할 가능성이 생길 수 있다. 물론 이런 경우 원하는 결과 값을 얻을 수 없기 때문에 대부분 실행 처리 과정에서 뭔가 에러가 발생하여 그 원인을 추적하겠지만 때때로 로직의 실행이 완료되어 문제가 발생했을 때 바로 알아채지 못하고 한참 뒤에 운용자 또는 서비스 사용자의 보고를 통해서 알게 되는 경우가 생길 수 있다. 이러한 문제를 방지하기 위해서 문제가 발생했을 때 일부러 커스텀 에러를 정의하여 반환해 실행을 멈추도록 한다.
- 프로그래밍 언어의 디폴트로 내장된 예외 객체 라이브러리에서 정의한 예외 객체를 발생시키면 이 에러 객체는 예외가 발생하는 코드를 감싼 try-catch 문을 통해서 해당 에러를 포착할 수 있다. 물론 try-catch 문을 사용하지 않으면 예외가 발생했으므로 프로그래밍 언어에 의해 에러가 포착이 되어 멈추거나, 라이브러리에 의해 에러가 포착되어 멈추게 되고, 예외가 발생했을 때 별도의 처리를 해 줘야 한다면 try-catch로 예외를 잡아서 처리를 해 주면 된다.

### 예외의 구현
- 예외를 사용할 때는 인터페이스로 php언어에서 제공하는 [`Throwable`](https://www.php.net/manual/en/class.throwable.php) 인터페이스를 사용하고, 클래스로 php 언어에서 제공하는 [`Exception`](https://www.php.net/manual/en/class.exception.php) 클래스를 사용한다.
- 예외는 프로그래밍 언어에 기본적으로 내장된 예외 객체를 상속하여 만들어지며, `Throwable` 인터페이스로 직접 예외 객체를 구현하여 만들 수 없다. `Throwable`이란 `throw`라는 문법을 통해서 던질 수 있다는 의미를 갖고 있으며, 모든 php의 예외 클래스는 php 언어의 기본 `Exception` 클래스를 상속 받아 만들어지고 php 기본 `Exception`은 `Throwable` 인터페이스를 구현하고 있기 때문에 모든 예외 클래스는 자동으로 `Throwable` 인터페이스에 부합한다. 따라서 모든 예외 객체는 `throw` 문법을 통해서 던질 수 있다.

### 예외의 캐치
- 예외는 예외를 던진 부분을 실행하는 코드에서 실행된다. A라는 코드가 B를 실행하고, B라는 코드가 C를 실행하고, C라는 코드에서 예외를 던지게 되면, C의 예외 발생한 부분을 감싸는 `catch` 문이 있다면 `catch`문에서 예외를 잡고, C를 감싸는 `catch` 문이 없다면, B라는 코드를 감싸는 `catch` 문이 있는지 확인하고 있다면 예외를 잡고, 없다면 A라는 코드를 감싸는 `catch`문이 있는지 확인을 하고 있다면 예외를 잡고, 없다면 php의 전역 예외 처리기에 예외를 던지고 예외를 처리하지 못하면, php 프로세스는 종료가 된다.
- 예외는 예외를 실행하는 코드를 둘러싼 부분에서 코드를 처리한다. A, B, C를 둘러싼 어디서 코드를 처리한다는 것이다. 하지만 발생한 에외를 처리하는 곳이 A, B, C 중의 한 곳을 선택한다고 처리 로직의 맥락과 흐름에 맞는가를 생각해 봐야 한다. 아키텍처적으로 C에서 발생한 에러를 B에서 처리하도록 하면 좋은 구조를 특별히 만들지 않는한, 예외를 처리할 부분으로 적당한 곳을 선정하기 어려운 문제가 있다.

#### throwable 인터페이스로 케치
```php
try {
} catch(Throwable $e) {
}
```
- 모든 예외는 php의 `Throwable` 인터페이스의 구현인 내장 `Exception` 객체를 통해서 상속을 받아 만들어지기 때문에 `Throwable` 인터페이스는 모든 예외를 케치할 수 있다.
- 여러 인터페이스 유형을 상속 받는 관계를 생각해 보자. `Throwable` 인터페이스는 모든 예외 객체가 갖고 있는 값이지만, 어떤 예외 객체는 다른 유형을 함께 구현했다고 하자. 그럼 해당 인터페이스만 구현된 예외 객체의 그룹을 선택적으로 catch 할 수 있다. php는 다중상속을 지원하지 않지만, 인터페이스를 통해서 상위-하위 관계가 아닌 여러 타입을 가질 수 있다. 상위-하위 관계는 아니지만 특정 유형의 예외를 그룹화 하고 싶을 때 인터페이스를 이용한 예외를 만들어 유형별로 케치를 할 수 있다.
```php
try {
} catch(CustomAddtionalExceptionInterface $e) {
}
```

#### 예외 객체로 캐치
```php
try {
} catch(Exception $e) {
}
```
- `Throwable` 인터페이스를 사용하지 않더라도 php의 디폴트 내장 `Exception` 객체를 사용하여 모든 객체를 잡을 수 있다.
- `Exception $e`에서 `Exception` 부분은 케치하는 대상의 타입을 지정하는 부분이다. 타입힌트와 마찬가지로 자신의 하위 타입을 모두 받을 수 있는 특징을 갖는다.
- 하위 타입은 상위 타입의 인터페이스를 포함한다. 하위 타입에서는 상위 타입이 가지지 못한 인터페이스를 추가할 수 있다. 컴파일 언어의 경우, `Exception`으로 하위 타입의 예외 객체를 받으면 추가된 인터페이스에 접근 불가능한 특성이 있으나, php와 같은 동적 언어는 `Exception` 타입으로 캐치를 할 때 하위 타입에 접근할 수 있다.
- `catch` 구문에서 상위 타입을 지정하게 되면, 하위 타입은 모두 받게 되고 하나의 try-catch 문에서 catch를 여러번 사용했을 때 먼저 실행 된 `catch` 문에서 예외를 포착했다면, 다음으로 실행된 `catch` 문에서 동일한 타입의 예외를 캐치할 수 없다.
```php
try {
} catch(Exception1 $e) {
} catch(Exception2 $e) {
} catch(Exception3 $e) {
} 
```
- 위의 코드에서 `catch` 문은 단 한 곳에서만 실행이 된다. `Exception1`에서 예외를 잡았다면 다음 catch 문에서 잡을 수 없고, `Exception2`에서 예외를 잡았다면 다음 `catch` 문에서 잡을 수 없다.

#### 커스텀 예외 캐치
```php
class CustomException extends Exception
{
    public function __toString() {
        return "Custom Exception";
    }
}

class CustomException2 extends Exception
{
    public function __toString() {
        return "Custom Exception2";
    }
}

try {
    throw new CustomException();
    echo "try completed\n";
} catch(Exception $e) {
    echo "all Exception catch\n";
} catch(CustomException $e) {
    echo "custom Exception catch\n";
} finally {
    echo "finally is always run\n";
}

echo PHP_EOL;

try {
    throw new CustomException2();
    echo "try completed\n";
} catch(CustomException $e) {
    echo "custom Exception catch\n";
} catch(CustomException2 $e) {
    echo "custom Exception2 catch\n";
} finally {
    echo "finally is always run\n";
}
```
- 위의 예제는 커스텀 예외 클래스를 만들고 이를 `catch`하는 코드이다.
```php
catch(Exception $e) {
    echo "all Exception catch\n";
} catch(CustomException $e) {
    echo "custom Exception catch\n";
}
```
- 위 코드는 상위 타입 `Exception`에서 하위 타입 `CustomException`을 받기 때문에 `catch(Exception $e) { echo "all Exception catch\n"; }` 부분이 실행되며, `catch(CustomException $e) { echo "custom Exception catch\n"; }` 부분이 예외를 케치 할 수 있는 부분임에도 불구하고 앞선 `catch` 문이 먼저 예외를 케치하여 실행되지 않는다.
- `throw new CustomException2();`로 예외를 던진 케이스를 보면, `catch(CustomException $e) { echo "custom Exception catch\n"; }`에서는 타입 불일치로 예외를 잡지 않고, `catch(CustomException2 $e) { echo "custom Exception2 catch\n"; }`에서는 예외를 잡는다.

## Reference
- https://www.php.net/manual/en/language.exceptions.php
