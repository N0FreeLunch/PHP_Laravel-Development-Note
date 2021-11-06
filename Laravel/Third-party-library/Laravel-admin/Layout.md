# 라라벨 어드민에서 레이아웃이란?
- 라라벨 어드민의 화면은 상단바, 왼쪽 메뉴바 오른쪽 하단의 컨텐츠 영역으로 구성되어 있다.
- 컨텐츠 영역은 상단 왼쪽에 헤더 영역을 가지고 있고 오른쪽에 네비게이션바가 위치해 있다.
- 컨텐츠 영역의 상단의 헤더 영역의 바로 옆에 description 영역을 가지고 있다.

## 컨텐츠 클래스
```
use Encore\Admin\Layout\Content
```

## 셈플 코드
- app\Admin\Controllers\TestLayoutController.php 파일을 만들어 보았다.
```
namespace App\Admin\Controllers;

use App\Http\Controllers\Controller;
use Encore\Admin\Layout\Content;
use Encore\Admin\Facades\Admin;

class TestLayoutController extends Controller
{
    public function index(Content $content)
    {
      // return "test";
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
        // $content->view('dashboard', ['data' => 'foo']);
        $content->view('dashboard', ['data' => 'foo']);
        
      });
    }
}
```
- 셈플 코드가 동작하기 위해서는 `resources\views`에 블레이드 간단한 블레이드 파일을 만들고 `$content->view('dashboard', ['data' => 'foo']);`에 블레이드 파일 명을 지정해 줘야 한다.
- 또한 라우터로 컨트롤러를 연결 해 주고 페이지에 들어가 보면 레이아웃이 어떤지 알 수 있다.


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
- 페이지의 body를 구성하는 내용을 넣는 메소드, 랜더링 될 수 있는 대상을 넣으면 된다.
- 랜더링 될 수 있는 대상은 문자열, 숫자, \_\_toString을 사용한 메소드, implements Renderable, Htmlable interface, Laravel View objects 등이 있다.

### 줄 추가
- 컨텐츠 영역의 상단바 아래의 공간에 컨텐츠를 표시할 때 그리드 형식의 테이블을 만든다.
```
$content->row('hello world');
```

