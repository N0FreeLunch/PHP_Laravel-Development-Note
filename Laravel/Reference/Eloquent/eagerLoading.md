## 즉시로딩(eager-loading)과 지연로딩(lazy-loading)
- 엘로퀀트에서 연관 모델을 접근할 때 기본적으로 지연로딩이 이뤄진다.
- `$modelA->modelB()`에서는 모델 A에서 모델 B를 접근할 때 지연로딩이 이뤄지는 것.
- 지연로딩이란 모델 A의 데이터를 쿼리를 통해서 가져온 다음 모델 A의 각 레코드 데이터에 대해서 모델 B의 데이터를 쿼리를 통해 가져오는 방식이다.
- 엘로퀀트는 상위모델을 조회할 때 연관 관계를 맺고 있는 하위 모델을 즉시 로딩(eager-loading) 할 수 있는 기능을 제공한다.


## 상위 모델만 eager loading이 가능한 이유
- 상위 모델은 기본키를 가지고 있는 테이블을 매핑한 모델을 의미하고 하위 모델은 상위 모델의 참조 키를 가지는 테이블을 매핑한 모델을 의미한다.
- 상위 모델의 각각의 레코드의 기본키에 대응하는 하위 모델의 레코드는 여러개인 반면 하위 모델의 레코드의 참조키에 대응하는 상위 모델의 레코드는 하나이다.
- 모델을 로딩할 때 특정한 지정을 하지 않는다면 여러 레코드를 가지고 있다. 이 여러 레코드에 대해 각각이 여러 레코드를 가지는 방식으로 로딩하는 것은 지나치게 많은 로딩을 필요로 한다.


```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    /**
     * Get the author that wrote the book.
     */
    public function author()
    {
        return $this->belongsTo(Author::class);
    }
}
```
