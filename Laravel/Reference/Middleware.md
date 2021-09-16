# 미들웨어
- HTTP 요청이 애플리케이션 내부의 로직에 도달하기 전에 통과해야 하는 레이어를 미들웨어라고 할 수 있다.
- 미들웨어는 필터 기능, 차단기능, HTTP 요청의 변경 기능 등을 수행한다.

## 미들웨어의 역할
- 컨트롤러에 어떤 조건에 따라서 리퀘스트 객체 내부의 값을 다른 값으로 바꾸는 역할
- 유저별로 리퀘스트를 허용할지 허용하지 않을지를 선별하기 위한 것.
- 리퀘스트뿐만 아니라 리스폰스 데이터도 필터 및 변경을 할 수 있다.

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
- routes\web.php의 모든 라우터에 지정할 미들웨어를 설정 `'web' => []`
- routes\api.php의 모든 라우터에 지정할 미들웨어를 설정 `'api' => []`

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
- ShareErrorsFromSession : 라라벨은 애플리케이션의 여러 에러들을 MessageBag 객체에 집어 넣는데 MessageBag의 에러들을 세션에 저장하고 있다. 세션의 에러를 가져와서 라라벨의 view에서 사용할 수 있도록 하는 기능이다.
- VerifyCsrfToken : 리퀘스트 헤더에 csrf 토큰이 포함되어 있는지 체크하고 포함되어 있다면 발급된 csrf 토큰과 일치하는지 확인한다.
- SubstituteBindings : ??


### 라우터 미들웨어
- 라우터 미들웨어는 라우터에 뭔가를 추가할 때 쓰는 것이다. 
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
- Authenticate : 인증된 사용자에게만 주어진 라우터를 사용할 수 있게 한다.
- AuthenticateWithBasicAuth : 라라벨 유저를 로그인 페이지 없이 브라우저가 제공하는 ID/PASS 인증인 basicauth를 통한 인증 방식을 사용한다.
- SetCacheHeaders : 미들웨어를 통해서 리스폰스 헤더의 캐시값을 설정할 수 있다.
```
Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function () {
});
```
- Authorize : 컨트롤러의 엑션을 실행할 권한이 있는지 없는지를 필터하는 기능이다. 이 기능은 라라벨의 policy를 이용하는 기능이다. 
- RedirectIfAuthenticated : 어카운트 인증이 된 유저에게는 게스트 유저와는 다른 화면을 보여주기 위해 게스트 상태에서만 보여주는 페이지에 접근 했을 때, 로그인 된 유저가 보는 화면으로 리다이렉트 시키는 기능이다.
- RequirePassword : 어떤 페이지로 넘어갈 때 암호를 입력하고 페이지 이동이 되는 부분일 경우, 이전 리퀘스트 상태를 저장을 하고 패스워드 입력 화면에서 패스워드를 입력하고 인증을 받고 나서 다음 화면에 저장한 리퀘스트를 그대로 전달해 주는 역할을 하는 미들웨어이다.
- ValidateSignature : 리퀘스트에 유효한 서명이 존재하는지 확인한다. (서명이란 뭔가 발급을 해서 그 발급된 것이 있는지 확인하는 절차를 의미한다. 라라벨에서 발급한 서명이 유효한 서명인지 아닌지 판단하는 것)
- ThrottleRequests : 얼마의 시간 단위에 얼마의 리퀘스트 횟수를 허용할지 제한하는 미들웨어로 제한 횟수를 초과하면 라우터 접근이 차단된다.
- EnsureEmailIsVerified : 메일 인증이 된 사용자만 접근을 허용한다. 

## 미들웨어 생성
### artisan 커멘드로 생성하기
```
php artisan make:middleware CheckAge
```

```
<?php

namespace App\Http\Middleware;

use Closure;

class CheckAge
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->age <= 200) {
            return redirect('home');
        }

        return $next($request);
    }
}
```
- 미들웨어 클래스는 상속을 받지 않는다.
- app\Http\Kernel.php 파일에 보면 미들웨어 클래스를 그냥 넣어서 정의한다. 배열에 클래스를 넣으면 배열에 정의된 클래스의 handle 메소드를 꺼내어 리퀘스트 및 리스폰스를 필터할 때 사용한다.
- handle 메소드는 $next라는 클로저에 $request를 집어 넣어 반환한다. 만약 리퀘스트의 값을 변경했다면 미들웨어를 거친 리퀘스트는 변경된 값을 라우터나 컨트롤러에 전달할 것이다.
- 리퀘스트 값을 필터링 하고 조건에 맞을 경우 리퀘스트를 다음 단계로 전달하고 조건에 맞지 않으면 리퀘스를 전달하지 않는 처리를 한다. 
- 위 예제에서는 $request의 age라는 값이 200이하일 경우 리퀘스트를 다음 단계로 전달하지 않고 home 네임의 라우터로 리다이렉트 시키고 있다.
- `$next($request)`의 다음 단계로 리퀘스트를 전달하는 것은 다음 미들웨어로 전달할 수도 있으며 라우터나 컨트롤러 등의 애플리케이션의 내부 쪽으로 전달하는 것이다.

### 미들웨어와 서비스 컨테이너
- 모든 미들웨어는 서비스 컨테이너를 통해서 처리된다.
- 서비스 컨테이너를 통해서 처리 되기 때문에 라라벨 프레임워크의 의존성 주입을 사용할 수 있다.
- (좀 더 정확하게 알아 볼 필요가 있는 부분)

### 리스폰스 레이어 역할의 미들웨어
```
<?php

namespace App\Http\Middleware;

use Closure;

class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        // Perform action

        return $response;
    }
}
```
- 리퀘스트가 애플리케이션에서 실행이 된 이후 처리되는 미들웨어
- 애플리케이션에서 리퀘스트가 처리 되면 리퀘스트는 애플리케이션 내에서 더 이상 사용되지 않기 때문에 리퀘스트를 필터링하거나 변경할 필요가 없다.
- 리스폰스가 통과해야 할 레이어의 기능으로 동작하는 미들웨어이다.

#### 리퀘스트 레이어 역할의 미들웨어와의 비교
```
<?php

namespace App\Http\Middleware;

use Closure;

class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {
        // Perform action

        return $next($request);
    }
}
```
- 리퀘스트 레이어의 미들웨어와 리스폰스 레이어의 미들웨어는 무엇을 return 하느냐에 따라 갈린다.
- 리퀘스트 레이어의 미들웨어의 경우 `return $next($request)`를 사용하며 리스폰스 레이어의 미들웨어의 경우 `return $response;`를 반환한다.
- 그런데 리스폰스 레이어인 미들웨어를 보면 `$response = $next($request)`라서 리퀘스트 레이어와 리스폰스 레이어가 같은 `return $next($request)`를 반환하는 것으로 보인다.
- 리퀘스트 레이어의 미들웨어는 $request의 값을 이용한 로직을 구현하고 $next($request)의 값을 이용한 로직을 구현하지 않는다.
- 리스폰스 레이어의 미들웨어는 $next($request)의 값을 이용한 로직을 구현하고 $request의 값을 이용한 로직을 구현하지 않는다.

---

## Reference
- https://laravel.kr/docs/8.x/middleware
