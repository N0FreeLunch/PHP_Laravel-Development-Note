# Validation
- 애플리케이션으로 유입되는 데이터의 유효성 검사를 하기 위한 도구

- ValidatesRequests 트레이트-trait

## 유효성 검사를 위한 라우터와 컨트롤러 예시
### 라우터
```
use App\Http\Controllers\PostController;
Route::get('post/create', [PostController::class, 'create']);
Route::post('post', [PostController::class, 'store']);
```

### 컨트롤러
```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class PostController extends Controller
{
    /**
     * Show the form to create a new blog post.
     *
     * @return Response
     */
    public function create()
    {
        return view('post.create');
    }

    /**
     * Store a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        // Validate and store the blog post...
    }
}
```
- 컨트롤러는 하나의 클래스이다. 보통 클래스는 단일 책임의 원칙을 가져야 하므로 CURD 중 하나의 기능만을 담당하는 것이 맞지만, 컨트롤러는 데이터를 전달하는 통로의 역할을 하기 때문에 CURD 각각의 로직에 대한 서비스 로직에 대한 처리는 서비스 레이어에 맡기고 서비스레이어가 단일책임의 원칙을 사용하도록 한다.





---

## Reference
https://laravel.kr/docs/8.x/validation
