
# 서비스 컨테이너
- 클래스의 의존성을 관리하고 의존성을 주입하는 강력한 도구

```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Repositories\UserRepository;
use App\Models\User;

class UserController extends Controller
{
    /**
     * The user repository implementation.
     *
     * @var UserRepository
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

    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return Response
     */
    public function show($id)
    {
        $user = $this->users->find($id);

        return view('user.profile', ['user' => $user]);
    }
}
```
### 모델
- 디비의 데이터를 조회, 수정, 삭제, 삽입하는 기능을 담당하는 부분

### 도메인
- 예를 들어 신선식품을 배달한다고 하자. 유저는 웹 사이트를 검색, 상품 보기, 장바구니, 주문, 결제, 배송 등의 기능을 사용하며, 유저의 불편을 처리하는 고객센터 운영으로 유저의 결제 취소, 환불, 반품 등의 절차를 수행하는 부분, 식품을 배달하기 위한 물류 시스템으로 배차 등등등... 무수히 많은 비즈니스적인 처리 단위가 존재한다. 이를 도메인이라고 한다.

### 레포지토리
- 레포지토리는 모델을 사용하여 도메인을 구성하는데 필요한 비즈니스를 구성하기 위한 부분으로 하나의 기능 하나의 단위 개념이다. 데이터의 조회, 수정, 삭제, 삽입을 구현한 것을 뜻한다. 예를 들어 검색이면 하나의 테이블이 아닌 여러 테이블의 조인하여 검색 키워드가 포함된 일부 결과를 검색 된 결과를 보여주는 것으로 검색이라는 도메인에 필요한 기능을 정의한 것이다.

### 의존성주입
- 'App\Repositories\UserRepository' 경로에 위치
- __construct의 매개 변수로 UserRepository $users를 집어 넣었다. 타입힌트의 방식으로 UserRepository클래스를 $users변수로 집어 넣는다는 의미이다.
- 라우터는 컨트롤러를 실행한다. 그런데 라우터에는 new UserController(생성자 인자)의 방식으로 컨트롤러를 실행하는 것이 아니라 어떤 컨트롤러인지 지정하면 라라벨 프레임워크가 알아서 UserController 클래스를 객체로 호출하라는 기능으로 되어 있다. 이때 라라벨 프레임워크는 
- 프레임워크가 의존성 주입을 하는 이유는 UserController 객체와 UserRepository의 결합도를 낮추기 위한 것. UserController 내부에서 new 키워드를 사용하여 호출하면 UserController는 UserRepository 없이는 사용할 수 없다. UserController만 태스트를 하기 위해서는 UserRepository를 객체를 생성할 때 생성자 인자로 주입하면 UserRepository를 대신하는 어떤 태스트 모듈을 UserController를 호출할 때 넣을 수 있다. 이를 UserRepository가 주입되었다고 말한다.
- UserController가 생성될 때 의존하는 대상인 UserRepository를 주입하기 때문에 의존성 주입이라고 한다.
- 이렇게 의존성 주입을 하면 애플리케이션을 테스트할 때 손쉽게 "목킹" 하거나 더미 UserRepository 구현체를 생성하여 UserController를 사용할 수 있다.
- 의존성 주입은 객체를 생성할 때 뿐만 아니라, setter 메서드 곧 맴버 변수에 무언가를 할당하는 메서드에 의해서도 만들 수 있다. 예를 들어
```
  public function updateUser(UserRepository $users)
  {
    // ...(생략)...
    $result = $this->users->changeName($username);
    // ...(생략)...
  }
```
- 이렇게 메서드에도 매개변수로 의존 대상을 받게하여 메서드를 실행할 때 주입해서 UserRepository를 받게 하는 기능이다.

### 리플렉션
- https://www.php.net/manual/en/book.reflection.php php의 리플렉션 API를 사용하여 라라벨은 의존성을 자동적으로 바인딩한다.
- 객체를 주입하는 방식이라면 리플랙션에 의해서 자동으로 의존성이 해결되기 때문에 라라벨에서 바인딩을 할 필요가 없다. 하지만, 인터페이스를 주입하는 방식으로 의존성 주입을 만들 수 있는데, 인터페이스를 주입하는 방식이라면 리플렉션에 의해 자동 바인딩이 되지않기 때문에 서비스 프로바이더에 주입할 대상을 올려 놓고, 라라벨 부트스트레핑 과정에서 주입할 대상을 미리 받아서 의존성 주입을 한다.
- https://stackoverflow.com/questions/47200527/how-does-reflection-in-laravel-work 부분을 참고하여 내용을 정리하자.
- 라라벨의 제작자인 otwell은 php의 리플렉션을 통해 라라벨 제어의 역전(IOC)을 구상하려고 했었다.

### 인터페이스 주입
- 인터페이스 주입은 왜 하는 것인가? (https://youtu.be/C6nsjqrCJq4 를 참고해서 내용을 보충하자.)


### 바인딩
- 컨트롤러를 호출할 때는 개발자의 코드레벨에서는 new UserController로 호출하지 않는다. 프레임워크가 라우터에서 지정한 컨트롤러 클래스를 객체화 하면서 알아서 UserRepository를 주입한다.


### 간단한 바인딩
- app\Providers\AppServiceProvider.php 파일의 boot 또는 register 메서드 안에서 바인딩할 대상을 작성한다.
- boot와 register에 관해서는 [RequestLifeCycle](./RequestLifeCycle.md) 부분을 참고하자.
- 바인딩하는 방법은 버전별로 차이가 많이 나는 것으로 보인다. 버전별 사용방법을 알기 위해서 영문 레퍼런스를 참고해야 한다. 여기서는 8.x 버전을 기준으로 하였다.
```
use App\Services\Transistor;
use App\Services\PodcastParser;

$this->app->bind(Transistor::class, function ($app) {
    return new Transistor($app->make(PodcastParser::class));
});
```
- 위 코드는 AppServiceProvider의 boot 또는 register 메서드 안에 위치한다.
- $this->app : 라라벨 서비스 컨테이너 인스턴스에 접근하는 코드이다.
- \:\:class 라는 것은 php 문법으로 네임스페이스를 반환하는 문법이다. (참고 : https://www.php.net/manual/en/language.oop5.basic.php#language.oop5.basic.class.class)
- bind 메서드의 첫번째 인자는 서비스 컨테이너에 등록하기 위한 명칭이다. 타입은 string이다. 만일 두번째 인자로 받은 대상이 없다면, 라라벨 프로젝트에서 해당 namespace를 가진 대상을 찾아서 등록하므로, namespace와 동일한 명칭으로 사용하는 것을 권장한다. 이때 라라벨이 대상을 찾는 방식은 재귀 알고리즘으로 네임스페이스를 탐색하는 방법을 사용한다.
- ```new Transistor($app->make(PodcastParser::class))```에서 Transistor 객체는 PodcastParser 객체를 주입 받겠다는 의미이다.
- bind 메서드의 두번째 인자는 첫번째 인자 명칭에 인스턴스를 할당하는 작업을 한다. 이를 통해서 서비스컨테이너 안에 바인딩 된 대상은 첫번째 인자의 명칭을 가지며, 이 명칭의 대상으로 접근을 할 때 두번째 인자의 클로저로 등록되는 인스턴스로 연결시킨다.
- php 클로저를 받는데 익명 함수를 사용하면 이 익명함수의 매개변수로 서비스 컨테이너 인스턴스인 $app 인스턴스를 받을 수 있다. 

### make 메서드
```
use App\Services\Transistor;

$app->make(PodcastParser::class);
```
- 앱 인스턴스의 make 메서드는 주입하는 namespace를 인자로 받는다. 인스턴스를 생성하거나 메서드를 호출할 때 주입이 필요한 대상이 있다면 주입할 때 넣을 인스턴스의 클래스를 네임스페이스로 받는다. 의존성 주입으로 생성자나 메서드에 투여되는 대상이기 때문에 클래스 뿐만 아니라 인스턴스로 대상으로 지정할 수 있다.
- 서비스 컨테이너에 make를 통해서 주입 대상이 등록된다. 서비스 컨테이너는 라라벨 프레임워크의 부트스트래핑 과정의 일부 절차로 실행 되며 라라벨 프레임워크의 리퀘스트 라이프 사이클이 진행될 때 주입이 필요한 곳이 있다면 서비스 컨테이너에서 주입대상을 찾아서 필요한 인스턴스의 생성자나 메서드에 집어 넣어준다.
- php의 리플렉션으로 자동 주입이 되는데 서비스 컨테이너에 등록해서 주입을 할까? php의 리플렉션은 타입힌트를 통해서만 자동 주입을 할 수 있다. 만약 타입힌트를 사용하지 않는 대상이라면 서비스 컨테이너를 통해 의존성을 주입해야 한다.



### 싱글톤으로 바인딩하기
```
$this->app->singleton('HelpSpot\API', function ($app) {
    return new \HelpSpot\API($app->make('HttpClient'));
});
```






---
Reference : https://laravel.kr/docs/8.x/container

https://stackoverflow.com/questions/47200527/how-does-reflection-in-laravel-work

https://laracasts.com/discuss/channels/general-discussion/what-application-bind-string-to-choose


descripted by N0FreeLunch
