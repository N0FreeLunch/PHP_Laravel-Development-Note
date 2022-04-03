## 즉시로딩(eager-loading)과 지연로딩(lazy-loading)
- 엘로퀀트에서 연관 모델을 접근할 때 기본적으로 지연로딩이 이뤄진다.
- `$modelA->modelB()`에서는 모델 A에서 모델 B를 접근할 때 지연로딩이 이뤄지는 것.
- 지연로딩이란 모델 A의 데이터를 쿼리를 통해서 가져온 다음 모델 A의 각 레코드 데이터에 대해서 모델 B의 데이터를 쿼리를 통해 가져오는 방식이다.
- 엘로퀀트는 상위모델을 조회할 때 연관 관계를 맺고 있는 하위 모델을 즉시 로딩(eager-loading) 할 수 있는 기능을 제공한다.

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