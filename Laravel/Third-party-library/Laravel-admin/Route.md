# 라우터

## laravel-admin의 라우터 설정 파일
- 라라벨의 기본 라우터 파일 : `app/routes/web.php`
- laravel-admin의 라우터 파일 : `app/Admin/routes.php`

---

## laravel-admin의 기본 라우팅
```
<?php
use Encore\PHPInfo\Http\Controllers\PHPInfoController;

Route::get('phpinfo', PHPInfoController::class.'@index');
```
- 기본 라우팅의 형식은 라라벨 7버전 이하의 라우팅 정의 방식과 같다.

### 라라벨 기본 라우팅
```
<?php
use App\Http\Controllers\UserController;
Route::get('/user', 'UserController@index');
```

---

## 라우터 그룹
### 라라벨 기본 라우터 그룹
```
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second Middleware
    });

    Route::get('user/profile', function () {
        // Uses first & second Middleware
    });
});
```
- 하나의 객체 안의 middleware 메소드와 group 메소드가 나눠져 있다.


### laravel-admin의 라우터 그룹
```
Route::group([
    'middleware' => 'admin.permission:allow,administrator,editor',
], function ($router) {
    $router->resource('users', UserController::class);
    ...
});
```
- group이라는 메소드의 인자에 연관 배열을 통해서 미들웨어를 할당한다.



