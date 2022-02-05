```
<?php
use Swoole\Http\Server;
use Swoole\Http\Request;
use Swoole\Http\Response;

$server = new Swoole\HTTP\Server("127.0.0.1", 9501);

$server->on("start", function (Server $server) {
    echo "Swoole http server is started at http://127.0.0.1:9501\n";
});

$server->on("request", function (Request $request, Response $response) {
    $response->header("Content-Type", "text/plain");
    $response->end("Hello World\n");
});

$server->start();
```

## Swoole\Http\Server
- swoole 서버를 열기위한 객체 형성 `new Swoole\HTTP\Server("127.0.0.1", 9501);`, 인자에는 로컬의 IP와 접속할 포트 번호를 입력한다.


## on 메소드
- 서버에서 리퀘스트를 받아 오기 위해 타입 힌트로 사용하기 위한 것.
- on 함수의 인자로 사용되는 클로저에 인자 `Request $request`와 `Response $response`를 사용하였는데, on 메소드에서 $request에 값을 넣어 주고 $response에 값을 넣어 주는 방식으로 사용되는 것. 인자에 이를 직접 쓴 것은 클로저 내부에서 리퀘스트 객체와 리스폰스 객체를 사용하게 하려는 의도

## start 메소드
- 서버 세팅을 한 후, 세팅한 서버를 실행하기 위해서 쓰는 메소드


## 서버 실행 명령
```
php server.php
```
- swoole 로직이 들어 있는 php 파일을 실행하기만 하면 된다.
```
curl http://127.0.0.1:9501/
```
- url에 통신을 시도 했을 때 결과를 받아 오는지 확인
