# Facades

## 파사드란 무엇인가?
### 서비스 컨테이너
- 라라벨에서 서비스 컨테이너는 의존성을 제공하는 도구이다. IOC 제어의 역전을 구성하기 위해서 라라벨 어플리케이션이 의존성 주입을 자동으로 집어 넣게 하기 위한 도구이다.
- 파사드에서 사용하는 서비스 컨테이너의 역할은 자동으로 의존성 주입을 하는 방식이 아니라, 서비스 컨테이너에 등록한 객체를 정적 클래스 형식으로 쓰기 위한 도구이다.
- 클래스를 정적으로 불러 정적(static) 멤버를 사용하는 것이 아니라 서비스 컨테이너에 선언된 객체(object)를 정적 클래스 형식/문법으로 불러다 쓰는 것이다.
- 서비스 컨테이너에 선언되어 있는 것은 부트스트래핑 동안에 만들어지므로 부트스트래핑 과정에 이후의 어플리케이션 영역에서 파사드를 사용할 수 있다.

### 파사드
- 서비스 컨테이너에서 사용가능한 클래스들에 대한 정적 인터페이스를 제공하는 라라벨의 기능들이라고 할 수 있다.
- 일반적으로 파사드를 사용할 때는 서비스 컨테이너에서 직접 가져다 쓰는 방식이 아니라 파사드라는 중간 매개체를 통해서 서비스 컨테이너의 대상을 불러다가 쓸 수 있다. 이 때 중간 매개체를 프록시라고 부르며 정적인 클래스를 가져다 쓴다고 해서 파사드를 정적 프록시 역할을 한다고 설명한다.

### 정적 메서드
- 정적 메서드란 객체를 선언하지 않고도 클래스의 기능을 사용할 수 있도록 구성한 것을 의미한다. 
- 보통은 클래스에 정적 멤버를 구성하기 위해서 static 이란 키워드를 사용하는데 멤버가 할당되는 곳에 메모리를 확보한다는 뜻을 가진다. 그러니 객체가 런타임에 메모리를 확보하는 것과는 달리 컴파일 타임에 메모리를 확보하는 방식이다.
- 클래스의 멤버가 특정 메모리 영역을 확보하면, 해당 멤버는 객체를 선언하든 그냥 허용하든 고정된 메모리 영역을 사용하기 때문에 공유하는 특성을 가진다.

## 파사드의 장단점
### 파사드의 장점
- 파사드는 이용하기 쉽게 간결한 문법을 제공할 수 있도록 만들어져 있다.
- 태스트를 위한 가상의 파스드를 만들 수 있기 때문에 태스트를 용이하게 한다. 
- 코드의 유연성을 유지하게 한다. 복잡한 보일러 플레이트가 필요하지 않기 때문에 코드를 변경하기 쉽다.
- 의존성 주입을 사용하면 생성자가 비대해지지만 파사드는 의존성 주입을 사용하지 않기 때문에 기능이 늘어나도 코드가 어느정도 간결하다. 
- 긴 이름의 클래스 및 네임스페이스를 사용하지 않고 쉽게 사용할 수 있는 기능을 제공한다. `\Cache::`등의 방식으로 사용
- php의 다이나믹 메소드를 사용해서 태스트가 용이하다(?)

### 파사드의 단점
- 의존성 주입을 사용하면 생성자가 비대해 지는 것을 시각적으로 확인 가능하기 때문에 하나의 클래스에 많은 의존성을 집어 넣는 것을 억제하지만, 파사드는 이런 비대해지는 것에 대한 시각적 피드백이 없기 때문에 클래스에 파사드를 많이 추가하게 될 수 있다.
- 파사드는 여러 기능을 제공하는데 많은 종류의 파사드가 한 클래스 안에 있다는 것은 책임의 분산이 이뤄지지 않는 좋지 않은 코드이다.
> 따라서 파사드를 사용할 때에는, 클래스의 책임 범위를 작게 유지하도록 클래스의 사이즈를 구성하는데 특별한 주의가 필요하다

## 파사드 사용하기
- 모든 파사드는 Illuminate\Support\Facades에 존재한다.
- 파사드에서 제공하는 기능은 https://laravel.com/api/5.5/Illuminate/Support/Facades.html 을 통해 확인할 수 있다.
#### example
```
use Illuminate\Support\Facades\Cache;

Route::get('/cache', function () {
    return Cache::get('key');
});
```

## 파사드 vs 의존성 주입
### 의존성 주입
- 어떤 객체를 만들 때 이 객체가 외부의 객체를 불러다가 사용하는 경우, 생성자나 메서드의 인자를 통해서 객체를 집어넣는 경우, 객체 내에 외부 객체를 선언하는 것과는 달리 비슷한 기능을 갖는 다른 객체를 넣어서 움직이게 할 수도 있다.
>  서비스 컨테이너에 의해서 의존성이 해결되는 클래스 객체의 프록시 메소드로 다이나믹 메소드를 사용하기 때문에, 실제 주입된 클래스 인스턴스를 테스트하는 것과 마찬가지로 파사드를 테스트할 수 있습니다. 
- 이 말은 파사드의 경우에도 의존성 주입과 마찬가지로 교체라는 것을 할 수 있다.
- 이 말은 파사드의 경우에도 의존성 주입과 마찬가지로 교체라는 것을 할 수 있다는 의미이다. 교체를 할 수 있기 때문에 태스트가 가능한 것. (의미를 좀 더 구체적으로 추후 분석을 할 필요가 있다.)

### 파사드
- 파사드는 정적 클래스를 사용하는 것이며, 의존성 주입을 할 때 사용하는 유사 의존성을 제공하는 목킹(mocking) 객체나 스터브(stub : 일부 기능만을 태스트하기 위해 간략히 만든 대상)를 사용할 수 없다.
```
use Illuminate\Support\Facades\Cache;

Route::get('/cache', function () {
    return Cache::get('key');
});
```
```
use Illuminate\Support\Facades\Cache;

/**
 * A basic functional test example.
 *
 * @return void
 */
public function testBasicExample()
{
    Cache::shouldReceive('get')
         ->with('key')
         ->andReturn('value');

    $this->visit('/cache')
         ->see('value');
}
```
- ` $this->visit('/cache')` /cache라는 PATH로 접근을 한다. 앞서 정의된 라우터에 태스트 코드로 접근하고 있다.
- 파사드에 get이라는 대상이 존재하지 않는다고 가정하자. 그래서 태스트용 파사드를 하나 만들 것이다.
```
Cache::shouldReceive('get')
     ->with('key')
     ->andReturn('value');
```
- Cache 파사드에 가상의 메서드를 만든다. `Cache::get('key')`로 호출 할 수 있도록 get이라는 메서드는 'key'라는 인자를 받았을 때 'value'라는 값을 리턴한다고 가상의 파사드를 만드는 것이다.
- `$this->visit('/cache')`메서드를 통해서 `/cache` 라우터로 접근을 했을 때, `->see('value')`라는 코드는 라우터의 리턴값이 'value' 인지를 체크한다는 의미이다. 현재 라우트를 ` return Cache::get('key');`으로 정의를 했기 때문에 라우트를 실행한 결과도 캐시의 결과값으로 나와야 한다.
- 위에 꺼는 코드를 보고 해석한 추측이므로 좀 더 정확하게 분석할 필요가 있다.


### 파사드 VS 헬퍼함수
#### 헬퍼함수란?
- 헬퍼함수는 라라벨 어디에서든 사용할 수 있는 함수들을 의미한다.
- 네임스페이스를 이용한다는 등의 클래스를 불러 오는 방식이 필요가 없기 때문에 라라벨 프로젝트 어디에서든 사용할 수 있기 때문에 아주 유용한 함수이다.

#### 파사드의 헬퍼함수와의 비교
- 파사드를 호출하기 위한 네임스페이스로 접근하도록 `use Illuminate\Support\Facades\Cache`이런 코드를 작성해야 한다.
- 파사드는 단일 함수로 제공되는 것이 아니라, 객체를 정적 멤버 형태로 사용하기 때문에 메서드를 체이닝하거나 파사가 연결해 준 객체의 상태변경을 이용한 논리를 전개할 수 있다.
```
return View::make('profile');
return view('profile');
```
- 파사드를 사용한 호출과 헬퍼함수를 사용한 호출 방식은 동일하다.
- 헬퍼함수를 사용할 때도 파사드와 동일하게 태스트를 할 수 있다.
```
Route::get('/cache', function () {
    return cache('key');
});
```
```
use Illuminate\Support\Facades\Cache;

/**
 * A basic functional test example.
 *
 * @return void
 */
public function testBasicExample()
{
    Cache::shouldReceive('get')
         ->with('key')
         ->andReturn('value');

    $this->visit('/cache')
         ->see('value');
}
```
- Cache 파사드에 가상의 메서드를 만든다.
- 라우터에서는 파사드를 쓰지 않고, 헬퍼함수를 사용한다.


## 파사드의 동작 원리
- 라라벨에는 서비스 컨테이너라는 기능이 있다. 서비스 컨테이너는 의존성을 라라벨 프레임워크가 자동적으로 의존성을 주입하기 위한 도구이다.
- 자동으로 의존성 주입이 되는 대상을 수동으로 불러다 쓰기 위한 도구이다.
- 라라벨 파사드는 서비스 컨테이너에 있는 대상(객체)을 연결해 주는 역할을 한다.
- 서비스 컨테이너에 등록할 수 있다면, 유저가 작성한 것이든 라라벨에서 제공하는 것이든 파사드로 연결해서 사용할 수 있다.

### Facade class
- 파사드는 `Illuminate\Support\Facades\Facade` 클래스의 상속을 받는다.
- php의 '\_\_callStatic()'이라는 기능을 사용한다.

#### php \_\_callStatic magic method
https://www.php.net/manual/en/language.oop5.overloading.php#object.callstatic
> \_\_callStatic() is triggered when invoking inaccessible methods in a static context.
- 정적 컨텍스트 상황에서 callStatic은 액세스 할 수 없는 메서드를 호출할 때 발동된다.
- 그러니까 접근 제한자에 의해 엑세스 할 수 없거나 존재하지 않는 메서드를 호출할 때 대신 가상의 메서드를 만들어 호출하는 역할을 한다.
```
class MethodTest
{
    public static function __callStatic($name, $arguments)
    {
        // Note: value of $name is case sensitive.
        echo "Calling static method '$name' "
             . implode(', ', $arguments). "\n";
    }
}

MethodTest::runTest('in static context');
```
> Calling static method 'runTest' in static context
- 존재하지 않는 메서드를 호출했는데, 클래스에 존재하지 않는 가상의 메서드를 호출한다.

```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Cache;

class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return Response
     */
    public function showProfile($id)
    {
        $user = Cache::get('user:'.$id);

        return view('profile', ['user' => $user]);
    }
}
```
-  Cache 클래스의 get static 메소드를 호출하는 방식으로 보인다.
-  그러나 파사드는 

---

## Reference
https://laravel.kr/docs/8.x/facades


described by N0FreeLunch
