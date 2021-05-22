# 라이프 사이클

### 왜 라이프 사이클이라고 부르는가?


### 라라벨의 모든 요청의 시작점
- '라라벨 프로젝트 폴더/public/index.php'가 시작점이다.
- HTTP 리퀘스트는 기본적으로 요청한 파일을 리스폰스로 보내주는 역할을 하며 특별한 설정이 되어 있지 않다면, 요청한 URL의 PATH 부분에 해당하는 파일을 리스폰스로 제공한다.
- 실서버에는 nginx, apache 등을 통해서 public 폴더만 공개하도록 설정한다.
- 라라벨의 public 폴더만 공개가 되므로, public 폴더 내의 파일은 URL의 PATH를 통해 접속할 수 있지만 public 파일 밖의 프로젝트 파일 및 폴더는 URL PATH로 접근할 수 없다. (public 폴더에서 PATH로 접근할 수 없는 파일은 라라벨 라우터에 정의된 값을 사용하도록 연결된다.)

### public/index.php 내부 실행
```
...(생략)...
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
- 오토로더 -> bootstrap/app.php -> kernel -> response -> terminate 순으로 호출된다. 각각에 대해 살펴 보자.


### HTTP/console 커널 
-  app/Http/Kernel.php
-
-



---

Reference : https://laravel.kr/docs/8.x/lifecycle


descripted by N0FreeLunch

