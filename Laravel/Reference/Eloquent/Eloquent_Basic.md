# Eloquent Basic
## 액티브 레코드
- [ruby on rails](https://railsguides.kr/active_record_basics.html)의 설명을 보자.
> 액티브 레코드는 MVC의 M (모델)에 해당하며, 비즈니스 데이터 및 로직를 나타내는 시스템 계층이다. 액티브 레코드는 데이터베이스에 데이터를 지속적으로 저장해야 하는 비즈니스 객체의 작성 및 사용을 용이하게 한다. 이것은 Object Relational Mapping(ORM: 객체와 관계형 데이터베이스를 연결하는 작업) 시스템을 기술해 놓은 것으로 액티브 레코드 패턴을 구현한 것이다.
### 액티브 레코드 패턴
> 마틴 파울러(Martin Fowler)는 그의 저서 Patterns of Enterprise Application Architecture 에서 액티브 레코드를 기술했다. 액티브 레코드에서 객체란 영구 데이터와 그 데이터를 다루는 동작을 모두 가지고 있다. 데이터 액세스 로직이 액티브 레코드 객체 내에 포함되어 있기 때문에, 그 객체를 사용하는 사람들은 객체내에 포함된 로직에 따라 데이터베이스로부터 읽고 쓰는 법을 알게 될 것이다.

- 리펙토링의 대가인 마틴 파울러가 액티브레코드 패턴을 제시한 것을 보면 좀 더 이해하기 쉽고 다루기 쉬운 코드를 만들기 위해서 ORM 방식이 좋다라고 한 것으로 생각 될 수 있다.
- 실제로 [laravel-best-practice](https://github.com/alexeymezenin/laravel-best-practices#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays) 부분을 보면 베스트 프렉틱스에서는 쿼리 빌더 보다 엘로퀀트를 권장하고 있다.
> Prefer to use Eloquent over using Query Builder and raw SQL queries. Prefer collections over arrays



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

## 모델 멤버에 세팅하는 값

### 테이블과 엘로퀀트 모델을 연결하기
- 멤버는 private가 아닌 protected를 사용했다. 이는 엘로퀀트 모델을 상속해서 사용할 가능성을 열어 놓은 것이라고 볼 수 있다.

### 테이블명
- 연관 테이블을 지정하지 않을 경우 : 엘로퀀트 모델 클래스 이름의 복수형을 스이크 케이스로 변환한 이름이 사용 됨
- 예를 들어 엘로퀀트 모델의 클래스 이름이 AirTrafficController 으로 되어 있다면, air_traffic_controllers 테이블을 연결한다.

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'my_flights';
}
```
- 엘로퀀트 모델의 멤버에 $table을 집어 넣는 방식으로 사용한다.


#### Model suffix에 관하여 
- 라라벨에서 `php artisan make:model 모델명`을 사용해서 모델 이름을 만들 때, \*\*\*\*Model이라고 이름을 붙이지 않는다. 왜냐하면, 그냥 모델이 아닌 엘로퀀트 모델이기 때문이다. 엘로퀀트 모델의 디비 테이블명의 자동매칭 때문에 엘로퀀트 모델명에 Model이 붙게 되면 \_model이 붙게 된다. 그럼 \*\*\*\*Model 부분의 Model을 자동 제외해도 되지 않냐는 말을 할 수도 있다.
- 라라벨은 기본적으로 '라우터 - 컨트롤러 - 모델' 이런 방식으로 구성되어 있다. 도메인 주도 설계를 한다면 '라우터 - 컨트롤러 - 레포지토리 - 모델' 또는 '라우터 - 컨트롤러 - 서비스 - 레포지토리 - 모델' 이런식으로 만들 수도 있다. 기본적으로는 '라우터 - 컨트롤러 - 모델'으로 구성되어 있고 컨트롤러와 모델의 명칭을 구별해야 하는데 모델에는 \*\*\*\*Model 이라고 하지 않고 컨트롤러에는 \*\*\*\*Controller라고 한다. 간단하게 만들기 위해 Model이나 Controller에서 하나는 생략해 주었다. 그런데 도메인 모델을 적용한다던지 로직을 외부에 맡기는 방식이 되어 버리면 모델명과 컨트롤의 로직을 맡긴 다른 부분의 명칭을 구분 해 줘야 한다. \*\*\*\*Service 또는 \*\*\*\*Repository 등으로 접두어를 붙여 줘야 Model과 구분할 수 있다. 컨트롤러의 로직을 다른 곳으로 빼면 항상 이런 접미어를 붙여서 구분을 해 줘야 Model과 구분이된다.  '라우터 - 컨트롤러 - 모델'이런 단순한 모델이었을 때는 문제가 없는데, 구조가 이 틀을 벗어나는 순간인 컨트롤러 로직을 컨트롤러 클래스가 아닌 다른 곳에 두고 넣는 방식일 경우, 접두어 네이밍을 해 줘야 하는 문제가 발생한다.


### Primary Keys
- Eloquent 모델은 하나의 테이블을 매핑한다. 또한 각 테이블의 컬럼을 매핑한다. 테이블의 프라이머리 키는 다른 테이블의 관계를 설정하거나, 데이터를 넣을 때 auto increament 등을 파악하고 처리하기 위해 엘로퀀트 모델에 프라이머리 키가 무엇인지 알려 줘야 한다.
- 기본적으로 프라이머리키가 지정되어 있지 않을 경우, `id` 컬럼명을 자동으로 매칭한다.
- 수동으로 프라이머리 키를 매핑할 때는 `$primaryKey = 'flight_id'`을 세팅한다.

```
?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The primary key associated with the table.
     *
     * @var string
     */
    protected $primaryKey = 'flight_id';
}
```
- protected를 붙인 것은 엘로퀀트 모델을 상속해서 사용할 가능성을 열어 놓은 것.

- Eloquent는 primary key가 데이터베이스 테이블에서 설정된 auto increament 속성을 가지고 있다고 가정한다.
- auto increament가 프라이머리 키에 세팅되어 있지 않다면 엘로퀀트에 수동으로 `public $incrementing` 속성을 `false`로 설정하여 프라이머리 키 값이 자동으로 증가하는 값이 아니라고 알려줘야 한다.

```
<?php

class Flight extends Model
{
    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;
}
```

- primary key가 정수값이 아니라면, 모델의 `protected $keyType` 속성을 타입에 맞게 설정해 줘야 한다.
```
<?php

class Flight extends Model
{
    /**
     * The "type" of the auto-incrementing ID.
     *
     * @var string
     */
    protected $keyType = 'string';
}
```

### timestamp

#### 타임스템프의 컬럼의 존재유무
- Eloquent는 테이블에 created_at과 updated_at이 존재한다고 가정한다. 이 컬럼은 자동 매핑을 하는데 자동 매핑하지 않게 하기 위해서는 `$timestamps` 속성을 false로 지정한다.
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Indicates if the model should be timestamped.
     *
     * @var bool
     */
    public $timestamps = false;
}
```
- 접근제한자가 다른 속성들이 protected로 설정된 것과는 달리 public으로 설정되어 있는데 특별한 의미가 있는지 (?)

- 타임스템프 컬럼에 데이터가 저장될 때는 리눅스 타임을 사용한다. 그리고 쿼리로 꺼내 쓸때 특별한 포멧으로 뽑지 않는다면 yyyy-mm-dd hh:mm:ss 형식으로 나온다. 엘로퀀트를 쓸 때  yyyy-mm-dd hh:mm:ss 형식이 아닌 다른 포멧으로 뽑게 하려면  `$dateFormat` 속성을 지정한다.

>  이 속성은 날짜 속성이 데이터베이스에 저장될 때의 형식과 모델이 배열이나 JSON으로 직렬화-serialization되었을 때의 형식을 결정합니다.

- 위 의미를 좀 더 구체적으로 파악할 필요가 있음

#### 타임스템프의 표시 형식
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The storage format of the model's date columns.
     *
     * @var string
     */
    protected $dateFormat = 'U';
}
```

```
protected $dateFormat = 'Y-m-d H:i:s';
```

```
protected $dateFormat = 'Y-m-d';
```
- 유닉스 타임 : 1970.01.01부터 얼마의 시간이 지났는지 파악하는 것.
- 옵션에 대한 설명을 보면 `protected $dateFormat = 'U';`는 유닉스 타임을 반환하도록 만드는 것이며 옵션에 대한 자세한 지정 방식은 php의 date format을 사용하므로 https://www.php.net/manual/en/function.date.php 링크를 참고한다.

#### CREATED_AT, UPDATED_AT 테이블명
- 엘로퀀트는 기본적으로 CREATED_AT, UPDATED_AT가 존재한다고 가정하고 매핑을 하려고 한다. 기본 타임스템프의 컬럼명이 CREATED_AT이 아니거나 UPDATED_AT이 아닐 때 기본 타임스템프 컬럼을 다른 컬럼으로 지정할 수 있다.
```
<?php

class Flight extends Model
{
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'last_update';
}
```



### 데이터베이스 커넥션
- 라라벨에는 하나의 데이터베이스 뿐만 아니라 여러 데이터베이스를 연결할 수 있다.
- 엘로퀀트 모델은 라라벨에 지정되어 있는 기본 데이터베이스를 연결하며, 추가적인 데이터베이스를 연결할 경우에는 라라벨에 설정되어 있는 다른 데이터베이스 지정명을 적어서 엘로퀀트 모델과 연결할 수 있다.
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The connection name for the model.
     *
     * @var string
     */
    protected $connection = 'connection-name';
}
```

### 기본 속성 정의
- 데이터베이스로 부터 받은 데이터에 값이 null으로 되어 있을 때 또는 테이블에 존재하지 않는 컬럼을 뽑을 때 가상의 어트리뷰트를 정의하고 디폴트 값을 세팅하기 위한 것
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The model's default values for attributes.
     *
     * @var array
     */
    protected $attributes = [
        'delayed' => false,
    ];
}
```


---

Reference : https://laravel.kr/docs/8.x/eloquent
