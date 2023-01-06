## syntax
```
fopen(
    string $filename,
    string $mode,
    bool $use_include_path = false,
    ?resource $context = null
): resource|false
```

### `string $filename`
- 로컬 시스템의 파일 경로를 입력 받을 수 있다.
- 몇 가지 지정된 형식의 프로토콜을 전달할 수 있다. `스키마://...` php에서 지원하는 형식의 주소를 통해서 연결되는 방식을 통해 파일을 읽을 수 있다.

#### 지원되는 프로토콜 리스트
- `file://` — Accessing local filesystem
- `http://` — Accessing HTTP(s) URLs
- `ftp://` — Accessing FTP(s) URLs
- `php://` — Accessing various I/O streams
- `zlib://` — Compression Streams
- `data://` — Data (RFC 2397)
- `glob://` — Find pathnames matching pattern
- `phar://` — PHP Archive
- `ssh2://` — Secure Shell 2
- `rar://` — RAR
- `ogg://` — Audio streams
- `expect://` — Process Interaction Streams

### `string $mode`
- 파일을 조작하기 위한 모드를 설정한다.
- 제공되는 모드는 `'r'`, `'w'`, `'a'`이다.

#### 파일 시작 부분에 포인터 놓기
- `'r'` : 읽기 모드
- `'r+'`: 읽기 모드 + 쓰기 모드

#### 파일 


### return
- 지정한 파일의 파일 포인터 리소스를 반환한다.
- 파일 포인터 리소스는 파일의 포인터 정보를 php의 파일 관련 기능 간에 공유하기 위한 것이다.


## 예제
```
<?php
$handle = fopen("/home/rasmus/file.txt", "r");
$handle = fopen("/home/rasmus/file.gif", "wb");
$handle = fopen("http://www.example.com/", "r");
$handle = fopen("ftp://user:password@example.com/somefile.txt", "w");
?>
```


## Reference
- https://www.php.net/manual/en/function.fopen.php
