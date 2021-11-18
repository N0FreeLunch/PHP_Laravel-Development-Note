## 
- 라라벨 어드민은 컨텐츠 영역과 컨텐츠가 아닌 영역으로 나눠져 있다. 메뉴 버튼을 클릭하면 전체 페이지가 로드 되는 방식이 아니라 컨텐츠 영역만 표시되는 방식으로 구성되어 있다.
- 각각의 컨텐츠 페이지별로 자바스크립트를 로드하기 위해서는 컨텐츠 영역에 자바스크립트를 넣어 줘야 하며, 컨텐츠 영역 이외의 부분에 자바스크립트를 로드하면 컨텐츠 영역의 페이지가 바뀔 때 자바스크립트가 실행 되지 않는 문제점이 있다.
- 라라벨 어드민에서 지원하는 자바스크립트 로드 방식은 컨텐츠 영역에 자바스크립트를 로드하는 방식이 있으며, 컨텐츠 영역 이외에 자바스크립트를 로드 하는 방식이 있다.
- 컨텐츠 영역 이외의 영역에 자바스크립트를 로드하는 것은 초기 페이지 로딩이나 리로드 할 때 로드 되는 것으로 라이브러리와 같은 것들을 위치시킨다.
- 컨텐츠 영역에는 컨텐츠 영역의 페이지 전환 시 로드 되는 자바스크립트로 컨텐츠 영역 페이지의 돔 조작과 같은 기능을 위치시킨다.

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
- 컨텐츠 영역에 JS 파일을 로드 할 수 없는 문제점이 있다.

### Admin::html로 자바스크립트 로드하기
```
Admin::html("<script src='/your/javascript/path/js.js'>...</script>");
```
- 1.5버전에서는 Admin에 html 메소드가 존재하지 않는다.
- https://github.com/z-song/laravel-admin/blob/bc3ca99a236a6424bb67a22612ff9aa8075d8b3e/src/Admin.php#L19 부분을 보면 html 메소드가 없다.

### Admin::script로 자바스크립트 로드하기
```php
Admin::script("var tempScript = document.createElement('script');tempScript.src='$path'document.body.appendChild(tempScript);");
```
