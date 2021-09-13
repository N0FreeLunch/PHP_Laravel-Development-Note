# 미들웨어
- HTTP 리퀘스트를 필터링 하는 것이다. 

## 미들웨어의 역할
- 컨트롤러에 어떤 조건에 따라서 리퀘스트 객체 내부의 값을 다른 값으로 바꾸는 역할
- 유저별로 리퀘스트를 허용할지 허용하지 않을지를 선별하기 위한 것.

## 라라벨 기본 미들웨어
- app\Http\Kernel.php 파일은 정의한 미들웨어를 리퀘스트를 필터링하기 위해 적용한 부분이다.

### 기본 미들웨어
```
protected $middleware = [
        // \App\Http\Middleware\TrustHosts::class,
        \App\Http\Middleware\TrustProxies::class,
        \Fruitcake\Cors\HandleCors::class,
        \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
    ];
```
- TrustHosts : 호스트(도메인 또는 서브도메인을 포함한 호스트)가 지정한 패턴일 때만 허용을 하기 위한 미들웨어 TrustHosts 클래스 안에는 URL host의 패턴을 집어 넣는다.
- TrustProxies : 프록시 서버로 부터 요청된 리퀘스트라면 지정한 프록시 서버일 경우 HTTP 요청을 받았을 때 HTTPS 요청으로 반환 하도록 한다.
- HandleCors : 모든 리퀘스트에 option 메소드를 추가
- PreventRequestsDuringMaintenance : 메인테인스 모드에서 접속을 허용할 대상을 설정하는 것, 메인테인스 모드 활성화는 `php artisan down` 비활성화는 `php artisan up`을 사용한다.
- ValidatePostSize : POST 요청의 사이즈를 설정하는 미들웨어. 리퀘스트 헤더의 'CONTENT_LENGTH'가 최대치를 초과할 때 블락하는 기능
- TrimStrings : 리퀘스트 요청의 모든 문자열 필드들을 앞뒤 공백문자 종류가 포함될 경우 앞뒤 공백 문자들을 지워주는 trim 처리를 한다. 이 미들웨어가 있다면 코드 작성 영역에서 trim 처리를 하지 않아도 된다.
- ConvertEmptyStringsToNull : 빈 문자 필드는 null으로 바꿔준다.

### 미들웨어 그룹
```
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            'throttle:api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];
```
- EncryptCookies : 쿠키를 암호화 할지를 결정, 쿠키를 암호화하면 클라이언트에서 쿠키 값이 무엇을 의미하는지 알 수 없기 때문에 쿠키 조작에서 안전해진다.
- AddQueuedCookiesToResponse : 쿠키 큐에 쿠키를 저장해 놓으면 리스폰스를 생성할 때 큐에 저장된 순서대로 쿠키를 추가하는 기능. Cookie 파사드를 이용해서 추가한 쿠키를 리퀘스트에 추가할지를 설정하는 기능. 리퀘스트의 필터 뿐만 아니라 리스폰스에 설정할 것도 미들웨어를 통해서 추가할 수 있다. 
- StartSession : 세션 기능을 사용할지 하지 않을지
- AuthenticateSession : 로그인 한 유저인지를 판별하기 위한 세션을 집어 넣는다. (https://laravel.com/api/5.5/Illuminate/Session/Middleware/AuthenticateSession.html)
- ShareErrorsFromSession : 라라벨은 애플리케이션의 여러 에러들을 MessageBag 객체에 집어 넣는데 MessageBag의 에러들을 
- VerifyCsrfToken : 
- SubstituteBindings : 


### 라우터 미들웨어
```
protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];
```



## 주된 쓰임새
- 라라벨의 기본 미들웨어는 사용자의 
