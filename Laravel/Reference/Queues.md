## 큐(Queues)
- 큐는 순차적인 처리를 담당한다. 작업들을 순서대로 적재하고 적재 된 순서대로 실행하는 기능을 담당한다.

## 큐를 사용해야 할 때
- 웹 서버는 클라이언트의 요청에 대한 응답을 제공해야 한다. 하지만 요청을 받고 응답을 만들어내는데 오랜 시간이 걸린다면 유저에게 나쁜 경험을 제공하게 된다. 예를 들어 유저는 기능이 제대로 동작하지 않는 것으로 느끼고는 페이지를 떠나거나 새로 고침을 하게 되고 클라이언트가 응답을 받지 못해 후속 처리가 제대로 이뤄지지 않을 수 있다. 따라서 유저에게는 가능한한 빠르게 리스폰스를 보내어 유저의 경험을 좋게 유지하고 시간이 걸리는 작업은 백그라운드 작업으로 처리하고 유저에게는 백그라운드 처리의 진행상황을 보여주는 것이 좋다.

## 큐 관리자
- 라라벨의 큐 작업을 확인할 수 있는 대쉬 보드가 제공된다.
- 라라벨 호라이즌을 참고할 것

## 커넥션과 큐
- 라라벨에서 지원하는 큐 시스템은 서로 다른 연결을 가지고 있다. 라라벨의 큐 시스템은 연결을 여럿 가질 수 있으며 AWS SQS, Redis, RDBMS 등 다양한 저장소를 연결할 수 있는 기능을 제공한다.
- 하나의 연결이 여러 큐를 가질 수 있다.
```
use App\Jobs\ProcessPodcast;

// This job is sent to the default connection's default queue...
ProcessPodcast::dispatch();

// This job is sent to the default connection's "emails" queue...
ProcessPodcast::dispatch()->onQueue('emails');
```
- `onQueue('emails')`를 사용해서 하나의 연결에 대한 큐 이름을 지정할 수 있다.

## 다중 작업
- 큐가 순차적으로 작업을 처리하기 때문에 하나의 작업이 처리되고 다음 작업이 처리 되기까지 대기 시간이 필요하다. 만약 작업을 실행하는 도중에 시스템의 리소스가 남는다고 하면 다른 작업 실행하여 시스템의 리소스를 사용하는 것이 하드웨어 사용 효율을 높일 수 있다. 따라서 큐에 적재된 작업과 순서의 영향이 없는 작업은 따로 실행하여 시스템 리소스를 최대한 사용하는 것이 유리하다. 그런데 멀티 작업의 최대 갯수는 스레드의 2배 정도가 적당하다는 의견이 있다. 결국 작업의 최대 갯수를 조절을 해야 하는데 이를 위해서는 작업을 임의로 실행하게 만들면 안 되고 미리 정해둔 방식에 따라 백그라운드 작업의 갯수를 정해 둔다.

## 멀티 큐
- 애플리케이션의 다양한 요구사항을 처리하기 위해서는 작업의 순차적 처리가 중요한 여러 개의 작업 그룹이 있을 수 있다. 이 작업 그룹 각각에 속한 작업은 그룹 내의 다른 작업과의 순차적인 처리를 요구한다.
- 큐를 사용하는 것은 순차적인 작업처리를 위해 사용한다. 하나의 큐에 넣는 작업은 각 작업 간의 순서가 서로의 작업에 영향을 줄 수 있는 대상이 된다.
- 만약 작업 간에 서로 영향을 주지 않는 작업 집합이라면 두 집합을 담당하는 큐는 서로 분리 되어도 된다.
- 서로 다른 큐를 만드는 것은 작업 그룹을 관심사 별로 나눌 수 있기 때문에 중요하다.
- 하지만 너무 많은 큐를 동시에 돌리게 되면 멀티 작업이 너무 많아질 수 있어 좋지 않다. 동시에 일어날 수 있는 큐 작업의 갯수를 조절하는 방법이 필요하다.

## 단일 큐
- 큐의 갯수가 많아지게 되면 동시에 실행되는 하드웨어 스레스 갯수 대비 큐의 갯수가 너무 많아져서 멀티 작업에서의 컨텍스트 스위칭 비용이 상승해서 처리 효율이 떨어지는 경우가 생길 수 있다. 이런 경우 순서가 상관이 없는 작업들도 섞어서 하나의 단일 큐에 작업을 할당하는 방법을 사용하는 게 나을 수도 있다.
- 만약 서버의 하드웨어 사양이 코어 수가 적다면 여러 큐를 동작 시키는 것 보다 단일 큐를 사용하여 사용되는 스레드 수를 줄이는 것이 좋다. 

### 스케쥴러

## 멀티 큐의 우선순위
- 여러개의 큐를 운용하다 보면 우선적으로 처리해야 할 큐 작업이 존재할 수 있다.
- 큐의 우선순위를 정할 수 있는 명령어는 high, medium, low 만 존재하기 때문에 우선순위를 여러 큐 간의 실행 순서를 세부적으로 컨트롤하기 어려운 단점이 존재한다.
```
php artisan queue:work --queue=high,default
```

## 스테이트리스 서버
- 웹 애플리케이션 서버의 중요한 가치는 서버는 스테이트리스(stateless)여야 한다는 것이다. 서버가 0대가 되지 않는다면 서버를 추가 및 제거해도 트레픽 처리 문제가 아니라면 동작에 문제가 없어야 한다는 것이다. 이를 위해서 서버의 상태 저장은 웹 애플리케이션 서버와 독립적인 저장소에 보관되어야 한다.
- 하나의 컴퓨터에 쓰레드가 여럿 있다면 메모리 자원의 공유문제가 생긴다. 일반적인 프로그래밍은 변수에 할당된 값을 변경하면서 데이터의 상태를 변화시킨다. 변수에 할당된 값이 외부의 요인으로 달라지게 되면 로직이 꼬이게 된다. 따라서 멀티 쓰레드를 사용한 프로그래밍을 하기 위해서는 하나의 할당된 메모리의 상태 변화를 관리할 때 여러 프로세스가 동시에 접근하지 못하도록 해야 한다. 또는 여러 프로세스가 변수에 정해진 로직 대로 접근을 해서 멀티 쓰레드 프로그래밍이 변수의 상태 변화를 통제할 수 있는 로직이 짜여져 있어야 한다.
- 따라서 서버는 하나가 아닌 여러개가 될 수 있다는 가정을 하고, 저장소의 상태 변화를 요구하는 작업에 대해 상태 변화를 통제 할 수 있는 방법을 찾아야 한다. 이 방법 중 하나가 큐 작업이다.
- 큐는 서버의 공유 저장소에 작업을 순차적으로 적재한 뒤, 작업의 실행을 순차적으로 처리하기 때문에 다수의 서버에서 동시에 접근을 한다고 해도 작업의 순서가 보장이 된다는 점에서 공유 저장소의 데이터를 다루는데 좋은 방법이 된다.


## 저장소에 연결
- 라라벨에서 제공하는 큐는 먼저 저장소를 지정해야 한다.
- 라라벨에서 큐의 저장소로 사용가능한 대상은 RDBMS, Redis, AWS SQS가 있다.
- 참고 : 라라벨 큐에 추가되는 각각의 작업을 job이라고 표현한다. 

### RDBMS에 연결하기
- 데이터베이스를 저장소로 사용할 경우 잡을 담아둘 데이터베이스 테이블이 필요하다. 순차적으로 잡을 실행할 수 있도록 테이블에 job을 넣는 작업이 필요하다. 
```
php artisan queue:table
```
- 큐 테이블을 만드는 마이그레이션 파일을 만든다.

```
php artisan migrate
```
- 큐 테이블을 만드는 마이그레이션 파일을 마이그레이션 한다.

```
QUEUE_CONNECTION=database
```
- `.env` 파일에서 QUEUE_CONNECTION 옵션을 database로 지정하게 되면 디폴트 큐 저장소로 데이터베이스를 사용하게 된다.

### Redis

## Job
- Job이란? 큐에 적재된 한 단위의 작업을 일컫는 말로 하나의 작업은 큐에서 꺼내지고 실행되는 로직의 한 단위이다.
- 라라벨에서 하나의 job 로직의 단위는 job 클래스를 통해서 만들어진다.

### Job 클래스 생성하기
```
php artisan make:job ProcessPodcast
```
- 생성된 잡 클래스는 app/Jobs 디렉토리에 저장된다.
- app/Jobs 디렉토리가 존재하지 않는 경우 make:job Artisan 명령을 실행할 때 생성된다.
- 생성된 잡 클래스는 Illuminate\Contracts\Queue\ShouldQueue 인터페이스를 구현한다. (인터페이스를 설정한 특별한 이유가 있는지 확인 필요.)

### job 클래스의 구조
```php
<?php

namespace App\Jobs;

use App\Models\Podcast;
use App\Services\AudioProcessor;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ProcessPodcast implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    /**
     * The podcast instance.
     *
     * @var \App\Models\Podcast
     */
    protected $podcast;

    /**
     * Create a new job instance.
     *
     * @param  App\Models\Podcast  $podcast
     * @return void
     */
    public function __construct(Podcast $podcast)
    {
        $this->podcast = $podcast;
    }

    /**
     * Execute the job.
     *
     * @param  App\Services\AudioProcessor  $processor
     * @return void
     */
    public function handle(AudioProcessor $processor)
    {
        // Process uploaded podcast...
    }
}
```
- handle 메소드는 큐에서 작업을 꺼내 실행할 때 실행하는 부분이다. handle 메소드에 실행 로직을 짜 주면 된다.
```
    use App\Models\Podcast;
    ...
    public function __construct(Podcast $podcast)
    {
        $this->podcast = $podcast;
    }
```
- 엘로퀀트 모델을 생성자 주입할 수 있다.


## Refernece
- https://laravel.kr/docs/8.x/queues