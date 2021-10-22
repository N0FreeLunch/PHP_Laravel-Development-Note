# 라우터
---
## default route
- app\Admin\routes.php
```
use Illuminate\Routing\Router;

Admin::routes();

Route::group([
    'prefix'        => config('admin.route.prefix'),
    'namespace'     => config('admin.route.namespace'),
    'middleware'    => config('admin.route.middleware'),
    'as'            => config('admin.route.prefix') . '.',
], function (Router $router) {

    $router->get('/', 'HomeController@index')->name('home');

});
```

## 디폴트 라우트 다시 쓰기
- 디폴트 라우트 구성을 라라벨 표준 코딩 스타일에 맞게 변경 할 수 있다.
```
Route::prefix(config('admin.route.prefix'))
      ->middleware(config('admin.route.middleware'))
      ->namespace(config('admin.route.namespace'))
      ->name(config('admin.route.prefix') . '.')
      ->group(function () {
      
        Route::get('/', 'HomeController@index')->name('home');
        
      });
```
- https://laravel.com/api/8.x/Illuminate/Routing/Route.html 이 부분에서 라라벨 공식 문서에 나오지 않는 숨겨진 라우트 객체의 메소드를 확인할 수 있다.

## 라우터 설정
- config\admin.php
```
    'route' => [

        'prefix' => env('ADMIN_ROUTE_PREFIX', 'admin'),

        'namespace' => 'App\\Admin\\Controllers',

        'middleware' => ['web', 'admin'],
    ],
```
- 미들웨어를 보면 라라벨의 기본 web 미들웨어를 집어 넣고, 추가적으로 admin에 필요한 미들웨어를 추가 해 주었다.
- 그런데 admin 미들웨어가 `app\Http\Kernel.php` 부분에 정의되어 있지 않다. (어디서 가져 온 거죠? 어떻게 가져 온 거죠?)
- ` env('ADMIN_ROUTE_PREFIX', 'admin')` 부분을 보면 라우터의 prefix를 env 파일 안에 설정할 수 있으며, 기본적으로 env 파일 안에 ADMIN_ROUTE_PREFIX 키가 세팅되어 있지 않기 때문에 `hostname.com/amdin`과 같은 방식으로 어드민 페이지 path가 설정된다.



## 라우팅 설정
## laravel-admin의 기본 라우팅
- `프로젝트폴더/Admin/routes.php`
```
<?php
use Encore\PHPInfo\Http\Controllers\PHPInfoController;

Route::get('phpinfo', PHPInfoController::class.'@index');
```
또는
```
Route::get('phpinfo', 'PHPInfoController@index');
```
- laravel-admin의 기본 라우팅의 형식은 라라벨 7버전 이하의 라우팅 정의 방식과 같다.

## 라라벨 기본 라우팅
- 프로젝트폴더/routes/web.php
```
<?php
use App\Http\Controllers\UserController;
Route::get('/user', 'UserController@index');
```

---

## 라우터 그룹
### 라라벨 기본 라우터 그룹
```
Route::get('/', function () {
    //
})->middleware('web');

Route::group(['middleware' => ['web']], function () {
    //
});

Route::middleware(['web', 'subscribed'])->group(function () {
    //
});
```

### laravel-admin의 라우터 그룹
```
Route::group([
    'middleware' => 'admin.permission:allow,administrator,editor',
], function ($router) {
    $router->resource('users', UserController::class);
    ...
});
```
- 라라벨의 기본 라우터 그룹과 동일한 방식으로 라우터 그룹을 사용하는 것을 알 수 있다.
- 연관배열의 키는 동일한 방식으로 사용했는데 연관 배열의 value가 라라벨 기본에서는 배열, laravel-admin에서는 문자열로 되어 있다.
- 완전 동일한지 어떤지 확인을 할 필요가 있어 보임

---

### 라우터 리스트 보기
- 라라벨 어드민의 다음 경로를 통해 웹에서 라우터 리스트를 확인할 수 있다. `http://localhost/admin/helpers/routes.`

---

