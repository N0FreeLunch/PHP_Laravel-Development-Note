# 엘로퀀트 관계 (Relationships)

## 릴레이션 접근

### 동적 메소드
- 엘로퀀트 모델에 관계를 걸어서 릴레이션을 통해서 접근하는 경우, 마치 메소드 처럼 `기준모델->관계모델->관계모델속성` 이런식으로 메소드처럼 접근할 수 있다. 
- `기준모델->관계모델`은 기준 모델에서 관계 모델의 명칭으로 메소드를 정의하여 연결한다.
- `기준모델->관계모델->관계모델속성`은 마치 기준모델에 관계 모델이 있는 것 처럼 사용한다. 이런 자기 클래스/객체에 존재하는 메소드를 접근하지 않고 가상의 메소드를 만들어서 접근하는 방식을 `동적`이란 접미어를 써서 정의하며, 동적 속성을 통해 엘로퀀트를 사용한다고 한다.

## 1:1(일대일) 관계 정의하기
- 두 테이블이 가진 각각의 레코드가 일대 일 관계일 경우
- 두 테이블을 A, B라고 할 때 테이블 A의 일부 레코드가 테이블 B의 일부 레코드와 1:1 대응이어도 문제가 없다. 모든 레코드가 꼭 대응 관계여야 하는 것은 아니다. 단, 대응되지 않은 나머지 레코드는 대응 관계가 없어야 한다.
- 두 테이블의 1:1 대응을 정의하기 위해서는 각 테이블의 유니크한 속성이 서로 같아서 서로 공유될 수 있어야 한다.
- 엘로퀀트에서 지원하는 관계는 기본적으로 A 테이블의 기본키와 B 테이블의 

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
- 1:1 대응이 되는 모델의 이름을 메소드로 만든다. `public function phone()`은 Phone이란 모델을 메소드화 하기 위해 앞 글자를 소문자(phone)로 하였다. 이는 엘로퀀트 모델을 메소드체이닝 할 때 어떤 모델과 연결하는지 알게 하기 쉽게하기 위해 lowercase의 모델로 한다.
- 모델이 1:1 대응임을 알려주기 위해 hasOne 메소드를 호출하고 이를 리턴한다.
- `$this->hasOne('App\Models\Phone')`부분에는 User엘로퀀트 모델과 1대1 대응이 되는 엘로퀀트 모델을 넣어 준다.

```
$phone = User::find(1)->phone;
```
- user 모델은 user 테이블의 기본키의 값이 1인 대상과 1:1 대응되는 phone 테이블의 레코드를 찾는다.
- 만약 대응되는 레코드가 phone 테이블에 존재하지 않는다면? 속성 값이 없는 phone 모델의 인스턴스가 나올 것 같다.

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


## 1:N 관계 정의하기
- 참조 되는 테이블(reference)의 기본키와 참조하는 테이블의 외래키가 서로 매칭되는 관계
- 두 테이블 A, B에 대하여 테이블 A의 레코드와 테이블 B의 레코드가 

### 시나리오
- user 테이블과 phone 테이블이 존재하고 phone 테이블의 user_id 필드가 user 테이블의 기본키를 참조하고 있는 관계일 때.


---

## Reference
- https://laravel.kr/docs/8.x/eloquent-relationships
