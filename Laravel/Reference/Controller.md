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
- 

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
- 보통 컨트롤러에는 여러 메서드가 들어간다.
- 그런데 단일 메서드를 가진 컨트롤러도 존재한다.
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


####
- 컨트롤러의 비대함을 막기 위해서 가능하면 index, create, store, show, edit, update, destroy 위주로 사용하며 컨트롤러가 비대해 지지 않기 위해서 메서드의 양을 줄여야 한다.


---

## Reference
https://laravel.kr/docs/8.x/controllers
