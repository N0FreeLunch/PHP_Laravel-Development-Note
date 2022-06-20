## Batch
- 라라벨에서 Batch 작업은 Queue 방식으로 지원하고 있다. Queue 기반의 배치를 job batch 라고 부른다.
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




## Reference
- https://laravel.kr/docs/8.x/queues#job-batching
