## make laravel
```console
mkdir laravel-admin-project
```

## move to project folder
```console
cd laravel-admin-project
```

## install laravel
```console
composer create-project laravel/laravel .
```

## make database (sqlite)
```console
touch database/database.sqlite
```

## database connection setting
- .env file
```txt
DB_CONNECTION=sqlite
#DB_HOST=127.0.0.1
#DB_PORT=3306
#DB_DATABASE=ddd
#DB_USERNAME=root
#DB_PASSWORD=
```

## default migration
```console
php artisan migrate
```

## install laravel-admin
```console
composer require encore/laravel-admin
```

### apply laravel-admin schefolding
```console
php artisan vendor:publish --provider="Encore\Admin\AdminServiceProvider"
```
```console
php artisan admin:install
```

## run server in local development environment
```console
php artisan serve
```

## connection
```
127.0.0.1:8000/admin
```
