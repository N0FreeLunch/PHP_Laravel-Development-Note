# 엘로퀀트 관계 (Relationships)

## 릴레이션 접근

### 동적 메소드
- 엘로퀀트 모델에 관계를 걸어서 릴레이션을 통해서 접근하는 경우, 마치 메소드 처럼 `기준모델->관계모델->관계모델속성` 이런식으로 메소드처럼 접근할 수 있다. 
- `기준모델->관계모델`은 기준 모델에서 관계 모델의 명칭으로 메소드를 정의하여 연결한다.
- `기준모델->관계모델->관계모델속성`은 마치 기준모델에 관계 모델이 있는 것 처럼 사용한다. 이런 자기 클래스/객체에 존재하는 메소드를 접근하지 않고 가상의 메소드를 만들어서 접근하는 방식을 `동적`이란 접미어를 써서 정의하며, 동적 속성을 통해 엘로퀀트를 사용한다고 한다.

## 1:1(일대일) 관계 정의하기
- 두 테이블이 가진 각각의 레코드가 일대 일 관계일 경우
- 두 테이블을 A, B라고 할 때 테이블 A의 일부 레코드가 테이블 B의 일부 레코드와 1:1 대응이어도 문제가 없다. 모든 레코드가 꼭 대응 관계여야 하는 것은 아니다. 단, 대응되지 않은 나머지 레코드는 대응 관계가 없어야 한다.
- 두 테이블의 1:1 대응을 정의하기 위해서는 각 테이블의 유니크한 속성이 서로 같아서 서로 공유될 수 있어야 한다. 이 속성은 여러개의 어트리뷰트가 유니크한 속성을 가지는 특성을 가지고 있으며 이를 슈퍼키라고 부른다. 곧 두 테이블은 유니크한 키의 값의 대응 또는 유니크한 슈퍼키의 값의 대응으로 이뤄진다.
- 엘로퀀트에서 지원하는 관계는 기본적으로 A 테이블의 기본키와 B 테이블의 참조키의 값이 같은 레코드 끼리 이었을 때, 1:1 대응이 이뤄지는 경우에 한한다.

### 시나리오
- user 테이블과 phone 테이블이 존재하고 phone 테이블의 기본키와 user 테이블의 기본키가 서로 같은 경우 또는 phone 테이블의 유니크 필드 세트와 user 테이블의 유니크 필드 세트가 같을 경우

### 엘로퀀트 정의
- User 엘로퀀트 안에 phone 메소드를 만든다.
- phone 메소드는 엘로퀀트 모델 클래스에서 상속받은 hasOne 메소드를 호출하여 이를 return한다.

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Get the phone record associated with the user.
     */
    public function phone()
    {
        return $this->hasOne('App\Models\Phone');
    }
}
```
- 1:1 대응이 되는 모델의 이름을 메소드로 만든다. `public function phone()`은 Phone이란 모델을 메소드화 하기 위해 앞 글자를 소문자(phone)로 하였다. 이는 엘로퀀트 모델을 메소드체이닝 할 때 어떤 모델과 연결하는지 알게 하기 쉽게하기 위해 관계 대상인 모델의 이름을 사용한다. 또한 메소드이기 때문에 모델명의 lowercase로 메소드명을 정한다.
- 모델이 1:1 대응임을 알려주기 위해 hasOne 메소드를 호출하고 이를 리턴한다.
- `$this->hasOne('App\Models\Phone')`부분에는 User엘로퀀트 모델과 1대1 대응이 되는 엘로퀀트 모델을 넣어 준다.
- hasOne을 썼기 때문에 엘로퀀트에서 관계 모델에 접근해서 얻는 레코드가 유일하다는 것을 가정하고 접근하는 방식을 사용할 것.

```
$phone = User::find(1)->phone;
```
- user 모델은 user 테이블의 기본키의 값이 1인 대상과 1:1 대응되는 phone 테이블의 레코드를 찾는다.
- 만약 대응되는 레코드가 phone 테이블에 존재하지 않는다면? 속성 값이 없는 phone 모델의 인스턴스가 나올 것 같다. (확인할 필요가 있음)

### hasOne
- https://laravel.com/api/8.x/Illuminate/Database/Eloquent/Relations/HasOne.html
- `Illuminate\Database\Eloquent\Relations\HasOne`이라는 클래스를 반환한다.

### 외래키 추정
- 기준 모델의 레코드에 대응되는 관계 모델의 레코드를 얻기 위해서는 기준 모델의 기본키와 관계 모델의 '기준테이블명_id' 형태의 컬럼이 매칭될 것이라고 가정한다.
- phone 테이블에 user_id라는 매칭용 컬럼이 있을 것이라고 가정한다.
- 외래키를 수동으로 지정하려면 hasOne의 두 번째 메소드에 대응되는 컬럼명을 입력하면 된다. `return $this->hasOne('App\Models\Phone', 'foreign_key');`는 user_id 말고 foreign_key라는 크를 대응에 사용하겠다는 의미

### 기본키 추정
- 엘로퀀트 관계를 설정할 때, 기준 모델의 레코드에 대응하는 관계 모델의 레코드를 찾는데 사용하는 키는 기준 모델의 기본키를 사용한다. 
- 기본키가 아닌 다른 키를 지정하고 싶다면 `return $this->hasOne('App\Models\Phone', 'foreign_key', 'local_key');` hasOne 메소드의 세번째 인자에 지정할 컬럼 여기서는 'local_key'를 넣는다.

## 1:1 역관계 정의하기
- User 모델에서 Phone 모델로 접근할 때는 기본적으로 user 테이블의 기본키에 phone의 user_id 컬럼을 대응하였다. 
- 역관계에서는 phone 테이블의 user_id 컬럼이 지정한 역관계 모델의 테이블인 user 테이블의 기본키에 대응하는 관계를 설정한다.
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Phone extends Model
{
    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\Models\User');
    }
}
```
- Phone 모델에 user 모델을 접근하기 위한 user 메소드를 만들어 주고 belongsTo 메소드를 리턴한다. belongsTo 메소드의 인자로는 User 모델을 지정한다.

```
/**
 * Get the user that owns the phone.
 */
public function user()
{
    return $this->belongsTo('App\Models\User', 'foreign_key');
}
```
- Phone 모델에서 User 모델을 belongsTo로 지정했을 때, Phone 모델의 컬럼인 외래키 명칭이 user_id가 아닌 경우, 두번째 인자로 외래키를 지정할 수 있다.

```
/**
 * Get the user that owns the phone.
 */
public function user()
{
    return $this->belongsTo('App\Models\User', 'foreign_key', 'other_key');
}
```
- Phone 모델에서 User 모델을 belongsTo로 지정했을 때, User의 기본키가 id가 아닌 경우 세번째 인자로 기본키를 지정할 수 있다. 'id' 대신 'other_key'를 사용한다.

## 1:N 관계 정의하기
- 하나의 테이블의 임의의 하나의 레코드가 다른 테이블의 여러 레코드와 연결되는 경우
- 연결이 될 때 테이블의 속성 값의 일치를 통해 연결되는 경우가 있으며, 여러 속성의 값이 동일한 것을 통해 연결 될 수도 있다.
- 엘로퀀트 모델에서의 일대다의 관계는 참조 되는 테이블(reference)의 기본키와 참조하는 테이블의 외래키가 서로 매칭되는 관계일 경우이다.

### 시나리오
- 블로그에 포스팅에 관한 테이블이 있고 포스팅에 댓글이 달린다고 하자. 그럼 블로그의 글(post) 관한 테이블이 존재할 것이며 댓글에 관한 테이블이 존재한다. 하나의 블로그 글에는 여러개의 댓글이 달릴 수 있다. 곧 하나의 레코드에 대한 다수의 레코드를 연결하는 관계의 예이다.

### 엘로퀀트 정의
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * Get the comments for the blog post.
     */
    public function comments()
    {
        return $this->hasMany('App\Models\Comment');
    }
}
```
- Post 엘로퀀트는 모델은 comments 메소드를 통해 comments 레코드에 접근할 수 있게 만들어 준다.
- comments에 일대다의 관계로 접근하기 위해서는 정의한 메소드에 `return $this->hasMany('App\Models\Comment');`로 hasMany 메소드를 반환해 줘야 한다. 
- hasMany 메소드는 'App\Models\Comment'로 Comment 모델을 인자로 가진다. 

```
$comments = App\Models\Post::find(1)->comments;

foreach ($comments as $comment) {
    //
}
```
- `App\Models\Post::find(1)->comments;`라는 것은 Post 모델에서 특정 레코드를 뽑아서 해당 레코드에 일대다로 대응되는 관계 테이블의 레코들를 얻는다는 의미이다. 
- `find(1)`을 사용해서 하나의 레코드에 대한 다수의 레코드를 얻은 경우 소유모델의 기본키 값이 1인 레코드에 대응하는 관계 테이블 comments의 레코드를 뽑은 것


### 외래키 추정
- Eloquent는 참조모델(Comments)의 외래키 명칭을 소유모델(Post)의 "snake case" 이름에 \_id를 붙인다. 
- Comments 모델은 컬럼 post_id를 가지고 있다고 추정하는 것.
- 외래키 추정을 사용하지 않고 직접 외래키 컬럼 명칭을 지정하려면 hasMany의 두번째 인자로 외래키 명칭을 지정한다. `return $this->hasMany('App\Models\Comment', 'foreign_key');`

### 기본키 추정
- 소유모델의 기본키는 기본적으로 id로 추정한다.
- 기본키 추정을 사용하지 않고 직접 외래키 컬럼 명칭을 지정하려면 hasMany의 세번째 인자로 기본키 명칭을 지정한다. `return $this->hasMany('App\Models\Comment', 'foreign_key', 'local_key');`


### 쿼리 빌더화
```
$comment = App\Models\Post::find(1)->comments()->where('title', 'foo')->first();
```
- 엘로퀀트 그대로 쓰는 경우는 `App\Models\Post::find(1)->comments` comments 멤버로 접근하는 코드만 쓰며 쿼리빌더로 사용하고자 할 때는 `comments()` 메소드를 사용한다.


## 1:N 역관계 정의하기
- 한 테이블의 N개의 레코드에 대해 다른 테이블의 하나의 레코드만 매칭되는 관계를 사용할 때 사용한다. 역관계의 경우 참조하는 모델에서 참조되는 모델로 매칭을 할 때 참조되는 모델의 레코드는 하나만 매칭되기 때문에 1:1의 역관계이든 1:N의 역관계이든 동일 메소드`belongsTo`를 사용하도록 만들어져 있다.
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Models\Post');
    }
}
```
- 댓글(comment) 데이터에 대한 모델 Comment가 키가 참조되는 모델 post에 '속해있다'라는 의미로 belongsTo('App\Models\Post')를 사용한다.


---

## Reference
- https://laravel.kr/docs/8.x/eloquent-relationships
