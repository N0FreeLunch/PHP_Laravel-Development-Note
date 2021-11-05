
## 컨텐츠 클래스
```
use Encore\Admin\Layout\Content
```

## 셈플 코드
```
public function index()
{
    return Admin::content(function (Content $content) {

        // optional
        $content->header('page header');

        // optional
        $content->description('page description');

        // add breadcrumb since v1.5.7
        $content->breadcrumb(
            ['text' => 'Dashboard', 'url' => '/admin'],
            ['text' => 'User management', 'url' => '/admin/users'],
            ['text' => 'Edit user']
        );

        // Fill the page body part, you can put any renderable objects here
        $content->body('hello world');

        // Add another contents into body
        $content->body('foo bar');

        // method `row` is alias for `body`
        $content->row('hello world');

        // Direct rendering view, Since v1.6.12
        $content->view('dashboard', ['data' => 'foo']);
    });
}
```


### 사용방법
#### Syntax
```
Admin::content(익명함수)
```

```
Admin::content(function (Content $content) {

})
```
- 익명함수에 전달되는 매개변수는 Content 객체(Encore\Admin\Layout\Content)이다.

### header 메소드
```
$content->header('page header');
```

### description 메소드
```
$content->description('page description');
```

### breadcrumb 메소드
```
$content->breadcrumb(
            ['text' => 'Dashboard', 'url' => '/admin'],
            ['text' => 'User management', 'url' => '/admin/users'],
            ['text' => 'Edit user']
        );
```

### body 메소드
```
$content->body('hello world');
$content->body('foo bar');
```
- 페이지의 body를 구성하는 내용을 넣는 메소드, 랜더링 될 수 있는 오브젝트를 넣으면 된다.
- 문자열, 숫자, \_\_toString을 사용한 메소드, implements Renderable, Htmlable interface, Laravel View objects 등을 사용할 수 있다.



