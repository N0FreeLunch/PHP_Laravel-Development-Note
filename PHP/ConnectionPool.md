## php에서의 커넥션 풀
- php에는 기본적으로 커넥션 풀링(connection pooling)이 존재하지 않는다.
- 커넥션풀 대신 '퍼스시트 커넥션'이란 개념이 존재한다.

### 커넥션 풀의 작동 원리
- 커넥션 풀링은 데이터베이스 프로그램으로 바꾼다.
- 서버측에서는 디비에 연결을 끊는다는 프로토콜을 전송하지 않는다.
- 디비와 서버는 계속 연결을 유지한다.

### php 에서 퍼시스트 커넥션
- 퍼시스트 커넥션은 커넥션 풀링과 다르다.
- 연결 오버헤드가 높으면 퍼시스트 커넥션이 상당한 도움이 된다.
- 퍼시스트 커넥션은 자식 프로세스가 SQL 서버에 연결해야 하는 페이지를 처리할 때마다가 아니라 전체 수명 동안 한 번만 연결되기 때문에 커넥션풀과 유사한 개념을 가지고 있다.
- 하지만 퍼시스트 연결(persistent connection)이 커넥션 풀링과 다른 점은 MySQL 서버 구성으로 활성화되면 MySQL은 요청된 클라이언트(php 스크립트)가 작업을 완료하고 종료된 후 연결을 열린 상태로 유지하고 유휴 상태가 된다는 점이다.
- 두 번째 요청에 동일한 자격 증명 데이터(동일한 사용자 이름, 동일한 비밀번호, 동일한 연결 매개변수, 동일한 데이터베이스 이름, 동일한 IP에서 온 것일 수도 있음, IP가 확실하지 않음)가 제공되면 MySQL은 이전 연결을 유휴 상태에서 활성 상태로 풀링합니다. 클라이언트가 연결을 사용하도록 합니다.
- 동일 자격 증명에 대한 유휴상태 활성화는 MySQL이 연결을 위한 초기 스타트 시간을 절약하고 총 연결 수를 줄이는 데 도움이 된다.
- PHP 스크립트가 종료되는 코드 끝에는 커넥션을 계속 연결한다거나 끊는 옵션이 없기 때문에 연결 풀링 옵션은 MySQL서버 측에서 활성화 해 줘야 한다.
- mysql_pconnect()는 스크립트 실행이 끝날 때 연결 닫기 요청 신호를 보내지 않도록 PHP에 알리는 래퍼이다.

### MySql 이 아닌 다른 디비에서의 퍼시스트 커넥션

### 퍼시스트 커넥션이 비효율적인 경우

### Nginx에서의 퍼시스트 커넥션

## Reference
- https://stackoverflow.com/questions/39753/connection-pooling-in-php
- 지속성 연결이란? https://www.php.net/manual/en/features.persistent-connections.php
- Nginx에서 지속성 연결 https://stackoverflow.com/questions/43280573/whether-the-connection-between-php-fpm-and-nginx-by-fast-cgi-are-persistent-kee