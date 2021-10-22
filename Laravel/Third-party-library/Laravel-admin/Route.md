# 라우터
---
## default route
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
- 


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

