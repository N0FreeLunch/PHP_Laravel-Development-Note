# 엘로퀀트 관계 (Relationships)

## 1:1(일대일) 관계 정의하기
- 두 테이블이 가진 각각의 레코드가 일대 일 관계일 경우
- 두 테이블을 A, B라고 할 때 테이블 A의 일부 레코드가 테이블 B의 일부 레코드와 1:1 대응이어도 문제가 없다. 모든 레코드가 꼭 대응 관계여야 하는 것은 아니다. 단, 대응되지 않은 나머지 레코드는 대응 관계가 없어야 한다.
- 두 테이블의 1:1 대응을 정의하기 위해서는 각 테이블의 유니크한 속성이 서로 같아서 서로 공유될 수 있어야 한다.

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
- 만약 대응되는 레코드가 phone 테이블에 존재하지 않는다면? null이 반환될 것 같은데 확인을 해 봐야 할 듯

> Relationship이 정의되었다면, Eloquent의 동적 속성을 이용하여 관련된 레코드를 찾을 수 있습니다.
> 동적 속성은 모델에 정의된 속성에 접근하는 방식과 같은 방식으로 relationship 메소드에 접근하는 것을 허용합니다.


## 1:N 관계 정의하기
- 참조 되는 테이블(reference)의 기본키와 참조하는 테이블의 외래키가 서로 매칭되는 관계

### 시나리오
- user 테이블과 phone 테이블이 존재하고 phone 테이블의 user_id 필드가 user 테이블의 기본키를 참조하고 있는 관계일 때.
