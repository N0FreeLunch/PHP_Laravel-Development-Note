# Laravel Sanctum

## Sanctum이 제공하는 기능 두 가지
- 싱글 페이지 어플리케이션을 위한 세션 인증
- 모바일 어플리케이션을 위한 토큰 기반의 API 인증
> 필요에 따라 두 기능 중 하나만 사용하는 것을 권한다.


## the motive of sanctum
- 깃 허브 access token을 모티브로 만들어 짐

### GitHub access token의 특징
- 매우 긴 만료기간을 가진 토큰
- 사용자에 의해 언제든지 해지(revoke) 가능


## 라라벨 Sanctum API의 특징
### 중앙 집중적인 톤큰 관리
- 단일 데이터베이스 테이블에 사용자의 API 토큰을 저장하여 여러 개의 웹 어플리케이션 서버가 돌아가는 상황에서도 토큰을 중앙 집중형으로 관리할 수 있다.

## the reason of using sanctum
- SPA 인증을 하기 위해서 기존에는 jwt 프로토콜을 구현한 JWT-auth나 OAUTH를 사용해야 했다. OAUTH는 복잡한 인증 토큰이기 때문에 편리하게 인증하기 힘든 단점이 있다.

## 라라벨 Sanctum 세션 인증의 특징
- API 토큰 인증을 사용하지 않는다. (토큰을 통해서 인증을 할 경우 세션 인증 방식이 무효화 된다.)
- 라라벨 내장 쿠키 기반의 세션 인증 서비스를 사용한다.

## Sanctum 세션 인증의 보안 
- API 인증의 토큰기반이 아닌 세션 인증의 보안에 대한 설명이다.

### CSRF 보호
- CSRF는 요청 위조 기법이다. 보통은 쿠키 세션에 저장된 인증 세션을 탈취하여 서비스 제공사가 아닌 다른 서비스에서 위조 요청을 하는 해킹 방법이다.
- 라라벨의 셍텀은 인증 세션 뿐만 아니라 도메인 기반 확인을 하기 때문에 CSRF 위조에 좀 더 강력하다.

### session 인증
- 라라벨 내장 세션 인증을 통해 강력한 기본 보안 기능 제공

### XSS 방어

## Install
```
composer require laravel/sanctum
```

## Config
### 토큰 기반의 셍텀 설치
- 토큰을 저장하고 관리하기 위한 테이블을 만드는 것.
```
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```
```
php artisan migrate
```

### SPA용 인증 설정
- 토큰 기반인 마이그레이션 설정을 할 필요가 없다. 대신 마이그레이션 설정을 무시한다는 세팅을 해 줘야 한다.
- app\Providers\AppServiceProvider.php
```
use Laravel\Sanctum\Sanctum;

// ...

public function register()
    {
        Sanctum::ignoreMigrations();
    }
```
- app/Http/Kernel.php
```
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

'api' => [
    EnsureFrontendRequestsAreStateful::class,
    'throttle:60,1',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```

### sanctum 마이그레이션만 적용
- `--provider="Laravel\Sanctum\SanctumServiceProvider"` 명령이 토큰 인증을 위한 마이그레이션 뿐만 아니라 파일 구조 세팅을 의미한다면
- `--tag=sanctum-migrations` 명령은 디비 마이그레이션에 관한 명령만을 담고 있다.
```
php artisan vendor:publish --tag=sanctum-migrations
```


## API 토큰 발행 방식
### API 토큰 인증 방식 종류
- API 토큰을 발행하여 인증 
- 개인 액세스 토큰을 발행하여 인증

### http(S) 요청 시 필요한 항목
```
Authorization : Bearer 토큰
```
- 위와 같은 방식으로 http(s) 요청 시 헤더와 토큰을 붙여서 서버에 전송한다.
- 보통은 ajax 요청의 헤더 값을 세팅할 때, 위와 같이 키 벨류를 작성한다.
- 'Bearer 토큰' 한 단어가 아니라 `Bearer `문자에 토큰 값을 붙여서 Bearer를 포함해서 Authorization의 값을 설정해야 한다.
- 이런 토큰 전송 때 붙이는 값은 Bearer 뿐만 아니라 여러가지 존재한다.

### 토큰 발급하기
```
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
```
- Notifiable은 User 모델에 기본적으로 들어가 있는 것. 패스워드 재설정 기능을 사용하기 위해서는 Illuminate\Notifications\Notifiable 트레이트가 있어야 한다. 하지만 토큰 기능과는 상관이 없는 부분이다.
- 토큰 기능을 사용하기 위해서는 HasApiTokens 트레이트를 추가해 줘야 한다.

### 토큰 발행
#### 토큰 저장 방식
- SHA-256 해싱을 이용해서 해싱한 값을 저장
- SHA-256 해싱된 값은 복호화가 불가능하다.
- 해싱 된 값이 저장되기 때문에 해싱된 비밀번호가 노출 되어도 사용자의 실제 토큰 값은 보호된다.

#### 토큰 생성
```
$token = $user->createToken('token-name');
```
- createToken는 Laravel\Sanctum\NewAccessToken 인스턴스를 반환

#### 평문 엑세스
- Sanctum\NewAccessToken 인스턴스로 접근을 한다.
- 토큰 저장은 해시화 되기 때문에 복호화가 불가능하다.
- 따라서 가장 처음 토큰을 만들 때, 토큰 값이 무엇인지 값을 반환 해 줘야 한다. 그리고 이 원본 토큰을 해시해서 유저 테이블에 저장한다.
```
$token->plainTextToken;
```

#### 토큰 값 접근 
- 토큰은 user 테이블에 저장하므로 user 모델을 사용해서 접근한다.
- 엘리퀀트 모델 클래스에 HasApiTokens이라는 트레이트를 집어 넣은 것으로 인해서 엘리퀀트 모델의 멤버 변수로 token 값을 저장한다.
```
$user -> tokens
```


```
foreach ($user->tokens as $token) {
}
```


## Reference
https://laravel.kr/docs/8.x/sanctum
