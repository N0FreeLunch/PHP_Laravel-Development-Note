# Validation
- 애플리케이션으로 유입되는 데이터의 유효성 검사를 하기 위한 도구
- 라라벨에서 벨리데이션은 크게 3가지 방식으로 사용할 수 있다.
- 첫 번째 : Request 객체의 validate 메서드를 사용하는 방법
- 두 번째 : Form request를 활용한 유효성 검사에 대한 로직을 적는 파일을 만들어 주는 방법
- 세 번째 : validate 파사드를 통한 방법이다.
- ValidatesRequests 트레이트 - trait ?

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
- 컨트롤러는 하나의 클래스이다. 보통 클래스는 단일 책임의 원칙을 가져야 하므로 CURD 중 하나의 기능만을 담당하는 것이 맞지만, 컨트롤러는 데이터를 전달하는 통로의 역할을 하기 때문에 CURD 각각의 로직에 대한 서비스 로직에 대한 처리는 서비스 레이어에 맡기고 서비스레이어의 클래스가 단일책임의 원칙을 사용하도록 한다.


## Illuminate\Http\Request의 validate 사용하기
- 유효성 검사 로직은 몇 가지로 제공된다. 그 중에서 리퀘스트의 정보를 담고 있는 Request 객체가 가지고 있는 유효성 검사 로직에 대한 설명이다.
- 리퀘스트 객체가 가진 유효성 검사는 request 데이터로 받은 파라미터에 한하여 유효성 검사를 실시한다.
- 앞선 PostController의 메서드 부분의 store 메서드의 부분이다.
```
/**
 * Store a new blog post.
 *
 * @param  Request  $request
 * @return Response
 */
public function store(Request $request)
{
    $validatedData = $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ]);

    // The blog post is valid...
}
```
- 유효성 검사를 통과하지 못하면 exception이 발생하며, 어떤 벨리데이션 설정을 위반했는지 예외를 통해 전달 한다.
- 기본적으로 벨리데이션 검사 규칙 ` 'required|unique:posts|max:255`에 따라서 무엇을 위반했는지에 대한 메시지를 예외에 전달하고 있으며, 각 메시지는 커스텀 값을 정할 수도 있다.

### 자동 리스폰스 생성
- 별도의 예외 처리를 하지 않아도 벨리데이션을 통과할 때, 유효성 검사에 문제가 발생하면, 자동으로 벨리데이션을 통과하지 못했다는 리스폰스를 생성하는데 HTTP 요청의 경우 웹 페이지의 리다이렉트가 만들어지고,  AJAX 요청에는 JSON 리스폰스가 생성이 된다.
- 벨리데이션을 통과하지 못했을 경우 생성되는 스테이터스 코드는 422이다.

### 배열 유효성 검사
- 구분자 | 를 사용하지 않고 배열로 검사 규칙 쓰기
```
$validatedData = $request->validate([
    'title' => ['required', 'unique:posts', 'max:255'],
    'body' => ['required'],
]);
```

## Form Request 유효성 검사

## Validator 파사드
- 리퀘스트의 벨리데이션이 리퀘스트로 전달된 post의 body 파라메터에 대한 유효성 검사를 하는 반면, Validator를 통한 유효성 검사는 전달된 대상에 대한 유효성 검사를 시도한다. 


```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;

class PostController extends Controller
{
    /**
     * Store a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        if ($validator->fails()) {
            return redirect('post/create')
                        ->withErrors($validator)
                        ->withInput();
        }

        // Store the blog post...
    }
}
```

### 수동으로 유효성 검사 성공 실패 여부 확인하기
```
if ($validator->fails()) {
    return redirect('post/create')
                ->withErrors($validator)
                ->withInput();
}
```
- 벨리데이션 파사드를 사용할 때, 유효성 검사를 실패하면, fails() 메서드를 사용해서 벨리데이션 통과 혹은 실패를 확인할 수 있다.

### 자동 리다이렉트
```
Validator::make($request->all(), [
    'title' => 'required|unique:posts|max:255',
    'body' => 'required',
])->validate();
```
- 유효성 검사가 실패하면 자동으로 리다이렉트를 한다.
- 자동 리다이렉트는 스테이터스 422, 벨리데이션 값을 포함한 view 리다이렉트 또는 ajax 요청인 경우 json이 반환 됨

### 오류 메시지를 별도로 저장하기
```
Validator::make($request->all(), [
    'title' => 'required|unique:posts|max:255',
    'body' => 'required',
])->validateWithBag('post');
```


## 사용자 지정 에러메세지
```
$messages = [
    'required' => 'The :attribute field is required.',
];

$validator = Validator::make($input, $rules, $messages);
```


---

## Reference
https://laravel.kr/docs/8.x/validation
