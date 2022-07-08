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
- `use App\Jobs\ProcessPodcast;` 큐 작업의 단위인 job을 만들었다.
- `dispatch()` 메소드는 큐 잡을 실행한다.

### 큐의 잡의 실행
- `dispatch()` 메소드를 통해 큐 잡이 실행되면 job에 정의한 작업이 저장소에 저장이 된다.
- 저장소에 저장된 잡은 저장된 순서대로 백그라운드에서 큐 워커에 의해서 순차적으로 실행이 된다.

## 다중 작업
- 큐가 순차적으로 작업을 처리하기 때문에 하나의 작업이 처리되고 다음 작업이 처리 되기까지 대기 시간이 필요하다. 만약 작업을 실행하는 도중에 시스템의 리소스가 남는다고 하면 다른 작업 실행하여 시스템의 리소스를 사용하는 것이 하드웨어 사용 효율을 높일 수 있다. 따라서 큐에 적재된 작업과 순서의 영향이 없는 작업은 따로 실행하여 시스템 리소스를 최대한 사용하는 것이 유리하다. 그런데 멀티 작업의 최대 갯수는 스레드의 2배 정도가 적당하다는 의견이 있다. 결국 작업의 최대 갯수를 조절을 해야 하는데 이를 위해서는 작업을 임의로 실행하게 만들면 안 되고 미리 정해둔 방식에 따라 백그라운드 작업의 갯수를 정해 둔다.

### 멀티 큐
- 애플리케이션의 다양한 요구사항을 처리하기 위해서는 작업의 순차적 처리가 중요한 여러 개의 작업 그룹이 있을 수 있다. 이 작업 그룹 각각에 속한 작업은 그룹 내의 다른 작업과의 순차적인 처리를 요구한다.
- 큐를 사용하는 것은 순차적인 작업처리를 위해 사용한다. 하나의 큐에 넣는 작업은 각 작업 간의 순서가 서로의 작업에 영향을 줄 수 있는 대상이 된다.
- 만약 작업 간에 서로 영향을 주지 않는 작업 집합이라면 두 집합을 담당하는 큐는 서로 분리 되어도 된다.
- 서로 다른 큐를 만드는 것은 작업 그룹을 관심사 별로 나눌 수 있기 때문에 중요하다.
- 하지만 너무 많은 큐를 동시에 돌리게 되면 멀티 작업이 너무 많아질 수 있어 좋지 않다. 동시에 일어날 수 있는 큐 작업의 갯수를 조절하는 방법이 필요하다.

### 단일 큐
- 큐의 갯수가 많아지게 되면 동시에 실행되는 하드웨어 스레스 갯수 대비 큐의 갯수가 너무 많아져서 멀티 작업에서의 컨텍스트 스위칭 비용이 상승해서 처리 효율이 떨어지는 경우가 생길 수 있다. 이런 경우 순서가 상관이 없는 작업들도 섞어서 하나의 단일 큐에 작업을 할당하는 방법을 사용하는 게 나을 수도 있다.
- 만약 서버의 하드웨어 사양이 코어 수가 적다면 여러 큐를 동작 시키는 것 보다 단일 큐를 사용하여 사용되는 스레드 수를 줄이는 것이 좋다. 

#### 스케쥴러
- 스케쥴러는 지정한 시간 또는 시간 간격으로 작업을 지시하는 역할을 한다. 라라벨의 아티산 콘솔을 통해 실행되는 작업은 하나의 프로세스로 동일 시간에 너무 많은 작업이 실행되는 경우가 있다. 이럴 경우 작업의 처리 효율이 떨어지고 갑작스런 부하로 작업 실패가 일어날 수도 있다. 만약 배치 로직을 만든다고 하면 스케쥴러의 로직을 바로 실행하는 프로세스를 여러개 실행하는 것 보다 큐에 작업을 담아서 하나의 프로세스에서 큐에 담은 순서대로 작업을 순차적으로 실행할 수 있는 로직을 만드는 것이 과부하의 위험을 줄일 수 있다.
- 스케쥴러는 작업을 여러 시간대에 나눈다. 스케쥴러를 통해 배치작업을 하게 되면 작업을 여러 시간에 나누기 때문에 컴퓨터의 리소스를 충분히 사용하지 못하고 스케쥴러가 실행한 시간 부터 작업이 끝나는 시간까지만 시스템 리소스를 소비한다. 작업이 끝나게 되면 스케쥴러에 의해 다음 작업이 실행될 때까지 시스템의 리소스가 남게 된다. 이런 문제를 최대한 방지를 할 수 있는 것이 스케쥴러를 통해서 정한 시간 이후에 실행하면 되도록 로직을 만들고 큐에 넣는 것이다. 계속적으로 큐에 담긴 작업이 실행 되어 스케쥴러의 간격에 따른 리소스 여유가 남는 사태를 최대한 방지할 수 있다.

### 멀티 큐의 우선순위
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
```
QUEUE_CONNECTION=sync
```
- `.env` 파일에서 QUEUE_CONNECTION 옵션을 지정하여 큐 잡을 어떻게 처리할 것인지 지정한다.
- `QUEUE_CONNECTION`의 디폴트 값은 sync이다. sync는 동기적으로 처리하겠다는 의미이며 큐 job을 백그라운드로 처리하지 않으므로 큐 job을 실행한 프로세스는 실행된 큐 job이 종료될 때까지 대기하게 된다.
- QUEUE_CONNECTION 옵션을 database로 지정하게 되면 비동기적으로 처리하겠다는 의미이며 큐 job을 백그라운드로 처리한다. 큐 job을 실행한 프로세스는 실행할 큐 job을 저장소에 저장을 하고 다음 로직을 실행한다. 백그라운드에서 동작하고 있는 큐 워커는 저장된 job을 적재된 순서로 실행하는 비동기적인 작업을 한다.

### Redis

## 직렬화와 페이로드
### 직렬화
- 직렬화(直列化) 또는 시리얼라이제이션(serialization)은 컴퓨터 과학의 데이터 스토리지 문맥에서 데이터 구조나 오브젝트 상태를 동일하거나 다른 컴퓨터 환경에 저장(이를테면 파일이나 메모리 버퍼에서, 또는 네트워크 연결 링크 간 전송)하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정이다. [reference](https://ko.wikipedia.org/wiki/%EC%A7%81%EB%A0%AC%ED%99%94)

### 페이로드
- 페이로드(영어: payload)는 사용에 있어서 전송되는 데이터를 뜻한다. 페이로드는 전송의 근본적인 목적이 되는 데이터의 일부분으로 그 데이터와 함께 전송되는 헤더와 메타데이터와 같은 데이터는 제외한다. [reference](https://ko.wikipedia.org/wiki/%ED%8E%98%EC%9D%B4%EB%A1%9C%EB%93%9C_(%EC%BB%B4%ED%93%A8%ED%8C%85))
- 라라벨의 job이 저장소의 queue에 적재될 때 job 인스턴스를 직렬화해서 저장한다. 큐에 의해서 적재된 잡이 실행될 때는 클래스나 인스턴스를 직접 읽지 않고, 적재된 직렬화 된 정보를 역직렬화한 인스턴스를 실행하는 방식을 가지고 있다.
- 저장소의 job은 payload라는 컬럼 또는 키를 가지고 있고 벨류값으로 직렬화된 잡 인스턴스를 저장한다.

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

### 생성자 주입
- 엘로퀀트 모델을 생성자 주입할 수 있다.
- SerializesModels 트레이트를 통해서 생성자를 통해 주입된 모델과 모델에 로드된 관계 모델 데이터는 job이 처리될 때 job 인스턴스를 실행했을 시점에서 로드한 모델과 동일한 구조의 인스턴스로 처리하도록 하기 위해서 시리얼라이즈 되어 저장소에 저장된다. 
- 큐에 의해 잡이 실행될 때 job 인스턴스가 실행될 시점에서 저장된 모델을 역직렬화하여 실행하도록 한다. 직렬화 되는 것은 모델 인스턴스를 타입힌트 방식으로 의존성 주입했을 때만이다. 모델 클래스를 생성자 주입해서 받는 별도의 클래스를 주입했을 때는 이 인스턴스가 직렬화되지 않고 인스턴스 내부에서 로드된 모델 인스턴스도 직렬화 되지 않는다. job 클래스의 생성자에서 의존성 주입이 되었을 때만 모델 인스턴스가 시리얼라이징 된다.
- 큐 잡에 의해서 데이터베이스에 저장되어 있던 잡이 실행되면 저장된 구조에 맞게 데이터를 데이터베이스에서 다시 로드하기 때문에 큐 잡을 저장소에 넣을 때 실행한 모델 인스턴스와 구조는 동일하지만 실행 시점이 다르기 때문에 데이터의 값이 다를 수 있다. 잡 인스턴스가 실행될 때 생성자 주입된 모델의 인스턴스를 직렬화하여 저장소에 저장할 때 큐에 의해 잡이 실행될 때의 인스턴스의 구조는 같지만 담긴 데이터는 실행시점에 따라서 로드가 되기 때문에 다를 수 있다. 어차피 실행 시점에서 로드된 데이터에 의존하기 때문에 잡 인스턴스가 실행될 때 생성자 주입된 모델의 인스턴스를 직렬화 할 때 모든 데이터를 저장할 필요 없이 구조만 저장하면 되기 때문에 로드된 전체 데이터가 아닌 다시 데이터를 로드할 수 있는 인스턴스 구조를 만들 수 있는 정도의 데이터만 큐에 job을 적재할 때 사용한다. 
- 기본적으로는 job 인스턴스가 시리얼라이징 된다. SerializesModels 트레이트에 의해 모델이 시리얼라이징 되는 것은 job 인스턴스가 시리얼라이징 될 때 시리얼라이징 된 데이터 내부에 엘로퀀트 모델이 시리얼라이징되어 저장된 형태를 가진다.

## handle 메소드 주입
- job 인스턴스의 생성자가 큐 job에 적재 되기 전에 실행되는 것에 반해, handle 메소드는 저장소에 적재된 시리얼라이징 된 잡 인스턴스가 역직렬화 되어 실행되는 시점 곧 큐 워커에 의해서 큐 잡이 실행되는 시점에서 handle 메소드가 실행된다.
- 큐 잡에 의해서 handle 메소드가 실행될 때 타입힌트를 통한 의존성 주입이 가능하다. 라라벨의 서비스 컨테이너는 job 인스턴스의 의존성을 자동 주입한다.

### handle 메소드 의존성 주입 커스터마이징
- bindMethod 메서드를 사용하여 handle 메소드의 의존성 주입 방식에 대한 사용자 정의를 수행할 수 있다.
```
use App\Jobs\ProcessPodcast;
use App\Services\AudioProcessor;

$this->app->bindMethod([ProcessPodcast::class, 'handle'], function ($job, $app) {
    return $job->handle($app->make(AudioProcessor::class));
});
```
- `function ($job, $app)` bindMethod 메소드는 작업과 컨테이너를 수신하는 콜백을 입력받는다.
- 콜백 내에 정의한 `return $job->handle($app->make(AudioProcessor::class));`과 같이 원하는 대로 handle 메서드를 호출할 수 있습니다.
- 일반적으로 `App\Providers\AppServiceProvider` 서비스 컨테이너의 `boot` 메소드에서 이 메소드를 호출해야 한다.

#### 주의
> Raw 이미지와 같은 바이너리 데이터의 경우, 큐를 통해서 처리되기 전에 base64_encode 함수가 적용된 상태로 전달되어야 합니다. 그렇지 않으면 Job이 큐에 입력 될 때 JSON으로 제대로 serialize 되지 않을 수 있습니다.

##  relationship 처리
- 엘로퀀트 모델을 의존성 주입하게 되면 모델 인스턴스에 로드된 관계 모델의 구조도 함께 시리얼라이징 된다. 로드된 관계 모델의 데이터가 많으면 많을수록 시리얼라이징 되어야 하는 데이터도 늘어나기 때문에 관계 모델이 로드될 가능성을 차단하는 코드가 필요할 경우가 있다. 모델 인스턴스에서 `withoutRelations`메소드를 호출하면 관계 모델을 로드하지 않기 때문에 시리얼라이징되는 데이터가 줄어드는 것을 이용하여 최적화에 쓰도록 하자.
```
/**
 * Create a new job instance.
 *
 * @param  \App\Models\Podcast  $podcast
 * @return void
 */
public function __construct(Podcast $podcast)
{
    $this->podcast = $podcast->withoutRelations();
}
```


## 유니크 job
- 동일한 타입의 job 인스턴스는 큐에 하나만 적재 되도록 하기 위해 사용하는 방법이다.
- 같은 클래스의 인스턴스라도 내부 상태가 다를 수 있다. 서로 상태가 다른 인스턴스라고 하더라도 같은 job 클래스라면 하나만 queue에 적재할 필요가 있을 때 사용한다.
```
<?php

use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Contracts\Queue\ShouldBeUnique;

class UpdateSearchIndex implements ShouldQueue, ShouldBeUnique
{
    ...
}
```
- `ShouldBeUnique` 인터페이스를 사용하면 `UpdateSearchIndex` 잡은 하나의 큐 구조에 하나만 존재한다.
- 이미 동일 타입의 잡이 적재되어 있거나 실행되어 있다면 큐에 더이상 동일 타입의 잡을 추가하지 않는다.

### 유니크 잡을 위한 저장소 락
- 유니크 잡을 만들기 위해서는 테이블락을 지원하는 저장소를 사용해야 한다. 

```
<?php

use App\Product;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Contracts\Queue\ShouldBeUnique;

class UpdateSearchIndex implements ShouldQueue, ShouldBeUnique
{
    /**
     * The product instance.
     *
     * @var \App\Product
     */
    public $product;

    /**
     * The number of seconds after which the job's unique lock will be released.
     *
     * @var int
     */
    public $uniqueFor = 3600;

    /**
     * The unique ID of the job.
     *
     * @return string
     */
    public function uniqueId()
    {
        return $this->product->id;
    }
}
```

## Refernece
- https://laravel.kr/docs/8.x/queues
- https://dev.to/ryancco/understanding-laravel-s-serializesmodels-trait-a6e
