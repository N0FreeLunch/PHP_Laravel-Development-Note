## laravel admin에서의 controller
- 라라벨 admin의 컨트롤러는 CURD를 다루는 방식의 컨트롤러로 구성된다.
- 그래서 컨트롤러를 다루는 명령은 모델을 기반으로 생성한다.

## 컨트롤러 생성 명령어

### Syntex
```
php artisan admin:컨트롤러명 --model=모델네임스페이스
```

#### MAC/Linux
```
php artisan admin:make UserController --model=App\\User
```

#### laravel 8의 경우
```
php artisan admin:make UserController --model=App\\Models\\User
```

#### windows
```
php artisan admin:make UserController --model=App\User
```

### 단축 명령어
- 1.8 버전 이상에서 사용 가능
```
php artisan admin:controller --model=App\User
```
- `app/Admin/Controllers/UserController.php`를 생성한다.

#### Syntex
```
php artisan admin:controller --model=App\모델
```
- `app/Admin/Controllers/첫번째글자가대문자인모델Controller.php`를 생성한다.
- 만들어진 컨트롤러는 controller 폴더 바로 아래에 위치하기 때문에 폴더 구조의 자유도를 낮춘다.


## 라우터 만들기
- 컨트롤러를 자동 생성했다고 해서 라우터가 따로 만들어지지는 않는다.
- laravel admin의 라우터는 `app/Admin/routes.php` 부분에 경로를 생성한다.

### 기본 라우터를 사용했을 경우
```
$router->resource('demo/users', UserController::class);
```

### 라라벨 표준 스타일의 라우터를 사용했을 경우
```
Route::resource('demo/users', UserController::class);
```

#### 컨트롤러 접속 태스트
```
http://127.0.0.1:8000/admin/demo/users
```



## Reference
- https://laravel-admin.org/docs/en/README
