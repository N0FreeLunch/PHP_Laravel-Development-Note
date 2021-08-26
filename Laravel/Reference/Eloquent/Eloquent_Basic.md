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
- 이 부분은 다시 살펴 볼 필요가 있음
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

## 모델 조회하기
- 엘로퀀트 모델은 기본적으로 디비의 데이터를 필터링을 할 수 있는 제한적인 기능을 가지고 있으며 이 제한적인 기능으로 필터링 된 값을 반환할 때는 하나의 row 객체 또는 여러개의 row로 구성된 컬렉션을 반환한다.
- raw 쿼리 또는 쿼리빌더를 사용하면 디비의 자원을 활용해서 데이터를 필터링하고 데이터의 표시를 바꾸는 등의 작업을 할 수 있지만, 컬렉션을 반환하는 엘로퀀트를 사용하면 이는 컬렉션 메소드를 사용해서 데이터를 필터링 해야 하므로 php 서버의 자원을 사용하게 된다.
- 엘로퀀트 모델은 내부적으로는 쿼리 빌더로 만들어져 있다. 엘로퀀트 모델을 사용할 때 하나의 row 객체 또는 여러 row로 구성된 컬렉션을 반환하는 것 대신에 쿼리 빌더를 반환하여 쿼리 빌더의 메소드 체이닝을 사용할 수 있는 기능을 제공한다.
- 엘로퀀트 모델에 쿼리 빌더를 연결하면 쿼리 빌더의 모든 기능을 엘로퀀트 모델에 연결해서 사용할 수 있다. 단지 쿼리 빌더와 차이점은 엘로퀀트에 테이블이 정해져 있기 때문에 쿼리빌더의 테이블을 연결하지 않아도 된다는 점이다.
- `querybuilder -> table('테이블명')` 이렇게 쓰지 않아도 쿼리빌더 메소드를 연결 할 수 있다.
```
<?php

$flights = App\Models\Flight::all();

foreach ($flights as $flight) {
    echo $flight->name;
}
```
- 위 코드를 보면 엘로퀀트 모델에서 테이블의 모든 데이터를 가져와서 순회를 하여 name을 뽑았다.
```
$flights = App\Models\Flight::where('active', 1)
               ->orderBy('name', 'desc')
               ->take(10)
               ->get();
```
- 쿼리 빌더를 연결하여 애초에 DB에서 데이터를 가져올 때 쿼리 자체에 조건을 걸어서 데이터를 가져오게 하여 애플리케이션 서버의 자원을 쓰는 것이 아니라, 웹 서버의 자원을 쓰는 방식을 사용한다.

## 모델 리프레시
- 엘로퀀트 모델은 캐시 기능을 가지고 있다. 디비에서 데이터를 가져와서 엘로퀀트에 저장을 하고 엘로퀀트 모델을 통한 여러번의 데이터를 꺼낼 경우 한번 실행한 쿼리 단위만 실행하고 캐시하여 캐시된 값을 꺼내 쓰도록 만들어져 있다. 엘로퀀트만을 사용한다면 테이블의 데이터가 변했을 때, 자동으로 관련 테이블의 데이터를 조회하는 엘로퀀트의 데이터를 자동으로 리프레시한다. 하지만, 엘로퀀트를 사용하지 않는 방법 쿼리빌더나 raw 쿼리나 외부의 요인으로 테이블의 데이터가 달라졌을 가능성이 존재할 때, 수동으로 리프레시를 하는 방법을 사용한다. 
```
$flight = App\Models\Flight::where('number', 'FR 900')->first();

$freshFlight = $flight->fresh();
```

```
$flight = App\Models\Flight::where('number', 'FR 900')->first();

$flight->number = 'FR 456';

$flight->refresh();

$flight->number; // "FR 900"
```
- 이 예시는 first() 메소드를 통해서 데이터를 가져온 이후 테이블의 데이터가 변했을 때 리프레시를 하여 새로 데이터를 가져와서 값이 달라진 것을 보여주는 것이다.

## 모델 컬렉션
- 엘로퀀트 모델로 데이터를 가져왔을 때, 단일 row가 아닌 복수의 row를 가지고 오는 get, all 메소드를 사용해서 데이터를 가져왔다면 Illuminate\Database\Eloquent\Collection 인스턴스 타입의 값을 반환한다. 그래서 라라벨 컬렉션의 메소드들을 붙여서 함수형적인 표현으로 데이터를 다룰 수 있게 한다.
```
$flights = $flights->reject(function ($flight) {
    return $flight->cancelled;
});
```

```
foreach ($flights as $flight) {
    echo $flight->name;
}
```

### 결과 분할하기
- 데이터베이스로 부터 한번에 가져오는 데이터의 크기가 엄청 큰 경우, php의 변수에 데이터를 담을 수 없는 경우가 생긴다. 이 경우 데이터를 일정 길이만큼 뽑아서 php가 처리할 수 있는 만큼 뽑아서 처리 로직을 실행하기 위한 방법이다.
- chunk를 사용하면 php로 데이터를 저장하면 데이터의 크기 때문에 문제가 발생하므로 php의 메모리에 저장할 생각을 하지 않는 편이 좋다. 레디스와 같은 캐시 메모리를 사용하여 데이터를 저장하거나 배치 처리와 같이 데이터의 일부만을 처리하여 디비를 수정하는 등의 전체의 데이터를 한번에 사용하지 않는 방식에 사용하기 적절하다.
- php 메모리의 한계를 극복하기 위한 것 뿐만 아니라, 메모리를 절약하는 방식으로 사용할 수도 있다. 조금씩 처리하기 때문에 php가 사용하는 메모리 공간을 절약해서 애플리케이션의 로직 처리에 부하를 줄일 수 있다. 
```
Flight::chunk(200, function ($flights) {
    foreach ($flights as $flight) {
        //
    }
});
```
- Flight 테이블로 부터 데이터를 200 개씩 받아와서 처리 로직을 돌리겠다는 것. 내부 익명함수를 통해서 200개의 row를 받고 200개 각각을 순회하면서 각각은 하나의 row를 가지는 엘로퀀트이다. $flight는 엘로퀀트로 다루면 된다. 엘로퀀트의 메소드를 붙일 수 있다.

### cursor 
- chunk가 일정량의 row를 가져와서 처리하는 것에 반해 cursor는 하나씩 가져와서 처리하는 방식이다.
- 메모리에 디비로 부터 받은 데이터를 메모리에 저장은 하지만 다수의 row를 배열 형식으로 저장하지 않기 때문에 메모리를 많이 절약할 수 있다.
- cursor는 php의 generator 문법을 사용하여 만들어져 있다.
- php의 변수에 데이터를 담는 크기에는 한계가 있다. 하지만, 디비 등 외부에서 받은 데이터를 php 변수에 저장하지 않는 방법으로 처리하는 방법이 있다. php에서 데이터베이스의 값을 받을 때 받을 수 있는 용량의 제한은 없어 보이며, 있다고 해도 php 변수가 가질 수 있는 메모리 범위를 훨씬 상회한다. 
- cursor는 데이터가 메모리에 저장되어 있기는 하지만 이는 php 변수에 담지 않은 상태이다. cursor는 php의 generator 문법을 사용하여 이 데이터를 php 변수에 담지 않고 하나씩 처리할 수 있도록 한다.
- 제너레이터를 사용하면 함수 내의 yield 키워드가 나타나는 지점 yield까지의 값을 반환하고 return과 달리 함수를 종료하지 않고 정지 시킨다. 엄밀히는 값을 반환하면서 반환하는 시점의 상태를 저장하고 있다가 다음 번 함수가 실행될 때 반환 시점 이후, 곧 상태 저장이 된 부분부터 함수를 실행하는 방식으로 구성되어 있다.
```
foreach (Flight::where('foo', 'bar')->cursor() as $flight) {
    //
}
```
- `Flight::where('foo', 'bar')->cursor()`는 제너레이터로 구성되어 있다. foreach 문을 통해서 제너레이터의 실행을 반복할 수 있으며 메모리에 저장된 디비의 row 값을 하나씩 불러와서 처리한다.
- cursor는 제너레이터로 구성되어 있지만 내부 제너레이터의 yield 반환 값은 라라벨의 Illuminate\Support\LazyCollection으로 구성되어 있다.
- LazyCollection을 컬렉션을 바로 순회하지 않고 지연평가라는 방법을 사용한다. 연산할 함수를 한번에 모은 다음 함수들을 모두 합성한 후 연산하는 방식을 사용하기 때문에 cursor()으로 하나의 row에 합성된 함수를 적용하게 되므로 하나씩 처리하는 cursor에도 컬렉션을 적용할 수 있다.
```
$users = App\Models\User::cursor()->filter(function ($user) {
    return $user->id > 500;
});

foreach ($users as $user) {
    echo $user->id;
}
```
- 하나의 모델을 메모리에 불러오는동안 일반적인 많은 라라벨 컬렉션 메소드를 사용할 수 있습니다.


### chunk와 cursor의 차이
- https://stackoverflow.com/questions/45464676/what-is-the-difference-between-laravel-cursor-and-laravel-chunk-method

#### cursor의 메모리 사용
- php 프로세스의 버퍼에 디비로 부터 받은 값을 저장한다. 이는 아직 php 변수에 담겨지지 않은 상태이다. 이 버퍼는 php 각 프로세스의 디비 데이터를 받아오기 위해 저장하는 용도로 사용되는 버퍼이며 php 프로세스 당 하나가 존재한다. 따라서 하나의 프로세스에서 버퍼에 있는 것을 php 변수에 옮겨 가지 않으면 버퍼를 강제로 리프레시 하기 전 까지는 다른 데이터를 버퍼에 저장할 수 없기 때문에 통신이 블럭되는 현상이 생긴다. cursor을 사용할 경우 cursor를 통해 데이터가 다 처리되기 전까지는 디비 연결이 블럭되는 문제가 생긴다.
- 데이터를 가져올 때 쿼리를 한 번 실행하지만 cursor는 데이터를 하나의 row 단위로 가져온다. 제너레이터 문법을 사용하는 것은 실행이 정지 되는 것이기 때문에 php 프로세스의 디비 버퍼에 있는 데이터를 가져오는 작업이 제너레이터의 실행이 끝나기 전까지는 작업이 끝난 상태가 아니라서 php 프로세스의 디비 버퍼는 리프레시 되지 않는 상태가 된다.

#### chunk의 메모리 사용
- 쿼리로 일정량의 row를 호출한 후 일정량의 row에 대해서만 작업한다. cursor가 php 프로세스의 디비 버퍼에 모든 row를 다 받은 데이터를 담아 두는 것과는 달리 일정량의 row만 받기 때문에 버퍼 메모리를 적게 차지한다. 일정량의 데이터를 처리하고 다음 데이터를 받기 위해서 버퍼를 비워야 하기 때문에 php 변수에 row를 저장해야 한다. 버퍼에서 php 변수로 데이터를 옮기면 버퍼는 비워지고 php 변수의 메모리를 차지한다.

- 100,000 records의 경우 스텍오버 플로우에 나온 데이터
|              | Time(sec)  | Memory(MB) |
|--------------|------------|------------|
| get()        |        0.8 |     132    |
| chunk(100)   |       19.9 |      10    |
| chunk(1000)  |        2.3 |      12    |
| chunk(10000) |        1.1 |      34    |
| cursor()     |        0.5 |      45    |

- cursor는 빠르지만 메모리를 많이 소모한다. 여러번 쿼리를 디비에 전달해서 실행 결과를 받지 않고 한번에 데이터를 다 가져오기 때문에 많은 데이터를 버퍼에 저장하고 있어서 메모리 사용이 크다. 하지만 디비에서 데이터를 받는 과정이 한번 뿐이기 때문에 IO 대기 시간이 한번 뿐이라 chunk 방식에 비해 굉장히 빠르게 처리한다.
- 반면 chunk는 한번의 쿼리 요청에 일부 row만 가져온다. 저장하는 데이터의 양이 적기 때문에 메모리 소비량이 적다. 데이터를 적게 가져올 수록 메모리를 더 적게 사용하며, 데이터를 많이 가져올 수록 cursor 메모리 사용량에 육박하며 위 추세로 보면 동일한 데이터를 가져왔을 때 php 프로세스의 디비 버퍼에 바이너리 형식으로 저장된 cursor와 달리 php 배열에 저장된 데이터는 메모리를 많이 사용할 수 밖에 없는 구조로 변환이 되기 때문에 cursor와 동일한 100,000개의 데이터를 가져와서 처리를 하면 cursor 보다 훨씬 더 많은 메모리를 사용할 것으로 예상된다.


## 엘로퀀트로 서브쿼리 사용하기



---

Reference : https://laravel.kr/docs/8.x/eloquent
