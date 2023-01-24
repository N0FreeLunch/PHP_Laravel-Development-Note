## 라라벨 스케쥴러란?
- 리눅스의 크론에 이해하기 어려운 표현으로 장황하게 스케쥴링 할 대상을 정의하는 어려움을 개선하기 위해 라라벨의 스케쥴러를 통해서 크론 작업을 실행하게 한 것이다.
- 이를 위해서 리눅스의 크론에 라라벨의 스케쥴러를 매분 실행하는 한 줄의 스케쥴을 추가해 주어야 한다.

### 라라벨 스케쥴러의 장점
- 리눅스 크론을 사용하기 위해서는 리눅스 크론을 동작시키기 위한 크론 문법을 알아야 한다. 하지만 라라벨 스케쥴러를 사용하면, 리눅스 크론에 한 줄의 라라벨 크론을 동작시키는 코드를 등록하는 것으로 라라벨을 통해서 크론을 활성화 시킬 수 있다. 좀 더 구조화 되어 있고 적절한 메소드명으로 제공되는 인터페이스를 통해서 이해하기 쉬운 표현으로 크론을 스케쥴링을 할 수 있다.
- 스케쥴링의 코드가 변경될 때 일일이 리눅스 크론을 재정의 할 필요가 없이 서버의 라라벨 코드 변경만으로도 스케쥴링을 변경할 수 있다.

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
