## PHP의 버전 업데이트 방법

### 모든 기능의 동작을 체크하는 방식으로 업데이트 하기
- php 공식 문서의 depreciate 대상을 모두 확인하기
- php 다음 버전을 올릴 때, 먼저 depreciate 대상인 항목을 살핀다.
- 라라벨 프로젝트 내의 모든 코드, 프레임워크, 라이브러리를 포함한 depreciate 대상을 모두 확인해야 한다.
- 이 때, 사용할 수 있는 전략으로는 태스트 서버 실제 서버는 기존 버전으로 두고 로컬에만 PHP 버전업을 한 후, 오랜기간 로컬에서 다양한 태스트를 진행한 후 개발 서버에 반영하고 프로덕션 서버에 업로드 하는 방법으로 사용할 수 있다.

### 유닛 태스트를 통한 버전 업데이트
- 태스트 커버리지를 100% 가까이 올리는 방식으로 php 버전업을 하고 태스트를 실행하고 문제가 없는지 확인하는 방식으로 버전업을 한다.
- 하지만 대부분의 회사와 프로젝트는 TDD나 유닛 태스트 커버리지를 충분하게 작성하지 않기 때문에 유닛 태스트를 통한 php 버전 업그레이드는 보편적인 방법은 아니다.

### 호환성 검사 툴을 통한 버전 업데이트
- 해서 모든 코드에 대한 호환성 검사를 진행한다.
- https://github.com/PHPCompatibility/PHPCompatibility

### 이후 버전의 deprecated 항목 로그로 남기기
- php의 메이저 버전이 업그레이드 될 때, 이전 버전에서 deprecated로 지정된 대상은 php에서 더 이상 사용할 수 없는 기능으로 변경된다.
- 메이저 버전 하기 전에 deprecated 항목은 이전 버전에서 공개 되어 있고, 필요하다면 deprecated 항목을 로그로 남길 수도 있다.

#### laravel에서 deprecated 항목 로그로 남기기
- https://laravel.com/docs/10.x/logging#logging-deprecation-warnings
```php
'channels' => [
    'deprecations' => [
        'driver' => 'single',
        'path' => storage_path('logs/php-deprecation-warnings.log'),
    ],
],
```
- 드라이버는 'single', 'slack' 등을 지정할 수 있고, 'single'의 경우에는 라라벨 프로젝트의 로그 파일에 기록을 남기는 옵션이며, 'slack'은 슬렉 메시지로 로그를 보낸다는 의미이다.
- 파일 로그도 남기고 싶고 슬랙 메시지도 보내고 싶다면 'driver'가 'stack'인 채널을 하나 구성하고 이 채널 이름을 'deprecations' 채널의 'driver'로 설정한다.
- `storage_path('logs/php-deprecation-warnings.log')`으로 지정하면 logs 폴더에 `php-deprecation-warnings-yyyymmdd.log`라는 형식의 파일이 로그가 발생한 날짜별로 기록되어 저장된다.
- deprecated 로그는 다양한 부분에서 발생한다. 심지어 라라벨 라이브러리의 코드도 deprecated 대상으로 나오는데 라라벨 프레임워크를 버전업하면 해결되는 일이기 때문에 deprecated 로그 대상에서 제거하고 싶은 경우가 있다.
- 로그를 남기는 채널의 커스터마이징 로직을 참고하도록 하자. https://laravel.com/docs/10.x/logging#monolog-channel-customization
