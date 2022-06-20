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
- https://laravel.kr/docs/8.x/queues#Job%20%EB%B0%B0%EC%B9%98(%EB%8F%99%EC%9D%BC%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8%EC%97%90%EC%84%9C%20%EC%9D%BC%EA%B4%84%20%EC%B2%98%EB%A6%AC%EB%90%98%EB%8A%94%20%EC%9E%91%EC%97%85%20%EB%8B%A8%EC%9C%84)
