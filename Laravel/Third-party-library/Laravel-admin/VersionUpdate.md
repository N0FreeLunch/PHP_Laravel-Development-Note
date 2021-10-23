https://laravel-admin.org/docs/en/updating


## 현재 버전 보기
```
composer show encore/laravel-admin
```

## 최신 버전으로 업데이트
```
composer require encore/laravel-admin -vvv
```

## 개발 버전으로 업데이트
```
composer require encore/laravel-admin:dev-master -vvv
```

## 지정된 버전으로 업데이트
```
composer require encore/laravel-admin: 1.6.15 -vvv
```

## 스캐폴딩 변경 사항 적용
- 각 버전의 프론트앤드 리소스는 변경될 가능성이 있다.
```
/ / Force to publish release assets files
Php artisan vendor:publish --tag=laravel-admin-assets --force

// Force to publish release lang files
php artisan vendor:publish --tag=laravel-admin-lang --force

/ / Clean up the view cache
Php artisan view:clear
```
- 업데이트 후 브라우저 캐시를 정리하여 확인할 것.
