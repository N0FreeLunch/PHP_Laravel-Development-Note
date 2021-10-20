## 라라벨 스케쥴러란?
- 리눅스에서 크론 작업을 추가하기 위해서는 크론텝을 열고 작업을 등록해야 한다.
- 라라벨 스케쥴러는 한번 등록한 크론 파일에 연결되는 파일을 라라벨 레포지토리 안에 넣어 두는 방식으로 인해서 서버의 배포와 동시에 추가적인 스케쥴링을 수행할 수 있게 만들어 주는 기능이다.
- 또한 크론에서는 설정하기 어려웠던 편리한 설정을 제공하여 스케쥴링 php 코드를 통해 크론 로직을 쉽게 만들 수 있다.


## 라라벨 스케쥴러 등록
- 리눅스 크론텝에 다음 코드를 넣는다.
```
* * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```
- path-to-your-project 부분은 라라벨 프로젝트 폴더, `php artisan` 명령어를 실행할 수 있는 부분으로 연결 해 줘야 한다.

## 커널 정의하기
- App\Console\Kernel.php 파일에 스케쥴링을 설정한다. 
```
<?php

namespace App\Console;

use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;
use Illuminate\Support\Facades\DB;

class Kernel extends ConsoleKernel
{
    /**
     * The Artisan commands provided by your application.
     *
     * @var array
     */
    protected $commands = [
        //
    ];

    /**
     * Define the application's command schedule.
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
        $schedule->call(function () {
            DB::table('recent_users')->delete();
        })->daily();
    }
}
```

#### 클로저 부분
```
function () {
            DB::table('recent_users')->delete();
        }
```

```
 $schedule->call(function () {
            DB::table('recent_users')->delete();
        })->daily();
```
- 스케쥴링의 등록은 $schedule 객체의 call 메소드를 호출하여 스케쥴에 관한 로직이 들어 있는 클로저를 인자로 할당하여 스케쥴링 로직을 등록한다.
- \_\_invoke 메소드를 사용한 객체의 경우 `$schedule->call(new DeleteRecentUsers)->daily();`와 같이 클로저 대신 객체를 사용할 수도 있다.


### artisan 커멘드 등록하기
```
$schedule->command('emails:send Taylor --force')->daily();
```
- php artisan 으로 등록한 커멘드라인 명령어로 만들어진 스케쥴러를 등록할 수 있다.
- 리눅스 커멘드라인 명령어를 등록할 수도 있다.

### ??
```
$schedule->command(EmailsCommand::class, ['Taylor', '--force'])->daily();
```




## Reference
- https://laravel.kr/docs/8.x/scheduling
