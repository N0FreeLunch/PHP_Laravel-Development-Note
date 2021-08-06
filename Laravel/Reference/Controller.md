# 컨트롤러

## 컨트롤러 : 리퀘스트의 비즈니스 처리 로직이 들어가는 곳
### 비즈니스 로직의 처리를 하지 않는 부분
- 라우터 : 라라벨에서 리퀘스트에 대한에 처리 로직을 작성하는 곳은 처음에 라우터(routes.php) 부터 시작을 한다. 하지만 라우터에는 라우팅(PATH 분류)에 관한 기능만 모아두고 세부적인 리퀘스트에 대한 처리는 컨트롤러에게 맡긴다.
- 미들웨어 : 물론 라우터를 거치기 전에 미들웨어를 라우터 이전에 달아 줄 수 있지만, 미들 웨어는 비즈니스 로직에 대한 처리를 하는 곳은 아니다.
- 부트스트래핑 : 부트스트레핑 과정은 리퀘스트 객체를 사용하기 이전에 라라벨 프레임워크에서 기본적으로 세팅 되어야 할 기능을 세팅하는 곳이기 때문에 리퀘스트에 대한 처리를 하는 곳은 아니다.

### 비즈니스 로직의 처리를 하는 부분
- 클래스라는 것은 단일 책임의 원칙을 가져야 한다.
- 컨트롤러로 만드는 클래스의 경우, 단일 책임을 지고 있지 않다. 컨트롤러에서 클래스의 메소드는 각 리퀘스트에 대한 처리를 하는 담당하고 있다. 어떤 하나의 기능에 대한 자체 클래스의 메서드의 조합으로 동작시키지 않는 것이다. 컨트롤러에서 클래스의 메소드는 독립 된 단위를 가지고 있다. 단지 비즈니스 로직의 의미론적인 한 단위, 아니면 디비의 데이터 처리에 관한 로직인 CURD를 모아 놓은 한 단위로서 구성이 된다.
- 보통 CURD의 구성은 하나의 테이블에 대한 CURD를 처리하는 것이다. 그리고 컨트롤러의 메서드도 각 테이블의 CURD에 대한 처리를 담당하도록 구성하는 것이 기본적이다. 하지만 보안적인 요인으로 인해서 
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
- 개념적인 클로저와 언어적인 클로저를 구분해야 한다. 개념적인 클로저는 함수의 리턴 값을 함수로 리턴을 할 때, 리턴 된 값이 리턴 하기 전의 변수를 기억하는 것을 의미한다. 하지만 php에서는 함수를 리턴할 때 함수가 리턴된다면 이 함수는 Closure 클래스 타입을 가지게 된다. PHP에서 함수라는 것을 인자로 받기 위해서 사용하는 방식은 클래스를 인자로 받는 방식에서 Closure 타입의 클래스를 받는 방식으로 이뤄져 있다. 
- php 메뉴얼을 보면 Closure를 다음과 같이 설명하고 있다.
> Class used to represent anonymous functions.
- 익명함수를 나타내는데 사용하는 클래스라고 설명이 되어 있다.
- 함수의 인자로 클래스를 받는 경우는 클래스를 객체화 하여 집어 넣어 줘야 하는데, 익명함수가 하나의 객체화 되는 것이다.
- 아래의 미들웨어 함수 안에 익명함수를 사용하는데 이것이 Closure 타입의 클래스를 집어 넣은 것이다.
```
$this->middleware(function ($request, $next) {
    // ...
    return $next($request);
});
```


## 리소스 컨트롤러
### CRUD
- CURD란? 
> 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 묶어서 일컫는 말이다.  \- wikipedia \-

### 라라벨 CURD의 메서드 종류
- CRUD 메서드는 여러가지 만들 수 있지만, 가장 기본적인 CURD 메서드를 묶어서 제공하는 기능도 존재한다. 컨트롤러의 가장 기본적인 CURD 메서드는 index, create, store, show, edit, update, destroy이다.

#### 기본적인 CURD 메서드
- Create : store
- Update : update
- Read : index, show, create, edit
- Delete : destroy

####  create, edit가 Read에 속하는 이유
- create는 데이터를 생성하는 엑션이 아닌, 데이터를 생성하기 위한 페이지를 제공한다는 의미를 가지고 있다.
- edit는 데이터를 업데이트 하는 엑션이 아닌, 데이터를 변경하기 위한 페이지를 제공한다는 의미를 가지고 있다.

### 라라벨에서 기본적인 CURD를 제공하는 방식
- 'index, create, store, show, edit, update, destroy'가 포함된 컨트롤러 생성
```
php artisan make:controller PhotoController --resource
```
- `app/Http/Controllers/PhotoController.php` 경로에 컨트롤러 파일을 생성한다.
- 이렇게 생성된 컨트롤러를 리소스 컨트롤러라고 한다.

### 리소스 라우터 사용하기
- `routes/web.php` 또는 `routes/api.php` 경로에서 다음을 정의 한다.
```
Route::resource('photos', PhotoController::class);
```
- 첫 번째 인자는 메서드명을 문자열로 넣었다.
- 리소스 컨트롤러는 라우터에 등록할 때 resource 정적 메서드를 사용해서 등록한다.
-  resource 정적 메서드를 사용할 때, 컨트롤러의 메서드를 지정할 필요 없이 컨트롤러 클래스의 이름만 지정해 주면 된다. `PhotoController::class`

### 커스텀 엑션 추가
- 기본적인 CURD 메서드 이외에 추가적인 메서드를 `Route::resource`에 추가 할 때 첫 번째 인자를 배열로 지정한다.
```
Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

### 컨트롤러의 기본 공통 메소드를 정의한 이유
- 라라벨 컨트롤러에서 리소스 컨트롤러를 만들어 기본 공통 메소드를 정의한 이유를 추측하면
- 라라벨의 기본 CURD를 나타내는 컨트롤러의 메서드 명칭은 'index, create, store, show, edit, update, destroy'이다. 어플리케이션의 구현에 따라서 컨트롤러의 메서드 명칭은 'index, create, store, show, edit, update, destroy' 뿐만 아닌 다른 메서드 명도 만들어 질 수 있다. 
- 에를 들어 post 통신이며 URL 파라메터가 없는 경우는 저장하는 로직으로 주로 이용되기 때문에 store를 컨트롤러의 메소드로 사용한다. 하지만 저장하는 로직이 아니라면 굳이 store라는 컨트롤러 메소드 명을 쓸 필요 없으며, 로직에 맞는 메소드 명을 써야 한다. 
- 위와 같이 컨트롤러에 메소드 명을 정해 놓은 것은, 컨트롤러를 하나의 비즈니스 로직 그룹으로 하고 하나의 컨트롤러에 너무 많은 기능을 넣지 말고 분산하라는 의미를 가진 의도를 가진 것이다. 컨트롤러의 비대함을 막는 의도를 가진 것이다.
- 예를 들어 A, B 테이블이 존재하고 A 테이블의 리스트를 뽑는 로직과 B 테이블의 리스트를 뽑는 로직을 하나의 컨트롤러에 담지 말라는 의미이다. 곧 하나의 컨트롤러 클래스에 index를 두 가지 만들지 말라는 의미이다. 역할을 나누는 것, 곧 책임의 분산이 필요하다.
- 컨트롤러는 책임의 분산에 따라 여러 컨트롤러를 만들며 메서드는 각 컨트롤러의 동작에 관한 부분이며, 하나의 개념적 단위에 대한 여러 동작으로 구성되어 있다.  그런데 여러 컨트롤러 메서드의 개념에 관해서는 공통으로 사용되는 개념이 많이 있다. 이 때 개념이란 index는 리스트를 보여달라는 것이며, update는  의미이며, store 저장한다는 개념을 의미한다. 이런 동작은 많은 컨트롤러가 가질 수 밖에 없는 것이고 이 메서드명을 일치시키는 것이 어플리케이션을 통제하기 유리하다.
- 라라벨에서 컨트롤러의 기본 CURD 구현 메서드 'index, create, store, show, edit, update, destroy'가 존재하는 것은 메서드명을 일치시키는 것이 좋다는 의도를 담고 있다. 꼭 'index, create, store, show, edit, update, destroy' 명의 메서드가 아닌 메서드라도 여러 컨트롤러에 공통적으로 사용하는 개념의 메서드라면 메서드 명칭을 일치 시켜주자는 의도를 담은 부분이다.

### 리소스풀 컨트롤러에 의해서 구성된 액션

| Verb       | URI                  | Action        | Route Name    |
| ---------- | -------------------- | ------------- | ------------- |
| GET        | /photos              | index         | photos.index  |
| GET        | /photos/create       | create        | photos.create |
| POST       | /photos              | store         | photos.store  |
| GET        | /photos/{photo}      | show          | photos.show   |
| GET        | /photos/{photo}/edit | edit          | photos.edit   |
| PUT/PATCH  | /photos/{photo}      | update        | photos.update |

- 라라벨에서 컨드롤러의 메서드는 클래스의 메서드로 구성되어 있다. 
- 클래스의 메소드는 클래스/객체의 행동(action)을 정의한다. 컨트롤러의 메소드를 말할 때는 메소드라고 보통 말하지 않고 action이라는 표현을 사용한다. 
- 메소드 : 클래스의 동작(action)을 정의 하는 것이다. 클래스는 하나의 대상이며, 멤버라는 속성을 가지고 있다. 그리고 속성은 메서드라는 엑션에 의해서 변화되는 대상이다. 하지만 컨트롤러의 클래스는 단일 책임을 가진 대상이 아니기 때문에 메서드 엑션에 의해 속성이 변하는 방식으로 보통 만들지 않는다.
- 메서드는 객체의 속성을 변경하기 위한 방식으로 말을 하기 때문에 형태적으로는 메서드이지만 컨트롤러의 메서드를 메서드 보다는 엑션으로 부르는 것 같다.  
- Verb와 URI 구성 방식에 따라 엑션이 결정되고 있는 것을 알 수 있다. 이런 패턴으로 엑션을 구성 해 주면 된다.


### Resource 라우트의 일부만 지정하기
- resource 라우트를 선언할 때, 사용할 엑션을 지정할 수도 있으며 사용하지 않을 엑션을 배제할 수도 있다.
- 컨트롤러의 커스텀 메서드는 resource 라우트로 only, except 메소드를 사용할 수 없다.
- 지정/배제가 가능한 것은 'index, create, store, show, edit, update, destroy'뿐이다.

#### 사용할 엑션만 지정
```
Route::resource('photos', PhotoController::class)->only([
    'index', 'show'
]);
```

#### 사용하지 않을 엑션을 배제
```
Route::resource('photos', PhotoController::class)->except([
    'create', 'store', 'update', 'destroy'
]);
```

## API 리소스 라우트
- 앞서의 일반 리소스 라우트의 경우 'index, create, store, show, edit, update, destroy'로 엑션이 구성되어 있었다. 하지만 API 리소스 라우트의 경우에는 create, edit 같은 엑션이 필요가 없다.
- create는 store를 하기 위한 페이지를 제공하는 엑션이며, edit는 update를 하기 위한 화면을 제공하는 역할을 한다. API 통신을 사용할 때는 페이지를 제공하지 않고 데이터만 제공을 한다. 데이터를 제공할 때 API 통신은 Index와 show로 데이터를 제공 받을 수 있다. create, edit은 이를 작성할 페이지를 프론트에 전달하고 API 통신은 페이지를 제공하지 않기 때문에 create, edit 엑션을 기본적으로 제공하지 않는다.
- API 리소스 라우터는 'index, store, show, update, destroy'만을 제공한다.

### API 리소스 라우터 사용하기
- `routes/web.php` 또는 `routes/api.php` 경로에서 다음을 정의 한다.
```
Route::apiResource('photos', PhotoController::class);
```

### API 리소스 라우터에 커스텀 엑션 지정하기
```
Route::apiResources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

### 컨트롤러에 API 리소스 엑션을 포함하여 생성하기
```
php artisan make:controller API/PhotoController --api
```

## 라우트 PATH
- 중첩된 라우터 등등에 관해서는 나중에 

## 의존성 주입 & 컨트롤러
- 컨트롤러에서는 생성자 주입을 하는 코드를 직접 사용하지 않는다.
- 라라벨의 IOC에 의해 자동으로 라우터에 의해 컨트롤러가 실행이 될 때, 자동으로 의존성을 주입하여 라우터의 엑션을 실행한다.
- 이 때 라라벨의 IOC에 의한 자동 의존성 주입을 위해서 생성자나 메서드에 타입힌트를 사용하여 주입할 클래스를 지정 해 줘야 한다. 
- 라라벨에서 사용되는 객체에 의존성 주입을 할 때 IOC 방식의 동작으로 자동으로 의존성 주입을 하는 코드를 만들 수 있다. 직접 `new SampleClass(InjectedObject)`를 사용하지도 않고 서도 의존성 주입이 되게 하기 위해 라라벨에서는 서비스 컨테이너를 통해 의존성을 자동 주입할 수 있다. 
- 일반적으로 의존성을 주입하기 위해서는 `app\Providers` 폴더의 서비스 프로바이더에 자동 주입을 하기 위한 설정을 해 줘야 한다. 컨트롤러에 대한 IOC에 의한 의존성 주입은 이 부분에 저의되어 있지는 않은 것으로 보이며, 라라벨 프로젝트에서 제공하는 것으로 보인다. (이 부분에 대해서는 더 정확하게 조사 후 추가)
- 라라벨 프레임워크는 컨트롤러에 자동으로 의존성 주입을 한다. 컨트롤러가 `new SampleConatoller(InjectedObject)` 이런 코드 없이 의존성이 되는 이유이다. 이 코드에서 컨트롤러에 InjectedObject을 주입해야 하는데, 직접적으로 주입하는 코드를 사용하지 않을 경우 InjectedObject 오브젝트를 넣어주지 않는데 이를 자동으로 넣기 위해서 컨트롤러의 생성자나 메소드에 주입할 오브젝트의 클래스를 타입힌트로 하는 매개 변수를 적는다.

```
<?php

namespace App\Http\Controllers;

use App\Repositories\UserRepository;

class UserController extends Controller
{
    /**
     * The user repository instance.
     */
    protected $users;

    /**
     * Create a new controller instance.
     *
     * @param  UserRepository  $users
     * @return void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }
}
```
- 생성자의 매개변수로 UserRepository 클래스를 타입힌트로 하는 변수 $users를 만들어 줬다.
- 런타임에 IOC에 의해 $users에는 UserRepository클래스로 만들어진 오브젝트가 들어가게 된다.
> 라라벨 contract의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다.
- 위 내용에 대해서 내용을 좀 더 추가하자.

### 컨트롤러 메서드에 의존성 주입
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Store a new user.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $name = $request->name;

        //
    }
}
```
- 컨트롤러에서 리퀘스트에 대한 정보를 `Illuminate\Http\Request` 객체를 통해서 사용한다. 생성자 주입과 마찬가지로 메서드의 생성자 주입도 클래스를 타입힌트로 한 방식으로 사용할 수 있다. 
- 인스턴스(객체)를 주입하는 방식 뿐만 아니라, 클래스가 아닌 다른 매개변수를 사용하면 라우트 메서드를 통해서 인자를 라우터 파라메터 값을 받을 수 있다.

```
Route::put('user/{id}', [UserController::class, 'update']);
```
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Update the given user.
     *
     * @param  Request  $request
     * @param  string  $id
     * @return Response
     */
    public function update(Request $request, $id)
    {
        //
    }
}
```
- 라우터 파라메터인 {id}의 id와 같은 명치의 파라메터를 컨트롤러의 메서드에 넣어 줘야 한다.


## 라우트 캐시
- 라라벨의 라우트를 캐싱할 수 있다.
- 라우트를 캐시한다는 것은 라우트의 로직을 파일 드라이브나 레디스 드라이브 등의 캐시 저장소를 사용해서 라우터 php 파일을 실행하는 방식을 사용하지 않고 저장된 로직을 사용하겠다는 의미이다. 
- 라우터의 로직이 캐싱이 되어 버리면 똑같은 결과를 가진다. 그래서 리퀘스트 요청이 올 때마다 다른 결과를 가지는 로직이 들어가 버리면 캐싱이 되어버리면서 다르게 나와야 하는 결과를 동일한 결과로 주는 현상이 발생한다.
- 그래서 캐싱을 할 때, 리퀘스트에 따라 결과 값이 달라지는 경우에는 캐싱을 사용하면 안 된다.
> 애플리케이션이 컨트롤러 기반의 라우트만을 사용하고 있다면 라라벨의 라우트를 캐시하는 장점을 사용해야 합니다.
- 이렇게 나와 있는데, 라우터가 컨트롤러와 연결하는 역할만 하고 있다면 라우터의 상태는 연결하는 동일한 로직을 가지고 있기 때문에 캐싱을 해도 문제가 없다. 그래서 캐싱을 해서 속도를 올리라는 의미이다.
- 이론적으로 볼 때는 라우터가 컨트롤러와 연결하는 역할 뿐만 아니라 리퀘스트에 따른 결과 변화 상태변화가 없는 로직이라면 라우터에 로직을 넣어서 캐싱을 할 수도 있는 것이다.

#### 라우터 캐시를 생성하는 명령어
```
php artisan route:cache
```

#### 라우터 캐시를 제거하는 명령어
- 라우트 캐시를 리프레시하는 것이 아니다.
```
php artisan route:clear
```

#### 라우터 캐시를 리프레시 하기 위한 명령어
- 캐시를 제거하고 다시 생성한다.
```
php artisan route:clear
php artisan route:cache
```


---

## Reference
https://laravel.kr/docs/8.x/controllers
