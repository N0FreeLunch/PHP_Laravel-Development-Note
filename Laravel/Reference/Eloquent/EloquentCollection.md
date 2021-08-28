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
->map(function ($user) {
    return $user->name;
});
```

## 엘로퀀트 컬렉션 메소드
- 엘로퀀트 컬렉션은 기본 컬렉션을 상속 받아서 기본 컬렉션의 모든 메소드를 활용할 수 있게 구성되어 있다.
- 기본 컬렉션의 메소드를 사용할 경우 기본 컬렉션 타입의 객체가 반환되며, 엘로퀀트 컬렉션의 메소드를 사용할 경우 엘로퀀트 컬렉션 타입의 객체가 반환된다.
- 엘로퀀트 컬렉션의 타입만 사용하면 될 텐데 왜 기본 타입으로 반환하느냐는 컬렉션을 사용하는 다른 부분들과 호환되기 위해서가 아닐까 생각한다. (이 부분에 대해서는 검토가 필요)
- 다음 설명은 기본 컬렉션이 아닌 엘로퀀트 컬렉션에서만 사용하는 메소드이다.


### contains 메소드
- syntax : `contains($key, $operator = null, $value = null)`
- contains 메소드는 주어진 모델 인스턴스가 컬렉션에 포함되어 있는지를 결정하는데 사용될 수 있습니다. 이 메소드는 기본-primary 키 또는 모델 인스턴스를 허용합니다.
```
$users->contains(1);
```
- 기본 contains 메소드는 연관 배열의 value 값이 존재하는지 확인한다. 연관 배열의 value 값에 1이란 값이 존재하는지 확인한다.
- 엘로퀀트 컬렉션의 경우 컬렉션 내부의 리스트의 하나의 엘리먼트가 연관 배열의 key, value 형식이 아니라, 하나의 레코드 정보를 담고 있는 stdClass 타입의 객체이다. 이런 경우 엘로퀀트 컬렉션의 메소드를 이용해서 stdClass 타입 객체의 primary-key의 값을 매칭한다. primary-key는 엘로퀀트 모델에 설정되어 있는 값이다.

```
$users->contains(User::find(1));
```
- id가 1인 레코드에 해당하는 모델 인스턴스 : `User::find(1)`
- id가 1인 레코드에 해당하는 모델 인스턴스가 $users라는 `App\Models\User::all()`등의 all()이나 get()으로 반환된 컬렉현 안에 포함되어 있는지 확인하는 메소드이다.
- 여러 레코드에서 특정 레코드가 포함되어 있는지 확인하기 위한 것이다. 

---

## Reference
https://laravel.kr/docs/8.x/eloquent-collections
