# 컨트롤러

## 컨트롤러 : 리퀘스트의 비즈니스 처리 로직이 들어가는 곳
- 라라벨에서 리퀘스트에 대한에 처리 로직을 작성하는 곳은 처음에 라우터(routes.php) 부터 시작을 한다.
- 물론 라우터를 거치기 전에 미들웨어를 라우터 이전에 달아 줄 수 있지만, 미들 웨어는 비즈니스 로직에 대한 처리를 하는 곳은 아니다.
- 마찬가지로 부트스트레핑 과정은 리퀘스트 객체를 사용하기 이전에 라라벨 프레임워크에서 기본적으로 세팅 되어야 할 기능을 세팅하는 곳이기 때문에 리퀘스트에 대한 처리를 하는 곳은 아니다.
- 라우터에는 라우팅(PATH 분류)에 관한 기능만 모아두고 세부적인 리퀘스트에 대한 처리는 컨트롤러에게 맡긴다.
- 컨트롤러의 로직이 복잡해질 경우, 따로 비즈니스 로직에 대한 처리를 하는 레이어를 만들어서 처리 할 수도 있다.
- `app/Http/Controllers`폴더에 컨트롤러를 만들어 준다. 

## 컨트롤러의 예
```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Models\User;

class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return View
     */
    public function show($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```
- 컨트롤러는 컨트롤러 클래스 `use App\Http\Controllers\Controller;`를 사용하여 `extends Controller`상속을 받는다. 
- Controller 클래스의 상속을 받기 때문에 Controller 클래스가 가지고 있는 메소드를 사용할 수 있다.

## 라우트에 컨트롤러 연결하기
`routes\web.php` 또는 `routes\api.php` 위치에서
```
use App\Http\Controllers\UserController;

Route::get('user/{id}', [UserController::class, 'show']);
```
- `app/Http/Controllers/UserController.php`의 php 파일을 `App\Http\Controllers\UserController;`네임 스페이스로 연결하고 UserController 클래스의 show 메서드를 사용한다고 지정한다.
- 라라벨 6버전까지는 라우터를 다음과 같이 사용하였다.
```
Route::get('user/{id}', 'UserController@show');
```

## 컨트롤러 클래스
- 컨트롤러는 컨트롤러 클래스 `App\Http\Controllers\Controller`의 상속을 받는다.
- 컨트롤러 클래스가 가진 메서드는 middleware, validate, dispatch 등의 메서드를 가지고 있다.
- 컨트롤러 클래스의 상속을 받지 않아도 컨트롤러가 동작할 수 있다.(? 이거 태스트가 필요한 듯)

## 단일 메서드만 가진 컨트롤러
- 보통 컨트롤러에는 여러 메서드가 들어간다. 그런데 단일 메서드를 가진 컨트롤러도 존재한다.
- 이 경우, 컨트롤러의 역할은 컨트롤러의 클래스 이름으로만 정하고 메서드의 동작은 정하지 않는 방식으로 만들 수 있다. 
- 컨트롤러의 클래스에 \_\_invoke 메서드를 사용하여 라우터에서 컨트롤러를 호출할 때 메서드 없이 컨트롤러만 사용해도 자동으로 \_\_invoke 메서드가 호출되게 만드는 것이다.

```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Models\User;

class ShowProfile extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return View
     */
    public function __invoke($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```
```
use App\Http\Controllers\ShowProfile;

Route::get('user/{id}', ShowProfile::class);
```
#### \_\_invoke()
> The \_\_invoke() method is called when a script tries to call an object as a function.
- php 스크립트가 오브젝트를 함수로 실행하도록 시도한다는 의미
- \_\_invoke 라는 매직 메서드가 클래스 안에 있는 경우, php가 오브젝트를 호출할 때 함수로 호출하게 한다. 그러니까 클래스 외부에서는 이 객체를 오브젝트가 아닌 함수로 여긴다.
- 일반적으로 라우터는 컨트롤러의 메서드를 호출 다음과 같이 호출하는 방법을 사용한다. `Route::get('user/{id}', [UserController::class, 'show']);`
- 라우터에서 컨트롤러를 연결하지 않을 때 다음과 같이 두 번째 인자에 배열이 아닌 함수를 사용한다.
```
Route::get('user/{id}', function ($id) {
    return view('user.profile', ['user' => User::findOrFail($id)]);
});
```
- 위의 경우와 마찬가지로 \_\_invoke()를 사용했을 때는 객체를 함수로 보고 호출한 것으로 볼 수 있는 것이다. `Route::get('user/{id}', ShowProfile::class);`
- 문법을 보면 \_\_invoke()를 쓰는 사용한 객체를 함수로 불러다가 쓰는 것이라고 파악할 수 있지만, invoke 매직메서드를 쓰는 것이 특이한 문법을 사용하는 것이라서 지양하는 편이 좋을 것 같다. 그냥 메서드 하나 들어간 클래스를 그냥 만드는 것이 나을 수도 있어 보인다.

#### invoke 컨트롤러를 만드는 명령어
```
php artisan make:controller ShowProfile --invokable
```

## 컨트롤러 미들웨어
- 미들웨어는 라우트 그룹에 지정을 할 수 있으며, 라우터 각각에 지정을 할 수도 있다. 그리고 컨트롤러에도 지정을 할 수 있다.
- 컨트롤러에 미들웨어를 지정하기 위해서는 Controller 클래스의 상속을 받는 클래스이어야 한다. Controller 클래스가 가지고 있는 미들웨어 메서드를 상속 받아서 사용을 한다.
- 컨트롤러에 미들웨어를 지정하는 방법은 컨트롤러의 생성자에 미들웨어를 지정하는 방법이다. 


### 컨트롤러 미들웨어를 생성자에 쓰는 이유
- 이론적으로는 생성자 이외의 메서드에서 직접 호출해서 사용할 수도 있다.
- 클래스는 단일 책임의 원칙을 가진다. 하지만, 하지만 컨트롤러의 하나의 기능의 그룹역할로 가능하면 하나의 비즈니스적 단위에 대해서 'index, create, store, show, edit, update, destroy' 이런 메서드들로 구성하는 것을 요구하고 있다. 하나의 비즈니스적 단위에 대해서 비슷한 역할을 하는 것이라면 컨트롤러를 나누는 것을 의미하며, 하나의 비즈니스적 단위에 대해서 다른 역할을 가지고 있는 것이라면 메서드로 나눠서 만드는 것을 요구한다.
- 미들웨어는 중간에 필터를 하는 역할이며 필터를 추가한다는 것은 비즈니스 그룹 단위를 가진 컨트롤러에 달아주는 쪽으로 만든다는 의미를 가지고 있다.
- 즉 미들웨어는 비즈니스 그룹 단위의 필터를 하기 위한 역할을 가지고 있는 것이라고 볼 수 있다. 그래서 한 단위의 비즈니스 로직 전체에 미들웨어 필터를 달아주기 위해 모든 메서드에 적용될 수 있게 생성자로 사용을 한다. 

### 미들웨어를 지정하는 방법
#### 라우터 하나에 지정하는 방법
```
Route::get('profile', [UserController::class, 'show'])->middleware('auth');
```

#### 라우터 그룹에 지정하는 방법
```
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second middleware...
    });

    Route::get('user/profile', function () {
        // Uses first & second middleware...
    });
});
```

#### 컨트롤러에 미들웨어를 지정하는 방법
```
class UserController extends Controller
{
    /**
     * Instantiate a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth');

        $this->middleware('log')->only('index');

        $this->middleware('subscribed')->except('store');
    }
}
```

#### `app\Http\Kernel.php`에 등록된 미들웨어를 사용하지 않고 미들웨어를 추가하는 동시에 미들웨어에 사용할 함수를 등록하기
> 컨트롤러를 사용하면 Closure를 사용하여 미들웨어를 등록 할 수 있습니다.
- https://laravel.com/api/8.x/Illuminate/Routing/Controller.html#method_middleware 부분을 보면 미들웨어로 받는 $request는 php의 클로저 클래스를 타입으로 하는 closure 객체이다. 이는 $request를 클로저로 받아서 뭔가 처리를 한다는 의미를 가지고 있다.
```
$this->middleware(function ($request, $next) {
    // ...
    return $next($request);
});
```


## 리소스 컨트롤러
### CRUD
- CURD란? 
> 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 묶어서 일컫는 말이다. 
> - wikipedia -

### 라라벨 CURD의 메서드 종류
- CRUD 메서드는 여러가지 만들 수 있지만, 가장 기본적인 CURD 메서드를 묶어서 제공하는 기능도 존재한다. 컨트롤러의 가장 기본적인 CURD 메서드는 index, create, store, show, edit, update, destroy이다.

#### 기본적인 CURD 메서드
- Create : create, store
- Update : edit, update
- Read : index, show
- Delete : destroy

### 라라벨에서 기본적인 CURD를 제공하는 방식
- 'index, create, store, show, edit, update, destroy'가 포함된 컨트롤러 생성
```
php artisan make:controller PhotoController --resource
```
- `app/Http/Controllers/PhotoController.php` 경로에 컨트롤러 파일을 생성한다.
- 이렇게 생성된 컨트롤러를 리소스 컨트롤러라고 한다.
```
Route::resource('photos', PhotoController::class);
```
- 첫 번째 인자는 메서드명을 문자열로 넣었다.
- 리소스 컨트롤러는 라우터에 등록할 때 resource 정적 메서드를 사용해서 등록한다.
-  resource 정적 메서드를 사용할 때, 컨트롤러의 메서드를 지정할 필요 없이 컨트롤러 클래스의 이름만 지정해 주면 된다. `PhotoController::class`
- 기본적인 CURD 메서드 이외에 추가적인 메서드를 `Route::resource`에 추가 할 때 첫 번째 인자를 배열로 지정한다.
```
Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

### 기본적인 CURD가 존재하는 이유
- CURD는 어플리케이션을 동작 시키기 위한 기본적인 것이다. 하지만 어플리케이션의 구현에 따라서 항상 'index, create, store, show, edit, update, destroy'이런 메서드와 일치하는 명칭의 메소드를 만들지 못 할 수도 있다. 물론 그럴 때는 커스텀의 메서드 명칭을 추가해 주는 것이 맞다. 위의 메서드 명칭에 맞게 만들 수 있다면 좋겠지만, 컨트롤러를 하나의 비즈니스 로직 그룹으로 하고 하나의 컨트롤러에 너무 많은 기능을 넣지 말고 분산하라는 의미를 가진 것이다.
- 예를 들어 A, B 테이블이 존재하고 A 테이블의 리스트를 뽑는 로직과 B 테이블의 리스트를 뽑는 로직을 하나의 컨트롤러에 담지 말라는 의미이다. 곧 하나의 컨트롤러 클래스에 index를 두 가지 만들지 말라는 의미이다. 역할을 나누는 것, 곧 책임의 분산이 필요하다.
- 컨트롤러의 비대함을 막기 위해서 가능하면 'index, create, store, show, edit, update, destroy' 위주로 사용하며 컨트롤러가 비대해 지지 않기 위해서 메서드의 양을 줄여야 한다. 물론 기본적인 CURD 이외의 역할로 추가되는 메서드가 필요한 경우가 있다. 그런 경우에는 추가를 해 줘야 한다.



---

## Reference
https://laravel.kr/docs/8.x/controllers
