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
- `* * * * * `는 매 분마다 크론 잡을 실행하라는 의미이다.
- `php artisan schedule:run >> /dev/null`는 `php artisan schedule:run` 리눅스 크론이 실행한는 명령어이고 라라벨의 스케쥴러를 실행하고 실행한 출력 결과가 `/dev/null`라는 파일에 추가로 기록된다는 것을 의미한다. `>>`가 실행 결과를 파일에 추가로 기록한다는 의미이다.
- `/dev/null`이란 파일은 기록을 하지 않는 파일을 의미한다. 따라서 전달된 결과 값은 기록을 하지 않는다는 의미이다.
- 리눅스에는 표준입력, 표준출력, 표준에러라는 개념이 있다. 0은 표준입력을 의미하며, 1은 표준출력을 의미하며, 2는 표준에러를 의미한다.
- `>`는 파일의 내용을 비우고 전달한 값을 기록한다는 의미를 갖는다. 기본적으로 `>` 다음에는 파일을 지정하며 표준출력으로 전송한 값을 지정한 파일로 보낸다는 의미를 가진다. 이 때 파일을 지정하는 것이 아니라 표준출력으로 전송할 값을 다른 값으로 바꾼다는 의미를 갖게 하려면 `&값`의 표현을 사용한다. 
- `2>&1`는 표준에러를 표준출력으로 바꾼다는 의미를 가진다. 쉘 스크립트를 실행하는 과정에서 오류가 발생하면 뭔가 대단한 사건이 일어난 것 처럼 느껴진다. 따라서 라라벨에서 나온 실패는 라라벨 자체적으로 처리할테니 쉘에서는 애러라고 판단하여 뭔가 일어난 것처럼 난리 피우지 말고 그냥 에러 메시지를 문자열로 출력하라는 의미에서 지정한다.
- 곧, 전체 의미는 라라벨의 스케쥴러 명령어를 실행한 결과 로그는 라라벨에서 기록하니까 OS에서는 기록하지 말라는 의미이며, 이 때 에러가 발생한 경우 에러로 쉘에서 에러 났다고 소란 피우지 않도록 문자열로 로그를 전달하도록 설정한다는 의미이다.
- Reference : https://stackoverflow.com/questions/10508843/what-is-dev-null-21

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
