## 알아 둬야 할 것 - 컨텐츠 영역
- 라라벨 어드민은 컨텐츠 영역과 컨텐츠가 아닌 영역으로 나눠져 있다. 메뉴 버튼을 클릭하면 전체 페이지가 로드 되는 방식이 아니라 컨텐츠 영역만 표시되는 방식으로 구성되어 있다.
- 각각의 컨텐츠 페이지별로 자바스크립트를 로드하기 위해서는 컨텐츠 영역에 자바스크립트를 넣어 줘야 하며, 컨텐츠 영역 이외의 부분에 자바스크립트를 로드하면 컨텐츠 영역의 페이지가 바뀔 때 자바스크립트가 실행 되지 않는 문제점이 있다.
- 라라벨 어드민에서 지원하는 자바스크립트 로드 방식은 컨텐츠 영역에 자바스크립트를 로드하는 방식이 있으며, 컨텐츠 영역 이외에 자바스크립트를 로드 하는 방식이 있다.
- 컨텐츠 영역 이외의 영역에 자바스크립트를 로드하는 것은 초기 페이지 로딩이나 리로드 할 때 로드 되는 것으로 라이브러리와 같은 것들을 위치시킨다.
- 컨텐츠 영역에는 컨텐츠 영역의 페이지 전환 시 로드 되는 자바스크립트로 컨텐츠 영역 페이지의 돔 조작과 같은 기능을 위치시킨다.

## Admin 불러오기
```php
use Encore\Admin\Facades\Admin;
```

## 컨텐츠 이외의 영역
### CSS 로드
```
Admin::css('/your/css/path/style.css');
```

### JS 로드
```
Admin::js('/your/javascript/path/js.js');
```

### 파비콘 로드
```
Admin::favicon('/your/favicon/path');
```

## 컨텐츠 영역
### JS 로드
```
Admin::script('console.log("hello world");');
```

### CSS 로드
```
Admin::style('.form-control {margin-top: 10px;}');
```

### HTML 로드
```
Admin::html('<template>...</template>');
```

## 컨텐츠 영역 로드의 한계
- 컨텐츠 영역에 스크립트 자체를 추가할 수는 있으나 JS 파일을 로드 할 수 없는 문제점이 있다.

### Admin::html로 자바스크립트 로드하기
```
Admin::html("<script src='/your/javascript/path/js.js'>...</script>");
```
- 1.5버전에서는 Admin에 html 메소드가 존재하지 않는다.
- https://github.com/z-song/laravel-admin/blob/v1.8.16/src/Admin.php 부분을 보면 html 메소드가 없다.

### Admin::script로 자바스크립트 로드하기
```php
Admin::script("var tempScript = document.createElement('script');tempScript.src='$path';document.getElementById('app').appendChild(tempScript);");
```
- 자바스크립트를 로드할 때 중요한 것은 document.getElementById('app')에 스크립트 태그를 추가해야 한다는 것이다. 왜냐하면 라라벨 어드민에서 페이지 전환은 컨텐츠 영역에서 일어나는 것이기 때문 바뀌는 컨텐츠 영역에 항상 위치하는 태그인 id가 app인 태그에 넣어 주었다.

#### 자바스크립트 파일 로드 함수 만들기
- 함수를 하나 만들어서 자바스크립트를 path로 로드 할 수 있게 만드는 편이 좋다. app\Admin\bootstrap.php 경로에
```php
function loadScript(string $path): void {
  Admin::script("(function () {var tempScript = document.createElement('script');tempScript.src='$path';document.getElementById('app').appendChild(tempScript);})()");
}
```
- 사용법
```php
loadScript(mix('/your/resourse/folder/js/file'));
```
- 이렇게 mix를 써서 사용하기 위해서는 webpack.mix.js 파일에 .version() 을 추가해 줄 필요가 있다.
```
mix.js('resources/js/app.js', 'public/js')
    .version();
```

#### 별도의 클래스를 만들어 로드하기
- 전역으로 로드 되는 것이 싫은 경우에는 다음과 같이 사용하면 된다.
```php
namespace App\Admin\Tools;
use Encore\Admin\Facades\Admin;

class LoadFrontendFile
{
    public function loadScript(string $path) {
        Admin::script("(function () {var tempScript = document.createElement('script');tempScript.src='$path';document.getElementById('app').appendChild(tempScript);})()");
    }
}
```
- 사용법 (form, grid, show 등의 laravel-admin의 코드 구현부에) 다음을 통해 
```php
namespace App\Admin\Tools\LoadFrontendFile;

LoadFrontendFile::loadScript(mix('/your/resourse/folder/js/file'));
```



