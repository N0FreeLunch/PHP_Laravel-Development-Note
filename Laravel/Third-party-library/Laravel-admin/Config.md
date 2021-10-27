## 파일 시스템 설정 값
- config\filesystems.php
```
'disks' => [
    'admin' => [
        'driver' =>'local',
        'root' => public_path('uploads'),
        'visibility' =>'public',
        'url' => env('APP_URL').'/uploads',
],
```
