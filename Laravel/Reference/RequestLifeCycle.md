# 라이프 사이클

### 라이프 사이클이란?
- 어플리케이션의 시작과 종료의 흐름에서 반드시 실행되는 부분들로 이들 기능과 처리 흐름에 대한 설명을 라이프 사이클이라고 한다.

### 라라벨의 모든 요청의 시작점
- '라라벨 프로젝트 폴더/public/index.php'가 시작점이다.
- HTTP 리퀘스트는 기본적으로 요청한 파일을 리스폰스로 보내주는 역할을 하며 특별한 설정이 되어 있지 않다면, 요청한 URL의 PATH 부분에 해당하는 파일을 리스폰스로 제공한다.
- 실서버에는 nginx, apache 등을 통해서 public 폴더만 공개하도록 설정한다.
- 라라벨의 public 폴더만 공개가 되므로, public 폴더 내의 파일은 URL의 PATH를 통해 접속할 수 있지만 public 파일 밖의 프로젝트 파일 및 폴더는 URL PATH로 접근할 수 없다. (public 폴더에서 PATH로 접근할 수 없는 파일은 라라벨 라우터에 정의된 값을 사용하도록 연결된다.)

### public/index.php 내부 실행
```
// ...(생략)...
require __DIR__.'/../vendor/autoload.php';
$app = require_once __DIR__.'/../bootstrap/app.php';
$kernel = $app->make(Kernel::class);
$response = tap($kernel->handle(
    $request = Request::capture()
))->send();
$kernel->terminate($request, $response);
```
- 컴포저가 생성 한 오토로더 정의를 로딩
- '라라벨 프로젝트 폴더bootstrap/app.php' 파일을 호출하여 라라벨에 프레임워크를 로딩하는데 필요한 객체들을 호출한다.
- '라라벨 프로젝트 폴더bootstrap/app.php'에 의해 가장 먼저 호출되는 부분은 서비스 컨테이너의 객체를 호출한다. (서비스컨테이너는 라라벨 프레임워크의 의존성을 호출하는 부분)
- 오토로더 -> bootstrap/app.php -> kernel -> response -> terminate 순으로 호출된다.


### HTTP/console 커널
HTTP 커널
---------
- app/Http/Kernel.php
- Illuminate\Foundation\Http\Kernel을 상속 받아 정의 됨
- 라라벨 프레임워크로 리퀘스트를 처리하기 위해 기본적으로 로드할 것들을 배열로 정의 하는 곳.
- 라라벨 프레임워크 시작 시 실행할 목록들을 bootstrappers라고 하며 라라벨 커널 부분에 정의한다.
- 라라벨 프레임워크는 리퀘스트를 처리하기 위한 동작을 정의하는 곳이므로 리퀘스트 처리를 위해 미리 세팅해야 할 기본 동작들을 로드하는 곳이라고 할 수 있다.
- 기능적으로 중요한 점은 미들웨어를 정의하는 곳이라는 것이다. 
console 커널
-----------
- 관련 정보 습득시 추가

#### HTTP 커널의 handle 메소드
https://laravel.com/api/8.x/Illuminate/Contracts/Http/Kernel.html
- handle 함수를 오버라이딩해서 사용할 수 있음
```
public function handle (Request $request)
{
    // code
    return $response;
}
```

### 서비스 프로바이더
- config/app.php 파일의 providers에 사용할 서비스 프로바이더들을 불러 온다.
- 서비스 프로바이더란 라라벨 프레임워크를 시작할 때, 시작하기 위한 설정 코드, 기능들을 모아 둔 곳으로 커널은 HTTP 리퀘스트의 흐름을 제어하는 역할을 한 것에 반해, 서비스 프로바이더는 라라벨 프레임워크에서 제공할 기본 서비스에 대한 정의를 하는 부분이다. 이 기본 서비스의 종류에는 프레임워크의 데이터베이스, 큐, validation, 라우팅 컴포넌트등 리퀘스트에 대한 처리를 전문적으로 담당하는 커널과 달리 서비스 전반적으로 공통적으로 사용할 것들을 로딩하는 곳이다.
- 호출 순서 : 모든 서비스 프로바이더의 register 메소드가 호출 => 모든 서비스 프로바이더의 boot 메소드가 호출
- app/Providers에는 라라벨 디폴트 프로바이더가 정의되어 있음
- AppServiceProvider에는 커스텀으로 제작한 프로바이더를 넣는 곳

#### register와 boot를 나눈 이유?
- 서비스프로바이더는 라라벨 프레임워크에서 제공하는 다양한 기능들을 등록하는 곳이다.
- 그런데 미리 선행적으로 뭔가가 등록이 된 이후 실행해야 하는 기능들이 있다.
- register에는 미리 선행적으로 등록이 되는 부분으로 각각의 기능들이 다른 서비스프로바이더 기능과 의존적이지 않고 독립적인 대상들이 등록되는 곳이며 boot에는 register에 등록된 기능에 의존하는 대상들이 등록되어 실행되는 곳이다.

### 디스패칭
- 디스패칭은 기능을 실행 컨텍스트 상에서 바인딩하는 것을 의미한다.
- 라라벨에서 디스패칭이 되었다는 의미는 애플리케이션이 부팅(부트스트래핑)이 이뤄지고, 모든 서비스 프로바이더가 등록되고, 리퀘스트가 라우터에 전달되고, 라우터의 리퀘스트가 각 라우터의 정의에 따라 리퀘스트가 처리 된 상태이다. 곧 리퀘스트 정보에 관한 처리를 세부적으로 처리하는 부분을 시작하기 전 단계까지. 곧 컨트롤러에서 리퀘스트를 처리하는 것이라고 할 수 있다.
- 라라벨 프레임워크의 부트스트랩 과정이 완료된 시점을 디스패칭이라고 할 수 있다.


---

Reference : https://laravel.kr/docs/8.x/lifecycle


descripted by N0FreeLunch

