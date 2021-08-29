# 엘로퀀트 컬렉션

## 엘로퀀트로 부터 데이터 받아 오기
### all()
- 테이블의 모든 데이터를 받아 올 경우 : `App\Models\User::all()`를 사용
- 테이블의 모든 데이터를 받아 오면 컬렉션 형태로 반환한다. 컬렉션의 where 메소드를 사용해서 원하는 컬럼을 뽑을 수 있다.
- `App\Models\User::all() -> where('active', 1);`

### get()
- 엘로퀀트를 쿼리 빌더 방식으로 사용한다.
- `App\Models\User::where('active', 1)->get();`

### 컬렉션 반환
- 엘로퀀트 그대로 all()을 사용해서 데이터를 받거나, 쿼리 빌더를 통해 get()을 통해 데이터를 가져오는 경우 컬렉션 타입으로 데이터가 반환된다.
- get(), all()는 멀티 레코드를 반환한다. 이런 멀티 레코드를 반환하는 방식의 경우에는 컬렉션 타입으로 만들어져 있다.
- 컬렉션 타입은 lluminate\Database\Eloquent\Collection 클래스를 기반으로 한다.
- lluminate\Database\Eloquent\Collection 클래스는 Illuminate\Support\Collection 클래스를 상속받아 만들어 진다. 
- 엘로퀀트 컬렉션이 기본 컬렉션을 상속하는 이유는 기본 컬렉션의 모든 메소드를 엘로퀀트 컬렉션에서도 사용 가능하게 하면서 몇 가지 엘로퀀트 컬렉션만의 기능을 추가해서 사용하기 위해서이다. 또한 기본 컬렉션 메소드를 오버라이딩해서 컬렉션 메소드의 인자로 엘로퀀트 모델을 넣어 사용할 수 있게 만든다.

### 주의점
- 쿼리 빌더 방식으로 사용하게 되면 쿼리 빌더 방식으로 결과 값을 얻어야 하므로 get() 또는 first()를 사용하게 된다. 컬렉션 형식의 데이터로 받기 위해서는 멀티레코드를 반환하는 방식인 get()을 사용해야 한다.

## 컬렉션의 특징
  1. 컬렉션은 php의 Iterators 타입을 가지므로 순회할 수 있는 특징을 가진다.
```
$users = App\Models\User::where('active', 1)->get();

foreach ($users as $user) {
    echo $user->name;
}
```

  2. 컬렉션은 함수형 프로그래밍의 lambda 형식의 표현을 쉽게 사용할 수 있게 도와준다.
```
$users = App\Models\User::all();

$names = $users->reject(function ($user) {
    return $user->active === false;
})
-> map(function ($user) {
    return $user->name;
});
```

## 엘로퀀트 컬렉션 메소드
- 엘로퀀트 컬렉션은 기본 컬렉션을 상속 받아서 기본 컬렉션의 모든 메소드를 활용할 수 있게 구성되어 있다.
- 엘로퀀트 컬렉션의 메소드를 사용할 경우 엘로퀀트 컬렉션 타입의 객체가 반환되며 엘로퀀트 컬렉션에서 기본 컬렉션의 메소드를 사용할 경우 대부분 엘로퀀트 컬렉션 타입의 컬렉션이 반환되지만 일부는 기본 컬렉션 타입의 객체가 반환되는 경우도 있다.
- 쿼리빌더의 소스코드를 보면 기본적으로 엘로퀀트 모델을 받아서 처리하는 로직이 들어가 있다. 기본적으로 엘로퀀트 모델에서의 id를 값을 사용한다. (다른 경우 어떤 게 있나 조사하는 편이 좋아보임)
- 다음 설명은 기본 컬렉션이 아닌 엘로퀀트 컬렉션에서만 사용하거나 엘로퀀트 컬렉션에 의해서 기본 컬렉션과 다른 방식으로 사용할 수 있는 방식이다.

### contains 메소드
- syntax : `contains($key, $operator = null, $value = null)`
- contains 메소드는 주어진 모델 인스턴스가 컬렉션에 포함되어 있는지를 결정하는데 사용될 수 있습니다. 이 메소드는 기본-primary 키 또는 모델 인스턴스를 허용합니다.
```
$users->contains(1);
```
- 기본 contains 메소드는 연관 배열의 key, value 엘리먼트에서 value 값이 존재하는지 확인한다. 연관 배열의 value 값에 1이란 값이 존재하는지 확인한다.
- 엘로퀀트 컬렉션의 경우 컬렉션 내부의 리스트의 하나의 엘리먼트가 연관 배열의 key, value 형식이 아니라, 하나의 레코드 정보를 담고 있는 stdClass 타입의 객체이다. 이런 경우 엘로퀀트 컬렉션의 메소드를 이용해서 stdClass 타입 객체의 primary-key에 해당하는 멤버 값을 매칭한다. primary-key는 엘로퀀트 모델에 설정되어 있는 값이다.

```
$users->contains(User::find(1));
```
- id가 1인 레코드에 해당하는 모델 인스턴스 : `User::find(1)`
- id가 1인 레코드에 해당하는 모델 인스턴스가 $users라는 `App\Models\User::all()`등의 all()이나 get()으로 반환된 컬렉현 안에 포함되어 있는지 확인하는 메소드이다.
- 여러 레코드에서 특정 레코드가 포함되어 있는지 확인하기 위한 것이다. 


### diff 메소드
- syntax : diff($items)
```
use App\User;
$users = $users->diff(User::whereIn('id', [1, 2, 3])->get());
```

### except 메소드
- syntax : except($keys)

### find 메소드
- syntax : find($key)
```
$users = User::all();
$user = $users->find(1);
```
- `$users = User::all()`로 유저 리스트를 가진 엘로퀀트 컬렉션을 받아온다.
- `$users -> find(1)` find 함수를 통해서 프라이머리 키가 1인 레코드를 찾는다.
- 라라벨 기본 컬렉션에는 find라는 함수가 없다. 일반적으로 함수형 라이브러리에는 찾으면 순회를 종료하는 방식으로 find 함수를 만드는데 라라벨 컬렉션에서는 이와 동일한 기능을 가진 메소드는 search() 메소드이다. find 명칭의 메소드는 엘로퀀트 컬렉션의 특정 row를 뽑을 때 사용한다.


### toQuery()
- 엘로퀀트는 기본적으로 기본키를 선택하는 방식으로 레코드를 뽑아낸다.
- 엘로퀀트에 조건이 걸려 있다면 기본적으로 기본키를 whereIn으로 뽑는 쿼리빌더를 반환한다.
- 기본 쿼리 빌더 인스턴스(Illuminate\Database\Query\Builder)가 아닌 엘로퀀트 쿼리빌더 인스턴스(Illuminate\Database\Eloquent\Builder)를 반환한다.
```
$users = App\Models\User::where('status', 'VIP')->get();

$users->toQuery()->update([
    'status' => 'Administrator',
]);
```
- `$users->toQuery()`쿼리빌더 인스턴스를 반환했기 때문에 쿼리 빌더의 update 메소드를 사용하는 것이 가능하다.

---

## Reference
https://laravel.kr/docs/8.x/eloquent-collections
