## composer.json의 위치
- composer.json은 프로젝트 폴더 어디에 위치해도 상관 없다.
- Packagist.org에 패키지를 게시하려면 VCS (같은 버전 관리 시스템 : git과 같은) 폴더 상위 폴더에 composer.json이 위치해야 한다.


## 의존성 패키지 설치
- composer.json 파일에 패키지명과 패키지 버전을 다음과 같은 형식으로 적어준다.
```
{
    "require": {
        "monolog/monolog": "2.0.*"
    }
}
```
- `"monolog/monolog"` : 패키지명(package names)
- `"2.0.*"` : 버전 제약 조건(version constraints)
- `composer install 패키지명@버전제약조건` 방식으로 의존성 패키지를 설치할 수도 있지만 `composer.json` 파일에 


## 패키지 이름
- vendor name과 project's name으로 구성되어 있다. `"monolog/monolog"에서 / 앞이 벤더 이름, 뒤가 프로젝트의 이름이다.
- 벤더이름은 공급자 별로 유일하게 존재한다. 동일명의 패키지를 만들더라도 공급자에 따라 `igorw/json`, `seldaek/json`으로 명명된다.
- 컴포저로 인스톨할 수 있는 패키지가 되기 위해서는 Packagist.org에 등록을 해야 하고 이때 등록할 때 자신의 고유한 벤더명을 지정해야한다.

## 패키지 버전 제약
- ` 2.0.*`는 2.0 개발 분기의 모든 버전 또는 2.0 이상 2.1 미만( 2.0 <= 2.* < 2.1)의 모든 버전을 의미합니다.

## 종속성 설치 (Installing dependencies)
- 종속성 설치란 지정된 버전의 패키지를 기록하는 것을 의미한다. composer.json에서 버전을 느슨하게 정의했다면, 종속성 설치를 하면 composer.lock 파일이 생기고 설치된 버전의 대상을 정확하게 지정한다.
- 종속성을 처음 설치할 때는 다음 명령어를 사용한다.
```
php composer.phar update
```
- 현재 설치된 패키지 버전을 바탕으로 composer.lock 파일을 생성한다.

### 종속성 설치를 하는 이유
- 동일 프로젝트에서 작업하는 사람들이 정확히 같은 패키지를 사용하게 하기 위해서 쓴다. (조금이라도 다른 버전 차이에 의해 발생할 수 있는 버그를 미연에 차단하여 최대한 버그의 가능성을 완화하기 위한 역할이다.)
- 이를 위해서 프로젝트 리포지토리에 composer.lock 파일을 커밋해야 한다.

### 주의 할 점
- 종속성 패키지가 설치되는 vendor 폴더는 git등에 의한 버전관리를 하지 않는다.
- vendor 폴더가 버전관리 되지 않기 때문에 프로젝트를 클론해서 설치하는 경우 composer.lock (또는 lock 파일이 없다면 composer.json)의 패키지 버전으로 설치한다.

## Reference
- https://getcomposer.org/doc/01-basic-usage.md
