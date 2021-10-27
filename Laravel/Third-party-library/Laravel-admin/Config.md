## 파일 시스템 설정 값
- config\filesystems.php
- 이 부분을 설정하지 않으면 다음 메시지가 나온다.
```
Disk [admin] not configured, please add a disk config in `config/filesystems.php
```

```
'disks' => [
    'admin' => [
        'driver' =>'local',
        'root' => public_path('uploads'),
        'visibility' =>'public',
        'url' => env('APP_URL').'/uploads',
],
```
