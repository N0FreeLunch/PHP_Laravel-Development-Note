## CGI로 발생하는 PHP의 문제

PHP는 기본적으로 CGI 환경에서 실행된다. CGI 방식은 리퀘스트가 요청 되었을 때 PHP 파서 프로세스를 실행하여 코드를 실행하는 것을 의미한다. 요청당 프로세스의 실행이기 때문에 한 번에 많은 PHP 코드를 실행하게 되면 코드를 메모리에 올리는 과정에 시간이 들기 때문에 느려지게 된다. PHP의 속도를 빠르게 하기 위해서는 하나의 프로세스가 여러 리퀘스트를 처리하게 CGI 방식을 사용하지 않는 방식을 쓰거나, CGI를 사용할 때는 PHP 코드를 가능한 적게 실행되도록 만들 필요가 있다. 하지만 PHP는 속도 보다는 빠르게 비즈니스 문제를 해결하기 위한 용도로 사용하는 언어이다. 속도도 중요하지만, 이해하기 쉽고, 관심사가 잘 분리 되어 있어서 변경하기 쉬운 코드가 되어야 한다. 따라서 코드를 적게 쓰기 보다는 코드양이 늘어나더라도 여러 파일에 잘 나눠진 코드를 쓰는 편이 낫다. 그런데 이렇게 되면 하나의 리퀘스트에 너무 많은 코드를 실행한다는 문제점이 존재한다. 그래서 코드의 실행을 최소화 하기 위해서 처리에 필요한 파일만을 로딩하는 방식을 사용한다.

## 파일 가져오기는 컴파일 타임에 실행된다.

> Importing is performed at compile-time, and so does not affect dynamic class, function or constant names. <sup>[doc](https://www.php.net/manual/en/language.namespaces.importing.php#example-300)</sup>

파일에 대한 가져오기가 컴파일 타임에 이뤄진다는 것은, 네임스페이스로 연결된 모든 파일을 컴파일 타임에 사전 로딩한다는 것을 의미한다. 하지만 코드의 실행은 런타임에 이뤄지기 때문에 소스코드를 사전 컴파일한다고 해서 소스 코드를 실행하는 것은 아니다. 네임스페이스가 정의된 모든 파일을 사전 컴파일 하지만, 실제 코드의 실행은 런타임에 이뤄지고 런타임에 실행되지 않으면 컴파일 타임에 파일 데이터를 불러왔더라도 해당 파일의 소스코드를 런타임 동작으로 실행하지는 않는다는 의미를 가진다.

## 하나의 파일에는 하나의 심볼만 갖는 이유

심볼이란 함수, 클래스, 추상클래스, 인터페이스, 이넘 등의 한 단위의 문법을 뜻한다. 대부분의 언어에서 하나의 파일에 하나의 심볼을 가지는 코딩 스타일을 좋은 것으로 여긴다. php에서는 PSR-4에서 엄격하게 하나의 파일은 하나의 심볼을 가진다고 명시해 두었다. 이런 이유가 있는데, 만약 하나의 파일에서 여러 심볼이 export 된다고 하자. 여러개의 export 중에서 하나의 심볼만을 이용하고 싶은데, CGI 환경에서 실행되는 php는 하나의 심볼을 가져오기 위해서 해당 심볼이 들어 있는 파일 전체를 읽어야 한다. 곧, 사용하지 않을 코드가 들어 있는 부분을 사용할 코드와 같은 파일에 있다는 이유로 읽어야 하는 것이다. 이는 php의 실행속도를 느리게 하므로 export 되는 php의 경우 하나의 심볼만을 가지도록 코딩 표준으로 정한 것이다.

## PSR 살펴보기

### PSR-1

#### 사이드 이펙트

> Files SHOULD either declare symbols (classes, functions, constants, etc.) or cause side-effects (e.g. generate output, change .ini settings, etc.) but SHOULD NOT do both.

'파일들은 심볼을 선언하거나 사이드이펙트를 발생시키는 것 둘 중 하나여야 한다.'라는 부분이 있다. 어떤 함수나 클래스를 하나의 파일에 선언하고 이를 사용한 코드를 많은 예제에서 다루지만, 프로덕션에서 사용하는 프로젝트에서는 테스트 코드를 작성하는 것과 같은 특별한 경우를 제외하고는 하나의 파일에 정의 및 사용을 하지 않고, 별도의 파일로 분리해서 사용하는 것이 표준적인 방법이다.

> A file SHOULD declare new symbols (classes, functions, constants, etc.) and cause no other side effects, or it SHOULD execute logic with side effects, but SHOULD NOT do both.

하나의 파일에는 심볼들만 있고 사이드 이펙트는 존재하지 않아야 하며 또는 사이드 이펙트가 있는 로직을 실행해야 한다. 둘 모두를 사용해서는 안 된다.

> The phrase "side effects" means execution of logic not directly related to declaring classes, functions, constants, etc., merely from including the file.

사이드 이펙트라는 것은 클래스, 함수, 상수 등을 선언하는 것과 직접적으로 연관되지 않은 것으로 그저 파일에 포함되어 있는 것이다.

> "Side effects" include but are not limited to: generating output, explicit use of require or include, connecting to external services, modifying ini settings, emitting errors or exceptions, modifying global or static variables, reading from or writing to a file, and so on.

사이드 이펙트는 다음을 포함하지만 제한되지 않는다. 결과를 생성하는 것, 명시적인 require 또는 include의 사용, 외부 서비스 연결, ini 설정 변경, 오류 또는 예외 발생, 전역 변수 또는 정적 변수 변경, 파일의 읽기 또는 쓰기 등이 있다.

> The following is an example of a file with both declarations and side effects; i.e, an example of what to avoid:

다음 예제는 하나의 파일에 선언과 사이드이펙트 모두를 가진 예제이다. 이 예는 피해야 할 대상이다.

```php
// side effect: change ini settings
ini_set('error_reporting', E_ALL);

// side effect: loads a file
include "file.php";

// side effect: generates output
echo "<html>\n";

// declaration
function foo()
{
    // function body
}
```

## 네임스페이스
- PHP는 네임스페이스를 사용하여 클래스, 인터페이스, 함수 및 상수를 네임스페이스로 그룹화할 수 있다. PHP 오토로드 함수(spl_autoload_register)를 사용하면, 네임스페이스와 경로가 매핑되어 파일들을 자동으로 로드할 수 있다.
- PSR-4 표준에 따르면, 네임스페이스와 파일 시스템 경로는 일치해야 하며, 각 클래스는 해당 네임스페이스와 경로에 맞는 파일에 정의되어야 한다.
- 하나의 파일에는 하나의 클래스를 정의하는 것이 일반적이며, 파일 이름은 클래스 이름과 일치해야 한다. 클래스 정의는 파일 내 어디에나 위치할 수 있다. 그러나 파일 구조를 깔끔하게 유지하는 것이 권장된다.

## 한 파일안에 여러 클래스

네임스페이스를 사용하지 않는 php 파일의 경우 한 파일에 여러 클래스를 쓰는 것은 일반적이다. 네임스페이스를 쓰게 되면 다른 파일에서 네임스페이스로 부를 수 있는 클래스는 하나이다. 따라서 네임스페이스를 쓰는 php 파일 안에 여러 클래스가 들어 있는 경우는 다른 파일에서 네임스페이스로 불러오는 대상이 되는 클래스 내부에서 사용할 클래스를 추가할 때 사용한다.

## php 문법의 한계

php는 클래스의 단위로 상수(`const`), `final`, 멤버 변수의 타입 설정(typed memeber variable), 접근 제어자(access modifier) 등을 사용할 수 있지만, 다른 로컬 스코프에서 이들 키워드를 사용할 수 없다. 전역 상수로 `const`는 지원하지만 함수, 메소드 등과 같은 로컬 스코프에서는 사용할 수 없다는 단점이 있다.

## PSR 살펴보기
- PSR-1의 `Namespaces and classes MUST follow an "autoloading" PSR: [PSR-0, PSR-4].`라는 설명에 따라 클래스와 네임스페이스는 반드시 `PSR: [PSR-0, PSR-4]`를 따라 오토로딩 될 수 있어야 한다.
- PSR-4의 `The terminating class name corresponds to a file name ending in .php. The file name MUST match the case of the terminating class name.`으로 use로 사용하는 '네임/스페이스/클래스'에서의 클래스 부분은 php 파일과 이름이 동일해야 한다.
- PSR-4의 `A contiguous series of one or more leading namespace and sub-namespace names, not including the leading namespace separator, in the fully qualified class name (a "namespace prefix") corresponds to at least one "base directory".`에 따라서 네임스페이스는 폴더 경로와 일치해야 한다.
- 아래의 예제에서 PSR-4를 만족하려면 하나의 파일에 두 네임 스페이스가 존재한다. 하나의 파일은 하나의 폴더에 속하기 때문에 네임스페이스가 폴더 경로와 일치해야 한다는 PSR-4를 만족하지 않는다.
```php
// DefineClass.php

namespace UseClass {

    use DefineDto\Dto as AliasDto;

    $fn = function($a, $b, AliasDto $cdObj) {
        var_dump($a);
        var_dump($b);
        var_dump($cdObj->c);
        var_dump($cdObj->d);
    };

    $fn(1, 2, new AliasDto(3, 4));
}

namespace DefineDto {

    class Dto
    {
        public function __construct(
            public int $c,
            public int $d,
        ) {
        }
    }
}
```

## References
- https://wiki.php.net/rfc/nested_classes
- https://externals.io/message/126589
