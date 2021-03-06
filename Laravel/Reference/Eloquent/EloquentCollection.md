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
- contains 메소드는 주어진 모델 인스턴스가 컬렉션에 포함되어 있는지 확인할 때 사용한다. 이 메소드는 기본-primary 키 또는 모델 인스턴스를 허용합니다.
```
$users->contains(1);
```
- contains 메소드는 엘로퀀트 컬렉션 리스트에서 지정한 키에 할당된 지정한 값과 일치하는 레코드를 뽑아낸다.
- $key 값이 수인 경우, primary 키 값이 일치하는 대상을 찾는다. 따라서 엘로퀀트 컬렉션 리스트에서 기본키의 값이 1인 대상을 찾는 코드이다.
- 엘로퀀트 컬렉션의 경우 컬렉션 내부의 엘로퀀트 컬렉션 리스트의 하나의 엘리먼트가 연관 배열의 key, value 형식이 아니라, 하나의 레코드 정보를 담고 있는 stdClass 타입의 객체이다. 이런 경우 엘로퀀트 컬렉션의 메소드를 이용해서 stdClass 타입 객체의 primary-key에 해당하는 멤버 값을 매칭한다. primary-key는 엘로퀀트 모델에 설정되어 있는 값이다.

```
$users->contains(User::find(1));
```
- id가 1인 레코드에 해당하는 모델 인스턴스 : `User::find(1)`
- id가 1인 레코드에 해당하는 모델 인스턴스가 $users라는 `App\Models\User::all()`등의 all()이나 get()으로 반환된 컬렉현 안에 포함되어 있는지 확인하는 메소드이다.
- 여러 레코드에서 특정 레코드가 포함되어 있는지 확인하기 위한 것이다. 


### diff 메소드
- syntax : `diff($items)`
- diff 메소드는 엘로퀀트 컬렉션 리스트에서 주어진 엘로퀀트 컬렉션 리스트를 제외한 리스트를 반환한다.
```
use App\User;
$users = $users->diff(User::whereIn('id', [1, 2, 3])->get());
```
- `$users` 리스트에서 `User::whereIn('id', [1, 2, 3]`로 가져온 리스트를 제외한 리스트를 반환한다.
- 참고 : `diff` 메소드는 대상 엘로퀀트 컬렉션 리스트를 제외하는 반면 intersect 메소드는 대상 엘로퀀트 컬렉션 리스트을 뽑아내는 방식이다.

### except 메소드
- syntax : `except($keys)`
- 인자로 프라이머리 키를 나열한 배열을 받아 지정된 기본 키를 가지고 있는 리스트를 제외한 나머지 리스트를 반환한다.

#### 예제
```
$users = $users->except([1, 2, 3]);
```
- diff가 리스트 끼리의 차집합을 하는 기능이었던 것에 반해, 리스트에서 특정 배열에 들어간 번호에 해당하는 프라이머리키를 가진 대상을 제거하겠다는 의미를 가지고 있다. diff의 경우 제외할 리스트를 선택하는 것은 프라이머리 키를 통해 선택하지 않는 방법이 있는 반면 except의 경우 프라이머리 키를 꼭 사용해야 제외할 수 있다는 것이 차이점이다.

### find 메소드
- syntax : find($key)
- $key 값으로는 프라이머리 키 번호, 특정 레코드 인스턴스, 프라이머리 키가 나열된 배열을 받는다.
- $key 값으로는 프라이머리 키 번호를 받으면 해당 프라이머리 키에 해당하는 리스트를 뽑아내며, 특정 레코드 인스턴스를 받으면 해당 레코드에 해당하는 값을 반환 하며 이 때는 리스트 안에 지정한 인스턴스가 존재하는지 확인하는 용도로 사용할 수 있다. 프라이머리 키가 나열된 배열을 지정하게 되면 해당 프라이머리 키에 해당하는 리스트가 뽑힌다.

#### 예제
```
$users = User::all();
$user = $users->find(1);
```
- `$users = User::all()`로 유저 리스트를 가진 엘로퀀트 컬렉션을 받아온다.
- `$users -> find(1)` find 함수를 통해서 프라이머리 키가 1인 레코드를 찾는다.
- 라라벨 기본 컬렉션에는 find라는 함수가 없다. 일반적으로 함수형 라이브러리에는 찾으면 순회를 종료하는 방식으로 find 함수를 만드는데 라라벨 컬렉션에서는 이와 동일한 기능을 가진 메소드는 search() 메소드이다. find 명칭의 메소드는 엘로퀀트 컬렉션의 특정 row를 뽑을 때 사용한다.
- 엘로퀀트의 find 함수와 엘로퀀트 컬렉션의 find 함수를 구분해야 한다. 엘로퀀트의 find 함수는 지정한 프라이머리 키에 해당하는 대상을 쿼리 빌더로 뽑아서 새로운 엘로퀀트 객체를 반환하는 것에 반해, 엘로퀀트 컬렉션의 find 함수는 리스트에서 프라이머리 키에 해당하는 대상을 뽑아내는 역할을 한다.

### fresh
- syntax : `fresh($with = [])`
- 데이터베이스에서 컬렉션의 각 모델의 새로운 인스턴스 가져옴
- 지정된 모든 관계가 eager load 됨
```
$users = $users->fresh();
$users = $users->fresh('comments');
```
- 각각의 레코드에 해당하는 데이터를 데이터베이스에서 가져와서 갱신한다.
- 레코드의 데이터가 변경되어 초기화를 하고 싶을 때, 또는 데이터베이스의 변경이 있어 데이터를 다시 가져와야 할 때 사용한다.
- 각각의 레코드 인스턴스가 연관 관계를 가지고 있는 형태라면, 데이터베이스에서 연관 모델의 데이터도 함께 갱신한다.

### intersect
- syntax : `intersect($item)`
- intersect는 교집합이란 의미를 가지고 잇다.
- 리스트에서 인자로 주어진 리스트와의 교집합 부분에 해당하는 리스트를 뽑아낸다.
- 교집합이기 때문에 `$item`은 엘로퀀트 컬렉션 형식이어야 한다.
```
use App\Models\User;
$users = $users->intersect(User::whereIn('id', [1, 2, 3])->get());
```
- `$users` 엘로퀀트 컬렉션 리스트에서 `User::whereIn('id', [1, 2, 3])`에 해당하는 리스트와 공통되는 대상만 반환한다.

### load
- syntax : `load($relations)`
- 리스트의 각각의 레코드에 연관 모델의 데이터를 추가로 로드한다.
```
$users->load(['comments', 'posts']);
$users->load('comments.author');
```
- `['comments', 'posts']`는 users 모델의 연관 모델인 comments와 posts의 데이터를 각 레코드에 추가적으로 담는다.
- `'comments.author'`는 users 모델의 연관 모델은 comments이며 comments 모델의 연관 모델은 author이다. users 모델과 comments 모델을 매개로 연결되는 author 연관 모델의 데이터를 users 모델에 담는다.

### loadMissing
- syntax : `loadMissing($relations)`
- 리스트의 각각의 레코드에 대해 연관 관계 데이터가 로드 되지 않은 경우, 연관 모델의 데이터를 추가적으로 로드한다. `load`는 리스트 각각의 레코드에 연관 관계 데이터가 있든 없든 추가적으로 지정한 연관 모델의 데이터를 로드하지만, `loadMissing`은 지정한 연관 관계가 로드 되지 않았을 때만 추가적인 로드를 한다.
```
$users->loadMissing(['comments', 'posts']);
$users->loadMissing('comments.author');
```
- `['comments', 'posts']`는 users 모델의 연관 모델인 comments와 posts의 데이터를 각 레코드에 추가적으로 담는다. 단, comments가 이미 로드 되어 있다면 comments 연관 모델의 데이터를 로드하지 않고 posts가 이미 로드 되어 있다면 posts 연관 모델의 데이터를 로드하지 않는다.
- `'comments.author'`는 users 모델의 연관 모델은 comments이며 comments 모델의 연관 모델은 author이다. users 모델과 comments 모델을 매개로 연결되는 author 연관 모델의 데이터를 users 모델에 담는다. 단, author 연관 모델의 데이터가 이미 로드 되어 있다면 author 연관 모델의 데이터를 로드하지 않는다.

### modelKeys
- syntax : `modelKeys()`
- 엘로퀀트 컬렉션 리스트의 각각의 레코드가 가지고 있는 프라이머리 키의 리스트를 배열로 반환한다.
```
$users->modelKeys();

// [1, 2, 3, 4, 5]
```

### makeVisible 메소드
- syntax : makeVisible($attributes)
- 엘로퀀트 모델에서 데이터를 꺼낼 때 모든 필드 값이 나오지는 않는다. 엘로퀀트 모델에서 $fillable이나 $guarded를 사용하면 드러낼 필드나 숨길 필드를 설정할 수 있다. 필드가 숨겨지게 되면 기본적으로는 결과 값에 숨겨진 필드와 그 값이 나오지 않지만 makeVisible 메소드를 사용하면 숨겨져 있는 필드가 드러나게 된다.
```
$users = $users->makeVisible(['address', 'phone_number']);
```

### makeHidden
- syntax : `makeHidden($attributes)`
- 엘로퀀트 컬렉션 리스트의 각각의 레코드에 지정한 멤버가 로드되지 않도록 한다.
```
$users = $users->makeHidden(['address', 'phone_number']);
```
- `$users` 엘로퀀트 컬렉션 리스트의 각 레코드에 대해 레코드가 address, phone_number 멤버를 가지고 있다면 이 멤버를 로드하지 않으므로 멤버로 접근하지 못한다.

### only
- syntax : `only($keys)`
- 엘로퀀트 컬렉션 리스트에서 주어진 배열의 엘리먼트에 대응하는 프라이머리키를 가진 레코드만을 선택적으로 반환한다.
```
$users = $users->only([1, 2, 3]);
```
- `$users` 엘로퀀트 컬렉션에서 프라이머리 키가 1, 2, 3인 대상만을 남긴다.

### toQuery()
- syntax : `toQuery()`
- 엘로퀀트 컬렉션 리스트의 각각의 레코드는 엘로퀀트 모델의 여러 필드들의 값을 로드하고 있다. 하지만 이 레코드의 각각은 데이터를 로드한 집합에 불과하다. 데이터베이스의 데이터를 업데이트 하기 위해서는 엘로퀀트 모델을 사용해야 하지만 엘로퀀트 컬렉션 리스트와 그 레코드를 사용해서는 업데이트를 할 수 없다. 엘로퀀트 컬렉션 리스트로 데이터베이스의 값을 업데이트 하기 위해서 사용하는 것이 `toQuery()` 메소드이다.
- 엘로퀀트 컬렉션 리스트에서 레코드 각각이 가지고 있는 기본키 모두를 쿼리 빌더의 `whereIn(기본키명, [...대상 기본키])`의 조건의 '대상 기본키'를 세팅한 쿼리 빌더를 반환한다.
- 기본 쿼리 빌더 인스턴스(Illuminate\Database\Query\Builder)가 아닌 엘로퀀트 쿼리빌더 인스턴스(Illuminate\Database\Eloquent\Builder)를 반환한다.
```
use App\Models\User;

$users = User::where('status', 'VIP')->get();

$users->toQuery()->update([
    'status' => 'Administrator',
]);
```
- `$users->toQuery()`쿼리빌더 인스턴스를 반환했기 때문에 쿼리 빌더의 update 메소드를 사용하는 것이 가능하다.

### unique
- syntax : `unique($key = null, $strict = false)`
- 엘로퀀트 컬렉션 리스트에서 기본키가 같은 레코드는 제외하고 기본키 중복이 없는 레코드만을 뽑아낸다.
```
$users = $users->unique();
```

### 커스텀 엘로퀀트 컬렉션
```
<?php

namespace App\Models;

use App\Support\UserCollection;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Create a new Eloquent Collection instance.
     *
     * @param  array  $models
     * @return \Illuminate\Database\Eloquent\Collection
     */
    public function newCollection(array $models = [])
    {
        return new UserCollection($models);
    }
}
```
- 앨로퀀트 모델에서 결과 값으로 리스트 형식의 데이터를 뽑을 경우, 엘로퀀트 컬렉션 타입의 인스턴스를 반환한다.
- 엘로퀀트 모델에서 엘로퀀트 컬렉션을 만들 때, 모델에 `newCollection`을 메소드를 통해서 새로운 컬렉션 오브젝트를 반환하면 엘로퀀트 모델에서 엘로퀀트 컬렉션 타입의 인스턴스를 반환할 때 새롭게 정의된 컬렉션 인스턴스를 반환한다.
- `return new UserCollection($models);`에서 `UserCollection` 오브젝트를 커스터마이징 하면 사용자 정의 엘로퀀트 컬렉션의 형태를 만들 수 있다.
- 엘로퀀트 모델에 `newCollection` 메소드를 정의하면 엘로퀀트 모델을 통해서 `Illuminate\Database\Eloquent\Collection` 인스턴스를 반환할 때 마다 사용자 정의 컬렉션 인스턴스를 기본 컬렉션 인스턴스 대신에 사용한다. `newCollection`메소드의 리턴값으로 사용자 정의로 지정된 컬렉션 인스턴스를 만들 수 있다.


---

## Reference
https://laravel.kr/docs/8.x/eloquent-collections
