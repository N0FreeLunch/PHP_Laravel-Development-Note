# Eloquent Basic
## 액티브 레코드
- [ruby on rails](https://railsguides.kr/active_record_basics.html)의 설명을 보자.
> 액티브 레코드는 MVC의 M (모델)에 해당하며, 비즈니스 데이터 및 로직를 나타내는 시스템 계층이다. 액티브 레코드는 데이터베이스에 데이터를 지속적으로 저장해야 하는 비즈니스 객체의 작성 및 사용을 용이하게 한다. 이것은 Object Relational Mapping(ORM: 객체와 관계형 데이터베이스를 연결하는 작업) 시스템을 기술해 놓은 것으로 액티브 레코드 패턴을 구현한 것이다.
### 액티브 레코드 패턴
> 마틴 파울러(Martin Fowler)는 그의 저서 Patterns of Enterprise Application Architecture 에서 액티브 레코드를 기술했다. 액티브 레코드에서 객체란 영구 데이터와 그 데이터를 다루는 동작을 모두 가지고 있다. 데이터 액세스 로직이 액티브 레코드 객체 내에 포함되어 있기 때문에, 그 객체를 사용하는 사람들은 객체내에 포함된 로직에 따라 데이터베이스로부터 읽고 쓰는 법을 알게 될 것이다.

- 리펙토링의 대가인 마틴 파울러가 액티브레코드 패턴을 제시한 것을 보면 좀 더 이해하기 쉽고 다루기 쉬운 코드를 만들기 위해서 ORM 방식이 좋다라고 한 것으로 생각 될 수 있다.


## 모델 정의하기
- 일반적으로 모델에 대한 정의는 app\Models 디렉토리에 한다.
- 네임스페이스를 생성하고 연결 해 주는 composer.json 파일에 의해서 오토로드 되는 곳이라면 어느 곳에든 위치해도 상관없다.
- Eloquent를 사용하기 위해서는 Illuminate\Database\Eloquent\Model의 상속을 받아야 한다.
- app\Models의 모델을 엘로퀀트를 사용하지 않고 쿼리빌더로 만들 수 있는데, 이 때는 Illuminate\Database\Eloquent\Model의 상속이 필요없다. 엘로퀀트 ORM을 사용하기 위해서 Illuminate\Database\Eloquent\Model를 상속한다.
- 엘로쿼트 모델을 만들기 위한 기본 보일러 플레이트는
```
php artisan make:model Flight
```
를 통해서 쉽게 만들 수 있다.

### 엘로퀀트 모델과 마이그레이션 스키마 동시에 생성하기
- 엘리퀀트 모델을 생성 할 때, 마이그레이션 스키마를 만들 수 있다.
```
php artisan make:model Flight --migration
```
또는
```
php artisan make:model Flight -m
```

### Eloquent 모델 컨벤션
- flights 테이블에서 정보를 찾거나 저장할 때 쓸 Flight 모델의 예
- 앤터티에 관한 정보를 모아둔 테이블은 복수형을 쓰지만, 모델은 단수형으로 사용한다.
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    //
}
```

### 테이블과 엘로퀀트 모델을 연결하기
#### 테이블 이름 자동 지정 방식
- 연관 테이블을 지정하지 않을 경우 : 엘로퀀트 모델 클래스 이름의 복수형을 스이크 케이스로 변환한 이름이 사용 됨
- 예를 들어 엘로퀀트 모델의 클래스 이름이 AirTrafficController 으로 되어 있다면, air_traffic_controllers 테이블을 연결한다.
- 라라벨에서 `php artisan make:model 모델명`을 사용해서 모델 이름을 만들 때, \*\*\*\*Model이라고 이름을 붙이지 않는다. 왜냐하면, 그냥 모델이 아닌 엘로퀀트 모델이기 때문이다. 엘로퀀트 모델의 디비 테이블명의 자동매칭 때문에 엘로퀀트 모델명에 Model이 붙게 되면 \_model이 붙게 된다. 그럼 \*\*\*\*Model 부분의 Model을 제외해도 되지 않냐는 말을 할 수도 있다.


---

Reference : https://laravel.kr/docs/8.x/eloquent
