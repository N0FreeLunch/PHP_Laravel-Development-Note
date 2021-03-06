# Laravel Sanctum

## Sanctum이 제공하는 기능 두 가지
- 싱글 페이지 어플리케이션을 위한 세션 인증
- 모바일 어플리케이션을 위한 토큰 기반의 API 인증
> 필요에 따라 두 기능 중 하나만 사용하는 것을 권한다.

---

## the motive of sanctum
- 깃 허브 access token을 모티브로 만들어 짐

### GitHub access token의 특징
- 매우 긴 만료기간을 가진 토큰
- 사용자에 의해 언제든지 해지(revoke) 가능

---

## 라라벨 Sanctum API의 특징
### 중앙 집중적인 톤큰 관리
- 단일 데이터베이스 테이블에 사용자의 API 토큰을 저장하여 여러 개의 웹 어플리케이션 서버가 돌아가는 상황에서도 토큰을 중앙 집중형으로 관리할 수 있다.

## the reason of using sanctum
- SPA 인증을 하기 위해서 기존에는 jwt 프로토콜을 구현한 JWT-auth나 OAUTH를 사용해야 했다. OAUTH는 복잡한 인증 토큰이기 때문에 편리하게 인증하기 힘든 단점이 있으며, JWT-auth은 토큰을 중앙에서 관리하는 방식이 아니기 때문에 사용에 좋지 않다.

## 라라벨 Sanctum 세션 인증의 특징
- API 토큰 인증을 사용하지 않는다. (토큰을 통해서 인증을 할 경우 세션 인증 방식이 무효화 된다.)
- 라라벨 내장 쿠키 기반의 세션 인증 서비스를 사용한다.

---

### Sanctum 세션 인증의 보안 
- API 인증의 토큰기반이 아닌 세션 인증의 보안에 대한 설명이다.

#### CSRF 보호
- CSRF는 요청 위조 기법이다. 보통은 쿠키 세션에 저장된 인증 세션을 탈취하여 서비스 제공사가 아닌 다른 서비스에서 위조 요청을 하는 해킹 방법이다.
- 라라벨의 셍텀은 인증 세션 뿐만 아니라 도메인 기반 확인을 하기 때문에 CSRF 위조에 좀 더 강력하다.

#### session 인증
- 라라벨 내장 세션 인증을 통해 강력한 기본 보안 기능 제공

#### XSS 방어
- 인증 자격 증명(uthentication credentials) 유출을 방어한다.
- 세션 인증 방식은 통신이 일어나면 세션값이 바뀌게 되기 때문에 인증 정보가 유출이 되어도 변경 주기가 굉장히 짧기 때문에 안전하다고 볼 수 있다.

---

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

---

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
- 엘퀀트 모델 클래스에 HasApiTokens이라는 트레이트를 집어 넣은 것으로 인해서 엘로퀀트 모델의 멤버 변수로 token 값을 저장한다.
- HasApiTokens 이라는 이름과 같이 토큰을 가지고 있는 유저들의 토큰들을 가져온다. 
```
$user -> tokens
```
- 만약 엘로퀀트에서 특정 유저를 선택했다면 특정 유저의 토큰을 가져온다.
```
$user->tokens()->where('id', $id)
```

### 토큰 해지
- HasApiTokens 트레이트를 사용한다.
#### 모든 토큰 해지
```
$user->tokens()->delete();
```
#### 현재 유저로 로그인 된 대상의 토큰 해지하기
```
$request -> user() -> currentAccessToken() -> delete();
```
#### 지정된 토큰 해지
```
$user->tokens()->where('id', $id)->delete();
```
- where로 id를 기준으로 제거 했지만 다른 것을 기준으로 해도 제거 할 수 있다.


---

## Token Abilities (토큰 기능)
### 토큰에 권한 부여 하기
#### 권한 부여
- 문자열로 권한을 부여한다.
```
return $user->createToken('token-name', ['server:update'])->plainTextToken;
```
- 토큰에 부여한 문자열을 체크하여 권한이 있는 토큰인지 확인한다.
```
if ($user->tokenCan('server:update')) {
}
```
> 세션 인증으로 인증 요청을 했을 때는 권한에 관계 없이 항상 참으로 인정한다.

---

## 라우터에 sancturm guard 달기
### guard란?
https://stackoverflow.com/questions/34896130/laravel-what-is-a-guard
```
They're the definition of how the system should store and retrieve information about your users.
```
- 라라벨 어플리케이션 시스템이 유저의 정보를 저장하고 검색을 어떻게 할지에 대한 정의이다. 이런 의견도 있지만

```
A guard is a way of supplying the logic that is used to identify authenticated users.
```
- 이 설명이 더 guard란 이름에 적절한 것으로 보인다.

### guard
- 라라벨에서 guard란 인증된 사용자를 식별하는 로직을 의미한다.
- 라라벨에서의 인증은 인증되지 않은 유저의 접근을 차단하는 로직을 사용하기 때문에 guard라는 방어의 의미를 가진 용어를 사용한다.
```
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```
- 위를 보면 라우터에 미들웨어를 달아 주었다.
- 미들웨어를 컨트롤러에 달아 줄 수도 있지만, 특정 라우트에 대한 접근을 허용할지 말지 sanctum guard를 통해서 방어하는 방식으로 사용하고 있다.
- 미들웨어를 사용해서 유저의 권한을 나눠서 개발 할 수 있도록 설정

#### sanctum middleware의 역할
- 라라벨 셍텀의 세션 인증으로 들어 왔을 때, 라라벨 세션의 토큰 인증으로 들어 왔을 때 셍텀 가드가 이를 허용한다.

#### stateful 인증
- 백앤드에서의 상태저장을 stateful이라고 하며, 서버에서 상태저장을 하지 않는 방식을 stateless라고 한다.
- JWT 토큰과 같이 서버에 상태저장을 하지 않는 방식이 아니라, 서버에서 세션이나 토큰을 관리할 수 있도록 데이터베이스와 같은 저장소에 저장하는 방식을 사용하는 것이 stateful 방식이다. JWT는 stateless 방식이라고 할 수 있다.
- 라라벨에서 sanctum은 stateful 방식을 사용하고 있다. 세션이나 토큰을 저장소에 저장하는 방식으로 사용한다.

---

## SPA 인증
- Sanctum을 이용한 싱글페이지 어플리케이션(SPA) 인증을 하기 위한 간단한 방법을 제공
- 동일한 루트 도메인을 공유해야 한다. (도큐먼트에 top-level domain 이라고 적혀 있는데, top-level domain은 맨 뒤의 .com .kr .jp .org .net 등을 의미한다. 루트 도메인 단위로 인증을 허용해야 보안적인 위협을 방지할 수 있기 때문에 루트 도메인을 사용하는 것이라고 본다.)
- 라라케스트에서 나온 답변( https://laracasts.com/discuss/channels/laravel/set-multiple-domain-names-to-api-in-order-to-use-sanctum-with-frontends-on-different-top-level-domains) 을 보면 root 도메인을 최상위 도메인으로 보고 있다.
- 서브도메인은 화이트리스트 방식으로 허용할 수 있다.
- 동일한 root 도메인을 공유하면 되기 때문에 라라벨 프로젝트 안에 SPA 코드가 있어서 라라벨 어플리케이션를 통한 배포로 sanctum 세션 인증을 할 수도 있고, 라라벨과는 독립적인 저장소에서 SPA 인증을 할 수도 있다.
- SPA 인증은 세션을 통한 인증이기 때문에 어떤 종류의 토큰도 사용하지 않는다.

### First party Domain 설정
- First party Domain은 자사 도메인을 의미한다.
- 서버와 프론트가 공유하는 root 도메인이 무엇인지 설정하는 것이다.
- 로컬 개발 등의 IP주소를 통해 도메인을 공유할 경우에는 포트번호 127.0.0.1:8000 까지 설정해 줘야 셍텀인증 방식이 정상적으로 작동한다.
- 셍텀 가드에 허용할 도메인 리스트를 설정해야 한다.
- 도메인 설정을 통한 가드는 셍텀의 세션 인증 방식 또는 쿠키 인증 방식 모두에 적용되는 부분이다.

### sanctum middleware 설정
- app/Http/Kernel.php 파일 내의 API 미들웨어 그룹에 Sanctum 미들웨어를 추가 해 줘야 한다.
- 세션쿠키를 이용한 인증과 토큰을 이용한 인증 둘을 처리하는 미들웨어이다.

```
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

'api' => [
    EnsureFrontendRequestsAreStateful::class,
    'throttle:60,1',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```
- Kernel.php 파일에는 api 미들웨어로 접근하는 방식이 있고, web 미들웨어로 접근하는 방법이 있다. 그런데 sanctum은 api 미들웨어로 세팅을 한다.
- 라라벨의 route 폴더를 보면 api.php 와 web.php가 존재한다. web.php는 Kernel.php의 web 부분을 통해 미들웨어를 설정하며, api.php는 Kernel.php의 api 부분을 통해 미들웨어를 설정한다. 
- 보통 토큰을 통한 인증은 API 쪽을 통해서 인증을 하고, 세션쿠키를 통한 인증은 web 쪽을 통해서 인증을 한다. 그런데 Sanctum 미들웨어는 세션쿠키를 통한 인증임에도 불구하고 api 쪽을 통해서 인증을 한다. 
```
EnsureFrontendRequestsAreStateful::class,
```
- 클래스 이름을 읽어보면 '프론트 앤드의 리퀘스트를 stateful 방식으로 허용'한다라는 의미이다.
- 라라벨의 기본 stateful 방식은 세션인증을 디비에 저장하는 방식이며, Kernel.php의 web 미들웨어 세팅을 통해 리퀘스트가 서버 내부 로직에 전달 된다. 원래 Kernel.php의 api 미들웨어 쪽에는 세션쿠키의 값을 인식하고 처리하는 부분이 없지만, Kernel.php의 api 미들웨어 쪽에도 세션쿠키를 인식하여 처리하는 기능을 추가하는 것이다.

---

## CORS and Cookie
### CORS
- 라라벨 어플리케이션의 API 도메인과 SPA 페이지의 도메인이 다를 경우 (root 도메인은 같지만 서브 도메인이 다를 경우이다.) CORS 문제가 발생할 수 있다.
- CORS란? Cross-Origin Resource Sharing 으로 라라벨 어플리케이션 서버가 다른 도메인에서 요청한 요청을 허용하는 것이다. 기본적으로는 SOP(same-origin policy) 상태로 서브 도메인이 다른 것을 포함하여 다른 도메인이면 
- CORS에 대한 설정은 config\cors.php에 되어 있으며 라라벨을 배포하는 Nginx나 Apache에 의해서도 가능하다. Nginx 나 Apache에서는 CORS 관련 설정을 하게되면 어플리케이션 수준에서 관리가 안 되는 문제가 발생할 수 있다.

### third-party-cookie 제한
- 브라우저는 도메인이 서로 다를 때 한 도메인의 쿠키를 다른 도메인의 쿠키에 전송하지 못하게 하고 있다. 그런데 리퀘스트 설정에 따라서 이를 허용할 수도 있다.
- third-party-cookie를 제한하는 최근의(2010년대 중/후반부터 시작 된) 브라우저 정책으로 기본적으로 서로 다른 도메인에 쿠키 전송을 차단한다.


### Access-Control-Allow-Credentials
- Reference : https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Access-Control-Allow-Credentials
- 자격 증명이란? 'authorization 헤더들' 또는 'TLS 클라이언트 인증서'에 관한 부분을 의미함
- 기본적으로 브라우저는 자바스크립트 코드를 통해서 'authorization 헤더들' 또는 'TLS 클라이언트 인증서'에 해당하는 자격증명에 관한 부분에 접근을 못하게 막고 있다. 리스폰스 값으로 Access-Control-Allow-Credentials 헤더가 들어 있는 응답을 서버에서 받게 되면 프론트앤드 브라우저의 자바스크립트 코드가 자격증명에 관한 부분에 엑세스 할 수 있다.
- 서버에서 보내는 리스폰스의 헤더에 붙는다. 리퀘스트 요청에서는 Access-Control-Allow-Credentials 헤더가 붙지 않는다.
- 리스폰스로 받는 Access-Control-Allow-Credentials 헤더 값과 리퀘스트를 보낼 때 자바스크립트 통신 API에 설정한 값이 모두 있어야 자격증명 부분에 자바스크립트 코드로 접근할 수 있다.

### Access-Control-Allow-Credentials의 리퀘스트 요청
- 자바스크립트 통신 API에 credentials 설정을 한다. 브라우저의 통신 API가 데이터를 받게 되면, credentials 설정이 된 브라우저 API는 응답에서 자바스크립트 코드로 자격증명에 관한 부분을 접근할 수 있게 허용한다.
- Access-Control-Allow-Credentials 헤더를 리스폰스에 받아서 자바스크립트 코드로 자격증명 부분에 접근하기 위해서는 반드시 설정 해 줘야 하는 부분이다.

#### axios를 사용할 때
- axios의 경우에는 라라벨에서 `resources/js/bootstrap.js` 부분에서 수정할 수 있다.
```
axios.defaults.withCredentials = true;
```

#### XHR을 사용할 때
```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```

#### Fetch를 사용할 때
```
fetch(url, {
  credentials: 'include'
})
```

### 라라벨 설정에서 서브도메인 허용
- config\session.php 경로에서
```
'domain' => env('SESSION_DOMAIN', null),
```
- 위 부분에 라라벨에서 WAS(web application)에서 허용하는 서브 도메인을 설정해 줘야 한다. `.env`파일의 SESSION_DOMAIN 부분에 서브 도메인을 설정할 수 있다.
example
```
SESSION_DOMAIN=sub.domain.com, domain.com:8000, localhost:8000
```

---

## Sanctum 가드에 접근하여 로그인 인증 받기
### CSRF
#### CSRF란?
- Cross-site request forgery '교차 사이트 요청 위조'라고 할 수 있는데, 허용된 도메인이 아니라, 다른 서브도메인이 다르거나 루트 도메인이 다른 경우에도 웹서버에 요청을 허가하는 것이다.

#### CSRF 초기화
- CSRF 보호(protection)를 초기화란? 라라벨 Sanctum은 기본적으로 다른 도메인에서의 요청을 거부한다. 따라서 라라벨 어플리케이션이 다른 도메인에서의 요청을 허가 해 줘야 하는데, 이를 가능하게 하는 것이 CSRF 초기화이다.
- SPA 로그인 페이지에서 CSRF 보호(protection)를 초기화하기 위해서 `/sanctum/csrf-cookie` 라우트로 요청을 하게 되면 이 요청을 한 리퀘스트에 한해서 CSRF 보호를 해제해 준다.
```
axios.get('/sanctum/csrf-cookie').then(response => {
    // Login을 하기 위한 통신을 하는 부분에 대한 코드를 입력한다.
});
```
- 로그인을 하기 위해서는 로그인 데이터를 보내야 하는데, 다른 도메인에서 접근한다면 라라벨 sanctum 가드에 의해서 차단되기 때문에, 다른 도메인에서 온 요청이 차단되지 않게 하도록 서버에 부탁하는 과정이 필요하다.
- 이 요청의 응답으로 쿠키를 받을 때, 쿠키에는 XSRF-TOKEN 값을 받는다. ajax 요청을 할 때, 쿠키에 저장된 XSRF-TOKEN 토큰을 리퀘스트의 헤더에 붙여서 전달 해 줘야 한다.
- 헤더에 붙일 때 헤더의 key 이름은 X-XSRF-TOKEN로 한다.
- Axios 및 Angular HttpClient 등의 라이브러리는 리퀘스트 헤더에 자동으로 붙여준다. 이런 기능이 있다는 것은 쿠키의 XSRF-TOKEN 값을 헤더의 X-XSRF-TOKEN으로 세팅하는 표준이 있다는 것을 알 수 있다.

### 로그인
- 아래 부분을 통해 CSRF 초기화가 이뤄진 상태에서
```
axios.get('/sanctum/csrf-cookie').then(response => {
    // Login을 하기 위한 통신을 하는 부분에 대한 코드를 입력한다.
});
```
- 로그인 부분에 통신을 하기 위한 요청을 한다. 기본적으로는 라라벨 /login 라우트에 POST로 요청한다.
- laravel/jetstream을 인증스케폴딩을 사용하면 sanctum으로 /login 라우트에 POST로 요청을 할 수 있다.
- 기본인증을 사용하지 않고 커스텀 인증을 사용할 경우에는 다른 라우트를 통해 로그인을 할 수 있다. 이 때는 라라벨이 제공하는 세션기반 인증의 파사드인 Auth 및 Session 파사드를 통해서 인증을 컨트롤 할 수 있다.

---

## 라우트 보호하기
- sanctum 인증 가드는 미들웨어로 제공되고 있다. 
- 라라벨의 kernel파일(app\Http\Kernel.php)에 등록된 `EnsureFrontendRequestsAreStateful::class`을 통해서 'auth:sanctum' 이름으로 등록된 미들웨어를 사용할 수 있다.
```
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```
- 이 미들웨어에 의해서 sanctum에 의해 인증을 받아 인증된 요청만 받는다.
- 미들웨어는 서비스 컨테이너나 라우트에 달아 줄 수 있는데, Sanctum 미들웨어는 인증된 사용자만 허용하게 하는 기능을 가졌으므로 모든 요청에 대해 추가 기능을 다는 서비스 컨테이너에는 달아주지 않는다. 특정 라우트의 접근에 인증 기능을 달아주는 방식으로 라우터의 접근제한을 하기 위한 장치이다.
- 따라서 라우트를 만들 때에는 인증 권한에 따라 라우트를 만들어 줘야 한다.

### 소켓 통신을 할 때 Sanctum 가드를 사용한 인증을 하는 방법
- 라라벨의 브로드캐스트 채널을 이용할 때 Sanctum 요청을 하는 방법
- 소켓 통신은 나중에 다루자.

---

## 모바일 어플리케이션 인증
- 쿠키 세션을 이용한 방식이 아니라, 토큰을 사용한 요청이다.
- 쿠키 세션을 사용하면, 요청할 때 마다 쿠키 세션이 달라지기 때문에 보안을 높일 수 있다. 하지만 토큰 방식의 경우, 요청시 마다 갱신이 되지 않는다. 한번 토큰을 발급 받으면 그 토큰을 계속적으로 사용하는 방식이다. 따라서 어플리케이션에 접속할 때 개인 인증 등으로 어느 정도 보안 대책이 세워진 대상에 대해서 모바일 어플리케이션을 사용할 때 토큰을 어플리케이션에 저장을 하기 때문에 접속을 하는데 용이하다.
- 리프레시 토큰을 만드는 방식도 있지만, 라라벨에서 지원하지는 않기 때문에 커스터마이징을 해야 한다.

### Sanctum API 토큰 발급하기
- 토큰을 발급하는 것은 로그인을 한 후 토큰을 발급 받아, 로그인 대신 토큰을 저장하는 방식으로 사용한다.
- 라라벨의 기본 인증을 통해서 토큰을 발급하지 않기 때문에 토큰을 발급하는 컨트롤러를 만들어 줘야 한다.
- 로그인 할 때 토큰을 받기 때문에 로그인 관련 컨트롤러에 토큰 발급의 로직을 작성해 줘야 한다.
- 예를 들어 app\Http\Controllers\api\authentication.php의 login 메서드를 작성하는 방식을 사용한다.
토큰 발급의 예시
```
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;

Route::post('/sanctum/token', function (Request $request) {
    $request->validate([
        'email' => 'required|email',
        'password' => 'required',
        'device_name' => 'required',
    ]);

    $user = User::where('email', $request->email)->first();

    if (! $user || ! Hash::check($request->password, $user->password)) {
        throw ValidationException::withMessages([
            'email' => ['The provided credentials are incorrect.'],
        ]);
    }

    return $user->createToken($request->device_name)->plainTextToken;
});
```

### Sanctum 토큰 해지하기
- 토큰을 해지하는 메서드는 로그아웃에 포함시킬는 방법을 사용해도 된다.
- 라라벨의 유저 테이블을 다루는 model인 user 엘로퀀트 모델에 HasApiTokens 트레잇을 세팅하면
- 트레이트의 tokens 메서드가 user model에 할당되고 이를 통해 사용자의 API 토큰에 액세스할 수 있다.
```
use App\Models\User;

... 
// Revoke all tokens...
// 모든 토큰 해지...
$user->tokens()->delete();

// Revoke a specific token...
// 지정된 토큰 해지...
$user->tokens()->where('id', $id)->delete();
```

---

## TEST
- 인증 처리를 한 이후의 단위 태스트를 위해서 위해서 TEST 모듈을 만들 수 있다.
```
use App\Models\User;
use Laravel\Sanctum\Sanctum;

public function test_task_list_can_be_retrieved()
{
    Sanctum::actingAs(
        User::factory()->create(),
        ['view-tasks']
    );

    $response = $this->get('/api/task');

    $response->assertOk();
}
```
- 태스트에 이 태스트를 작성하면 설정한 `/api/task` 라우터로 보내 처리하는 것과 같은 태스트를 실시한다.
- `['view-tasks']` 부분은 'Token Abilities'(토큰 기능)를 설정했을 때 토큰 기능을 추가 해 주는 부분이다.
- 모든 '토큰 기능'에 대해서 접근을 허가하려면 `*`을 사용할 수 있다.
```
Sanctum::actingAs(
    User::factory()->create(),
    ['*']
);
```



---

## Reference
https://laravel.kr/docs/8.x/sanctum
