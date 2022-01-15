# Error Handling

## 에러 로그
- 라라벨에서 특별한 에러 처리를 하지 않더라도 에러가 발생하면 로그에 에러를 남긴다. 
- 라라벨에서 에러가 발생했을 때 기본적으로 로그는 `storage\logs\laravel.log`에 저장이 된다.

## 에러 표시
- 로그를 남기는 것과 달리 에러가 발생하면 리스폰스에 에러를 보여주는 기능을 라라벨은 기본적으로 제공한다.
- 이 기능은 `.env`파일의 `APP_DEBUG=true`옵션(환경 변수라고 한다.) 을 설정하면 리스폰스에 에러 내용을 보내며, `APP_DEBUG=false`를 설정하면 리스폰스에 에러를 담지 않고 숨긴다. 프로덕션 환경에서는 반드시 `APP_DEBUG=false`를 설정해 줘야 한다. 

## 예외 처리 핸들링을 위한 클래스
- 라라벨에서 발생하는 모든 에러는 하나의 핸들링 클래스를 거친다.
- 핸들러 클래스는 `App\Exceptions\Handler.php`에 정의 되어 있다.

## 예외 헨들러
```
use App\Exceptions\CustomException;

/**
 * Register the exception handling callbacks for the application.
 *
 * @return void
 */
public function register()
{
    $this->reportable(function (CustomException $e) {
        //
    });
}
```
- 모든 예외는 `App\Exceptions\Handler` 클래스에 의해서 처리된다.
- 커스텀 예외 리포터와 렌더러 콜백을 등록할 수 있는 register 메소드가 있다.
- 기본적으로 예외는 라라벨의 logging 설정에 의해 기록이 되지만 로그를 남기는 조건을 커스텀할 수 있다.

#### 렌더러 콜백 등록
- `$this->reportable()`안의 다음 함수
```
function (CustomException $e) {
        //
}
```
- 에러가 발생했을 때, 렌더러 콜백 내에 지정한 종류의 커스텀 예외 클래스에 해당하는 에러가 발생했을 때 처리할 로직을 적어 준다.

#### 예외 리포터
```
```

## 글로벌 로그 컨텍스트
- 라라벨의 기본 에러 로그에 커스텀으로 정의된 로그를 기록하기 위해 사용하는 기능이다.
- `App\Exceptions\Handler`클래스에 다음 메소드를 추가한다. (오버라이딩 됨)
- 글로벌 로그 컨텍스트를 사용하면 애플리케이션에서 기록되는 모든 exception 로그에 정의한 내용을 포함하여 기록한다.
```
/**
 * Get the default context variables for logging.
 *
 * @return array
 */
protected function context()
{
    return array_merge(parent::context(), [
        'foo' => 'bar',
    ]);
}
```

## report helper 함수
- 에러가 생기면 php 프로세스(PHP 엔진에서는 RINIT라고 부름)는 에러를 내 보내고 종료하게 된다.
- 라라벨은 php 프로세스가 종료되기 전에 라라벨의 에러 처리 로직을 거처서 내 보내도록 되어 있다.
- 예외처리(try ... catch ...) 구문을 사용하면 에러가 발생해도 RINIT가 종료되지 않지만, 종료되는 에러가 아니기 때문에 에러가 자동 로깅되지 않는 문제가 발생한다.
- php 프로세스를 종료시키지 않고 에러를 로깅하기 위해서는 예외처리 구문을 사용하고 에러 메시지는 로그로 남기는 처리를 해 주면 된다.
- 에러가 발생해서 RINIT가 종료되는 것이 아니기 때문에 에러 페이지를 표시하지 않는다.

```
public function isValid($value)
{
    try {
        // Validate the value...
    } catch (Throwable $e) {
        report($e);

        return false;
    }
}
```
- try ... catch ... 구문을 사용하여 에러가 발생해도 종료되지 않게 만든다. 
- report 헬퍼 함수를 사용하여 에러 객체 $e를 인자에 넣어주면 에러가 로깅된다.
- RINIT이 종료되지 않고 php 인터프린팅이 계속 되기 때문에 에러가 발생했을 때의 처리 로직을 넣어 준다.
- isValid 메서드이기 때문에 반환값을 bool 타입으로 해 준 것을 알 수 있으며 에러가 발생 했을 대 false를 반환해 주기 위해서 catch 구문 안에 `return false;`를 써 주었다.

```
abort(404);
```

## 유형에 따라서 예외 exception 무시하기


## Reference
- https://laravel.kr/docs/8.x/errors

