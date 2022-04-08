## 즉시로딩(eager-loading)과 지연로딩(lazy-loading)
- 엘로퀀트에서 연관 모델을 접근할 때 기본적으로 지연로딩이 이뤄진다.
- `$modelA->modelB()`에서는 모델 A에서 모델 B를 접근할 때 지연로딩이 이뤄지는 것.
- 또는 `'A 엘로퀀트 모델의 결과값의 싱글 레코드 정보를 담고 있는 객체'->' A 엘로퀀트 모델과 연관 관계를 가진 B 엘로퀀트 모델'` 방식으로 접근을 한다. 이 때는 B 엘로퀀트를 접근할 때 멤버 메소드로 접근하는 것이 아니라 멤버 변수로 접근하는 방식을 취한다.
- 지연로딩이란 모델 A의 데이터를 쿼리를 통해서 가져온 다음 모델 A의 각 레코드 데이터에 대해서 모델 B의 데이터를 쿼리를 통해 가져오는 방식이다.
- 엘로퀀트는 상위모델을 조회할 때 연관 관계를 맺고 있는 하위 모델을 즉시 로딩(eager-loading) 할 수 있는 기능을 제공한다. (하위 모델이 상위 모델을 즉시 로딩할 수 있는 기능을 지원하지는 않는다.)

## N+1 문제
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
- 한 명의 작가는 여러 책을 쓸 수 있다. 따라서 한 명의 작가는 여러 책에 대응하므로 작가는 상위 모델, 책은 하위 모델이 된다.

```
use App\Models\Book;

$books = Book::all();

foreach ($books as $book) {
    echo $book->author->name;
}
```
- `$books = Book::all();`에서 `$book`은 Book 모델이 가지고 있는 모든 책의 리스트를 가지고 있다.
- `foreach ($books as $book)`는 `$books` 리스트에서 각 레코드를 조회하기 위해서 순회 코드를 작성하였다.
- `$book->author` `$books`의 각 레코드 데이터를 가진 `$book` 객체에서 `author` 모델에 접근한다.
- `$book->author` 각 레코드에 대해서 `author` 모델에 접근할 때 `author` 모델의 데이터를 들고 오기 위해 쿼리를 날린다.
- `$books`의100개의 레코드에 대해 지연로딩을 하게 되면 `$books` 데이터를 가져오는 쿼리 하나와 100개의 레코드에 대한 각각의 `author`을 가져오는 쿼리를 포함하여 101개의 레코드에 대해 지연로딩을 한다.


## eager-loading
```
$books = Book::with('author')->get();

foreach ($books as $book) {
    echo $book->author->name;
}
```
- 즉시 로딩은 `Book` 모델의 쿼리를 날릴 때 연관 되어 있는 `author` 모델의 데이터를 함께 가져오는 역할을 한다.
- 따라서 루프 내의 `$book->author` 코드를 사용할 때 이미 `$books`에 저장되어 있는 정보를 이용하기 때문에 이 코드를 실행하는 시점에서 별도의 쿼리를 날리지 않는다.

#### 실행 쿼리
```
select * from books

select * from authors where id in (1, 2, 3, 4, 5, ...)
```
- `books` 테이블의 데이터를 모두 가져오고 `books` 테이블이 가지고 있는 authors 참조 키에 해당하는 id를 `authors` 테이블에서 가지고 온 후 엘로퀀트 모델 내부에서 매핑을 해서 객체화된 접근을 허용한다.

## 상위 모델만 eager loading이 가능한 이유
- 상위 모델은 기본키를 가지고 있는 테이블을 매핑한 모델을 의미하고 하위 모델은 상위 모델의 참조 키를 가지는 테이블을 매핑한 모델을 의미한다.
- 상위 모델의 각각의 레코드의 기본키에 대응하는 하위 모델의 레코드는 여러개인 반면 하위 모델의 레코드의 참조키에 대응하는 상위 모델의 레코드는 하나이다.


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

## 여러 연관관계 모델에 대한 eager loading
```
$books = Book::with(['author', 'publisher'])->get();
```
- Book 모델에서 연관 관계를 정의한 메소드 명칭을 적는 것으로 eager 로딩을 할 수 있다.
- Book 모델과 연관 관계를 정의한 Author 모델과 연관 관계를 갖는 `author` 메소드를 지정, Book 모델과 연관 관계를 정의한 Publisher 모델과 연관 관계를 갖는 `publisher` 메소드를 지정
- `$books->author->'Author 모델의 멤버에 접근'`, - `$books->publisher->'Publisher 모델의 멤버에 접근'` 방식으로 사용할 수 있다.


## 중첩된 모델에 대한 eager loading
```
$books = Book::with('author.contacts')->get();
```
- 연관관계의 연관관계를 즉시로딩 하기 위해 사용한다.
- `$books` 객체는 Book 모델의 연관 모델인 Author 모델을 미리 객체에 담아 놓고 Author 모델의 연관 모델인 Contacts 모델도 미리 $books 객체에 담아 놓는다.
- `$books->author->contacts->'Contacts 모델의 멤버에 접근'`의 방식으로 사용한다.


## morphTo 연관관계
### 테이블 간 연관관계
- Calendar:Event의 관계를 1:N
- Photo:Tag의 관계를 N:M
- Author:Post의 관계를 1:N
이라고 하자.

```
<?php

use Illuminate\Database\Eloquent\Model;

class ActivityFeed extends Model
{
    /**
     * Get the parent of the activity feed record.
     */
    public function parentable()
    {
        return $this->morphTo();
    }
}
```

## Eager 로딩에서 컬럼 지정하기
- 기본적으로 즉시 로딩을 사용할 때 엘로퀀트 모델에서 지정한 모든 컬럼을 다 들고 온다.
- 특정한 컬럼을 지정하여 데이터를 가져오고 싶을 때 사용한다.
```
$books = Book::with('author:id,name,book_id')->get();
```




## Reference
- https://laravel.kr/docs/8.x/eloquent-relationships#eager-loading
