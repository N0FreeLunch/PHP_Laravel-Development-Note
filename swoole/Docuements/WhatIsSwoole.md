## swoole의 탄생 목적
- PHP에서 이벤트 드리븐, 비동기, 논블록킹 IO, 코루틴 모델을 제공한다. 
- 대규모의 동시성 시스템을 구축하기 위한 방법으로 도입되었기 때문에 병렬 프로그래밍을 지원한다.

## 타 비동기 라이브러리와의 차이점
- swoole는 병렬성을 지원하기 위해서 탄생했기 때문에 php 코어의 확장이 필요하다. (php의 확장 프로그램을 PECL이라고 한다.)
- Nginx, Tornado, Node.js와 비교할 때 다중 스레드 I/O 모듈(HTTP 서버 , WebSockets, TaskWorkers, Process Pools)을 사용하며, 병렬성을 위한 PDO, MySQL, Redis, CURL 라이브러리를 지원한다.
- 병렬 프로그래밍을 하기 위해서는 CGI 방식을 사용할 수 없고 CGI 방식을 사용할 수 없기 때문에 reactPHP, php AMP와 같이 컴포저 라이브러리를 추가해서 쓰는 방식으로 사용할 수 없다는 단점이 있다.
- 병렬 프로그래밍을 하기 위해서 기존의 PHP 라이브러리는 싱글스레드 방식의 라이브러리이기 때문에 병렬성을 지원하는 I/O 모듈로의 교체가 필요하다.

## 호환성
- 기존 PHP 구문으로 고성능의 확장이 가능하다.

### 유명 라이브러리 중 호환 되지 않는 것들
- Xdebug, phptrace, aop, molten, xhprof, Phalcon, BlackFire

## 알아두면 좋은 것
- Apache, Nginx 등을 프록시 서버로 두고 swoole 서버에 연결할 수도 있다.

## Reference
- https://openswoole.com/docs/
