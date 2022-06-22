## Batch
- 라라벨에서 Batch 작업은 Queue 방식으로 지원하고 있다. Queue 기반의 배치를 job batch 라고 부른다.
- 일괄작업 : Batch processing의 한국어이다.
- 배치란 사용자의 개입없이 작업을 처리하는 것을 의미한다. 실행이 되고 나서 작업이 끝날 때까지 사용자와의 상호작용 없이 진행되는 작업을 의미한다.

## 배치 큐 생성하기
```
php artisan queue:batches-table
php artisan migrate
```
- 배치 작업을 저장할 저장소를 만들어 주기 위해서 테이블 스키마를 만들고 마이그레이션을 실행한다.

```
<?php

namespace App\Jobs;

use Illuminate\Bus\Batchable;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ImportCsv implements ShouldQueue
{
    use Batchable, Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        if ($this->batch()->cancelled()) {
            // Determine if the batch has been cancelled...

            return;
        }

        // Import a portion of the CSV file...
    }
}
```

```
use App\Jobs\ImportCsv;
use Illuminate\Bus\Batch;
use Illuminate\Support\Facades\Bus;
use Throwable;

$batch = Bus::batch([
    new ImportCsv(1, 100),
    new ImportCsv(101, 200),
    new ImportCsv(201, 300),
    new ImportCsv(301, 400),
    new ImportCsv(401, 500),
])->then(function (Batch $batch) {
    // All jobs completed successfully...
})->catch(function (Batch $batch, Throwable $e) {
    // First batch job failure detected...
})->finally(function (Batch $batch) {
    // The batch has finished executing...
})->dispatch();

return $batch->id;
```
- 일괄 작업을 사용하기 위해서는 `use Illuminate\Bus\Batch;`를 사용한다.
- 일괄 작업에 대한 예제 코드를 보면 `Bus::batch()` 안에 배열로 넣는 대상이 `use App\Jobs\ImportCsv;`인 것으로 job을 할당하는 것을 알 수 있다.
- 일괄 작업은 하나의 작업이 끝나고 다음 작업이 이뤄질 수 있도록 하기 위한 방법으로 사용한다.


### 완료 콜백
```
>then(function (Batch $batch) {
    // All jobs completed successfully...
})->catch(function (Batch $batch, Throwable $e) {
    // First batch job failure detected...
})->finally(function (Batch $batch) {
    // The batch has finished executing...
})
```
- 완료 콜백은 `Batch $batch` 부분에서 알 수 있듯이 `Illuminate\Bus\Batch` 인스턴스를 매개변수로 받는다. 
- 완료 콜백은 라라벨 큐에 의해 직렬화되어 실행되기 때문에 콜백 내에서 $this 변수를 사용하면 안된다.


## Reference
- https://laravel.kr/docs/8.x/queues#job-batching
