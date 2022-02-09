## Channel
- 채널은 논블럭킹 프리미티브(primitive)이다.
- 논블럭킹 프리미티브는 두 개 이상의 코루틴 간의 통신을 하기 위해 사용한다.
- 채널을 사용하면 복수의 코루틴 간이 통신 및 컨트롤을 할 수 있다.

## 채널이 필요한 이유
- 서로 다른 코루틴은 동일한 메모리 공간에 서로 엑세스 할 수 있다.
- 채널은 메모리 엑세스의 충돌 및 레이스 컨디션을 완화시키는 역할을 하여 병렬 프로그래밍을 가능하게 한다.
- 이는 golang의 `chan`(채널)과 비슷하다.

## 채널의 특징
- php의 배열과 비슷하다.
- 코루틴의 작업을 지원하는 역할을 한다.
- 모든 작업이 메모리 내에서 수행된다.


## swoole에서의 채널 사용
- `\chan`으로 사용하거나 `Swoole\Coroutine\Channel` 네임스페이스를 통해서 사용할 수 있다.

## 코루틴 동기화
- 코루틴을 동기화 할 때는 WaitGroup, batch , Barrier를 사용한다.

## 채널 클래스 속성
- ---

### capacity
- `Swoole\Coroutine\Channel->$capacity` : 채널의 대기열 용량으로, 새 채널을 생성할 때 생성자 내에서 설정됩니다. 1보다 크거나 같아야 합니다.

### 채널 에러 코드
- `Swoole\Coroutine\Channel->$errCode` : 채널의 오류 코드 상태를 가져옵니다.

#### 반환값이 0인 경우
- 상수 : SWOOLE_CHANNEL_OK
- 채널이 문제 없이 작동하며, 에러 없음

#### 반환값이 -1인 경우
- 상수 : SWOOL_CHANNEL_TIMEOUT
- 채널이 타임아웃 상태일 때


#### 반환값이 -2인 경우
- 상수 : SWOOL_CHANNEL_CLOSED
- 채널이 닫혔을 때

## 예제
```
<?php

Co\run(function () {

    $chan = new Swoole\Coroutine\Channel(1);
    go(function() use ($chan) {
        $cid = Swoole\Coroutine::getuid();
        $i = 0;
        while (1) {
            co::sleep(1);
            $chan->push(['rand' => rand(1000, 9999), 'index' => $i]);
            echo "[coroutine $cid] - $i\n";
            $i++;
        }

    });

    go(function() use ($chan) {
        $cid = Swoole\Coroutine::getuid();
        while(1) {
            $data = $chan->pop();
            echo "[coroutine $cid]\n";
            var_dump($data);
        }
    });
});
```
- `new Swoole\Coroutine\Channel(1)` 채널의 용량을 1로 지정
- 하나의 컨텍스트 `Co\run()` 내에 루틴이 두 개 `go(function() use ($chan) {...});`, `go(function() use ($chan) {...});`가 들어가 있다.
- 루틴을 정의할 때 사용하는 클로저는 설정한 채널 객체 `$chan`을 캡쳐링 한다. 클로저에 객체를 캡쳐하면 참조로 전달되므로 두 루틴은 $chan 객체를 공유하고 있다.
- 각 루틴에서 각 루틴의 아이디를 `Swoole\Coroutine::getuid();`으로 가져온다.



## Reference
- https://openswoole.com/docs/modules/swoole-coroutine-channel
