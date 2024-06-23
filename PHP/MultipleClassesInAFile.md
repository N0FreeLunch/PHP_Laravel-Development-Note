## 네임스페이스
- PHP는 네임스페이스를 사용하여 클래스, 인터페이스, 함수 및 상수를 네임스페이스로 그룹화할 수 있다. PHP 오토로드 함수(spl_autoload_register)를 사용하면, 네임스페이스와 경로가 매핑되어 파일들을 자동으로 로드할 수 있다.
- PSR-4 표준에 따르면, 네임스페이스와 파일 시스템 경로는 일치해야 하며, 각 클래스는 해당 네임스페이스와 경로에 맞는 파일에 정의되어야 한다.
- 하나의 파일에는 하나의 클래스를 정의하는 것이 일반적이며, 파일 이름은 클래스 이름과 일치해야 한다. 클래스 정의는 파일 내 어디에나 위치할 수 있다. 그러나 파일 구조를 깔끔하게 유지하는 것이 권장된다.

## 한 파일안에 여러 클래스
- 네임스페이스를 사용하지 않는 php 파일의 경우 한 파일에 여러 클래스를 쓰는 것은 일반적이다. 네임스페이스를 쓰게 되면 다른 파일에서 네임스페이스로 부를 수 있는 클래스는 하나이다. 따라서 네임스페이스를 쓰는 php 파일 안에 여러 클래스가 들어 있는 경우는 다른 파일에서 네임스페이스로 불러오는 대상이 되는 클래스 내부에서 사용할 클래스를 추가할 때 사용한다.

## php 문법의 한계
- php는 클래스의 단위로 상수(`const`), `final`, 멤버 변수의 타입 설정(typed memeber variable), 접근 제어자(access modifier) 등을 사용할 수 있지만, 다른 코컬 스코프에서 이들 키워드를 사용할 수 없다. 전역 상수로 `const`는 지원하지만 함수, 메소드 등과 같은 로컬 스코프에서는 사용할 수 없다는 단점이 있다.

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
