## 라라벨의 파일 시스템
- Flysystem 이란 오픈소스를 추상화한 파일 시스템을 제공 : https://github.com/thephpleague/flysystem


## 라라벨 파일 시스템의 기능
- 로컬 파일을 컨트롤 할 수 있는 기능
- AWS S3의 파일 시스템도 컨트롤 할 수 있는 기능
- 메모리에 파일을 저장할 수 있는 기능 제공


## 라라벨 파일 시스템의 특징
- 로컬, S3, 메모리 등등 다양한 저장소에 대해 동일한 인터페이스로 파일을 컨트롤 할 수 있도록 추상화 되어 있다. 


## 라라벨 파일 시스템의 설정
- `config/filesystems.php`에서 설정할 수 있다.
- 한 종류만이 아닌 다양한 종류의 파일시스템에 대한 설정을 할 수 있다.
- 파일 스토리지를 사용할 때는 설정된 여러 파일 스토리지를 이용할 수 있다.


## 로컬 스토리지 
- `config/filesystems.php`에서 로컬 파일 시스템에 대한 설정을 한다.
```
'local' => [
    'driver' => 'local',
    'root' => storage_path('app'),
    'permissions' => [
        'file' => [
            'public' => 0664,
            'private' => 0600,
        ],
        'dir' => [
            'public' => 0775,
            'private' => 0700,
        ],
    ],
],
```
- public과 private이 존재하는데, public 옵션은 라라벨 프로젝트 최상단의 `public` 폴더를 통해 웹으로 배포될 파일들이 위치하는 곳으로 연결하는 역할을 한다.
- 설정에서 dir의 경우 public은 `storage/app/public` 폴더의 권한을 설정하며 private는 `storage/app/public` 폴더의 권한을 설정한다. file의 경우 public은 `storage/app/public` 내부의 파일 권한을 설정하며 private는 `storage/app/public` 폴더 내부의 파일 권한을 설정한다.

### 로컬 스토리지를 배포에 이용하기
- 라라벨의 스토리지는 `storage/app` 경로에 파일을 보관하는 것에 반해 라라벨의 퍼블리싱은 `public` 폴더를 통해 이뤄진다. 파일이 `public` 폴더 안에 있어야 외부 환경에서 접근이 가능하며 이를 위해서는 `storage/app` 안의 파일을 `public` 폴더 안에 넣어야 한다. 이를 위해 `storage/app/public` 폴더를 `public/storage` 경로에 심볼릭링크로 생성한다.

#### 심볼릭 링크 생성하기
- 다양한 배포 환경에서 동일한 명령으로 심볼릭링크를 생성할 수 있도록 심볼릭 링크를 생성할 수 있는 아티산 명령을 제공한다.
```
php artisan storage:link
```

#### 로컬 파일의 경로를 퍼블리싱 때 제공하기
- `asset('storage/file.txt');`을 사용하면 파일의 경로를 만들며 이 경로가 포함된 URL로 접근하면 파일의 URL을 만들 수 있다.

### 커스텀 심볼릭 링크 만들기
- `config/filesystems.php`에서 파일에 대한 추가적인 심볼릭 링크를 구성할 수 있다.
```
'links' => [
    public_path('storage') => storage_path('app/public'),
    public_path('images') => storage_path('app/images'),
],
```
- 위와 같이 설정을 했다면 `storage/app/public`폴더는 `public/storage`에 심볼릭링크를 생성하고, `storage/app/images`폴더는 `public/images`에 심볼릭링크를 생성한다.
- 설정을 하고나서 `php artisan storage:link`명령어를 통해 심볼릭 링크를 생성해 줘야 한다.

## 로컬 환경에서 사용하기
```
Storage::disk('local')->put('file.txt', 'Contents');
```
- 코드를 보면 먼저 `disk('local')`로 드라이버를 지정한다. 그리고 `put('file.txt', 파일객체)`을 통해 파일명과 파일 객체를 넣어 준다.

### 로컬 드라이버
- `config/filesystems.php`에 `'local' => ` 연관 배열 안에 설정된 root 스토리지 (`'root' => storage_path('app')`) 부분에 storage_path 헬퍼 함수로 디폴트 경로가 storage인 폴더의 정의한 경로 app 폴더를 로컬 파일 저장소로 쓰고 있다.



## Reference
- https://laravel.kr/docs/8.x/filesystem
