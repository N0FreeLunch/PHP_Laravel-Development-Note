## object type 도입

```
<?php

function test(object $obj) : object
{
    return new SplQueue();
}

test(new StdClass());
```
- PHP 7.2 부터 새로운 타입 키워드인 object가 도입 되었다. (7.2 이전에 object 타입 자체가 있었는지 없었는지 타입 키워드만 새로 생긴 것인지 확인이 필요.)
- 이 타입힌트는 어떤 오브젝트에도 사용할 수 있으며, 
- 이 타입 키워드는 매개 변수 및 함수의 반환값의 타입힌트로 지정할 수 있다.
- 반공변성(contravariant) 파라미터 타입과 공변성(covariant) 리턴 타입을 지정할 때 사용할 수 있다.
- 공변성 : 자신과 상위 객체를 의미한다.
- 반공변성 : 자신과 하위 객체를 의미한다.

## Abstract method overriding
```
<?php

abstract class A
{
    abstract function test(string $s);
}
abstract class B extends A
{
    // overridden - still maintaining contravariance for parameters and covariance for return
    abstract function test($s) : int;
}
```
- 이제 추상 클래스가 다른 추상 클래스를 확장할 때 추상 메서드를 재정의할 수 있습니다.

## Password hashing with Argon2
- PHP의 기본 단방향 암호화 방식은 bcrypt이다.
- 단방향 암호화 방식은 주로 비밀번호를 해싱할 때 사용한다.
- bcrypt 이외의 새로운 단방향 암호화 방식인 Argon2가 PHP API에 추가 되었다. 
```
PASSWORD_ARGON2I
PASSWORD_ARGON2_DEFAULT_MEMORY_COST
PASSWORD_ARGON2_DEFAULT_TIME_COST
PASSWORD_ARGON2_DEFAULT_THREADS
```

## Extended string types for PDO
- PDO's string type has been extended to support the national character type when emulating prepares. This has been done with the following constants:
- 애뮬레이션 준비 시 국가별 문자 유형을 지원하도록 PDO의 문자열 유형이 확장되었습니다.
```
PDO::PARAM_STR_NATL
PDO::PARAM_STR_CHAR
PDO::ATTR_DEFAULT_STR_PARAM
```
### PDO에서의 사용 예
```
<?php

$db->quote('über', PDO::PARAM_STR | PDO::PARAM_STR_NATL);
```
- 옵션을 비트 값으로 사용한다.
- 비트 연산자를 사용해서 옵션을 조합할 수 있도록 구성되어 있다. 

### Additional emulated prepares debugging information for PDO 
- ??

## 매개변수 유형 확장
```
<?php

interface A
{
    public function Test(array $input);
}

class B implements A
{
    public function Test($input){} // type omitted for $input
}
```
- 인터페이스를 구현할 때 인터페이스에 지정한 매개변수의 타입힌트를 구현부에서는 생략할 수 있다.
- 비록 구현부에서 타입힌트가 없지만 지정된 유형의 타입으로 지정된다.
- 매개변수 유형이 반공변이기 때문에 매개변수에 클래스 타입이나 오브젝트 타입을 사용해도 여전히 LSP(리스코프 치환 원칙)를 준수한다.

## 그룹화된 네임스페이스에 대해 후행 쉼표 허용
```
<?php

use Foo\Bar\{
    Foo,
    Bar,
    Baz,
};
```

## Extension loading by name
## Sodium is now a core extension 
## Support for extended operations in LDAP
## Address Information additions to the Sockets extension
## proc_nice() support on Windows
## pack() and unpack() endian support
## Enhancements to the EXIF extension
## New features in PCRE
## SQLite3 allows writing BLOBs
## Oracle OCI8 Transparent Application Failover Callbacks
## Enhancements to the ZIP extension
