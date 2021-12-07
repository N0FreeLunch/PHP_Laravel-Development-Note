## Grid default
```php
use App\Models\Movie;
use Encore\Admin\Grid;
use Encore\Admin\Facades\Admin;

$grid = new Grid(new Movie);

// The first column displays the id field and sets the column as a sortable column
$grid->id('ID')->sortable();

// The second column shows the title field, because the title field name and the Grid object's title method conflict, so use Grid's column () method instead
$grid->column('title');

// The third column shows the director field, which is set by the display($callback) method to display the corresponding user name in the users table
$grid->director()->display(function($userId) {
    return User::find($userId)->name;
});

// The fourth column appears as the describe field
$grid->describe();

// The fifth column is displayed as the rate field
$grid->rate();

// The sixth column shows the released field, formatting the display output through the display($callback) method
$grid->released('Release?')->display(function ($released) {
    return $released ? 'yes' : 'no';
});

// The following shows the columns for the three time fields
$grid->release_at();
$grid->created_at();
$grid->updated_at();

// The filter($callback) method is used to set up a simple search box for the table
$grid->filter(function ($filter) {

    // Sets the range query for the created_at field
    $filter->between('created_at', 'Created Time')->datetime();
});
```

## Basic usage
#### 컬럼 추가
```php
// Add the column directly through the field name `username`
$grid->username('Username');
```
- 엘로퀀트 모델을 통해 username 필드의 데이터를 출력
- 출력되는 컬럼명은 Username

```php
// The effect is the same as above
$grid->column('username', 'Username');
```
- column 메소드를 사용하여 첫 번째 인자에 모델의 필드 네임을 두 번째 인자에 출력할 컬럼 타이틀을 지정한다.


```php
// Display JSON inline fields
$grid->column('profile->mobile', 'phone number');
```
- 인라인 형태의 태그 내부에 profile 릴레이션을 통해 profile 모델으로 접근하여 mobile 필드의 값을 가져와 출력한다는 의미
- 출력되는 컬럼명은 'phone number'

```php
// Add multiple columns
$grid->columns('email', 'username' ...);
```
- columns 메소드는 column 메소드명의 복수형 여러 명칭의 컬럼명을 추가할 때 사용한다.

## 모델에 제약걸기
```php
$grid->model()->where('id', '>', 100);

$grid->model()->whereIn('id', [1, 2, 3]);

$grid->model()->whereBetween('votes', [1, 100]);

$grid->model()->whereColumn('updated_at', '>', 'created_at');

$grid->model()->orderBy('id', 'desc');

$grid->model()->take(100);
```
- model() 메소드를 통해서 모델에 제약을 걸고 난 다음 부터는 $grid를 통해서 얻는 결과물은 모두 제약이 걸린 상태의 데이터로 나온다.


## 페이지네이션
```php
// The default is 20 per page
$grid->paginate(15);
```
- 페이지당 표시되는 줄 수를 표시

## display
```php
$grid->text()->display(function($text) {
    return str_limit($text, 30, '...');
});

$grid->name()->display(function ($name) {
    return "<span class='label'>$name</span>";
});

$grid->email()->display(function ($email) {
    return "mailto:$email";
});

// column not in table
$grid->column('column_not_in_table')->display(function () {
    return 'blablabla....';
});
```

## display 메소드에서 모델 사용하기
```php
$grid->column('first_name');
$grid->column('last_name');

// column not in table
$grid->column('full_name')->display(function () {
    return $this->first_name.' '.$this->last_name;
});
```
- display 내부의 익명함수에 바인딩된 $this는 $grid에 바인딩된 모델을 의미한다.
- 여기서는 Movie 모델의 인스턴스가 익명 함수 내에서 $this에 바인딩 되었다.
- 문제는 이 바인딩 때문에 현재 객체에 접근하는 용도로 $this를 사용할 수 없는 점


## Reference
- https://laravel-admin.org/docs/en/model-grid
