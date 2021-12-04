# Form validation 
- 라라벨 어드민은 라라벨의 벨리데이션을 사용한다.

## 라라벨 벨리데이션에 대한 간단한 이해
- 벨리데이션은 기본적으로 HTTP 프로토콜로 전달된 파라메터에 대한 벨리데이션을 건다. 그래서 Illuminate\Http\Request 객체나 커스텀으로 `php artisan make:request StoreBlogPost`등의 명령어로 만든 리퀘스트 객체의 벨리데이션을 사용할 때는 HTTP 프로토콜로 전달된 파라메터에 대한 벨리데이션만 걸 수 있다.
- Illuminate\Support\Facades\Validator 벨리데이션을 사용하면 HTTP 프로토콜로 전달된 파라메터 뿐만 아니라 임의로 만든 연관배열을 사용해서 벨리데이션을 걸 수 있다.
- 유효성 검사를 통과하지 못한 경우, 벨리에이션 메시지를 리스폰스에 보내주는데 라라벨은 기본적으로 벨리데이터 리스폰스는 파라메터 단위로 보내 준다. 리퀘스트 파라메터 각각에 대한 유효성 검사를 하고 각각에 대한 유효성 검사 실패 메시지를 리스폰스에 달아 보낸다.



## 간단한 유효성 검사
```php
$form->text('title')->rules('required|min:3');
```

- 익명함수를 사용하여 벨리데이션 룰을 설정
```php
$form->text('title')->rules(function ($form) {

    // If it is not an edit state, add field unique verification
    if (!$id = $form->model()->id) {
        return 'unique:users,email_address';
    }

});
```

## 배열을 사용한 유효성 검사
```php
$form->text('title')->rules(['required', 'min:3']);
```


### 배열과 클로저를 사용한 유효성 검사
```php
$form->text('title')->rules([
    'required',
    'max:255',
    function ($attribute, $value, $fail) {
        if ($value === 'foo') {
            $fail($attribute.' is invalid.');
        }
    },
]);
```
- 배열로 유효성 검사 룰을 만들어 넣었을 때, 배열의 원소로 익명함수를 넣었다. 이 익명함수는 어떤 값을 반환하는 것이 아닌 전달 받은 $fail인자를 사용해서 $fail 함수를 호출하는 것으로 유효성검사가 해당 폼 테그에 넣어 준다.
- $fail은 해당 폼에 대한 유효성 검사 실패를 알리는 역할을 하는 함수인 것을 알 수 있다.
- 배열 내부의 클로저로 유효성 검사를 사용하지 않게 되면, 벨리데이션이 걸려도 HTTPS 파라메터에 대한 유효성 검사인지 알 수 없기 때문에 클로저를 사용하여 커스텀 유효성 검사를 넣어 사용해야 한다.

### 리퀘스트 파라메터가 배열로 들어 올 때 배열 원소 각각에 대한 벨리데이터 설정
```php
use Illuminate\Support\Facades\Validator;
```
```php
$form->tags('email')->rules([
    'required',
    'max:255',
    function ($attribute, $params, $fail) {
        foreach ($params as $value) {
            $validator = Validator::make(
                ['value' => $value],
                ['email' => 'required|email']
            );
            if($validator->fails()) {
                $fail(__(validation.email));
            }
        }
    },
]);
```
- tags는 하나의 폼에서 여러개의 입력 값이 전달되며 이는 배열로 전달된다.
- 기존 email 벨리데이션에서의 에러메시지를 그대로 출력하기 위해서 `__(validation.email)`을 

## 유효성 검사에 메시지 넣기
```php
$form->text('code')->rules('required|regex:/^\d+$/|min:10', [
    'regex' => 'code must be numbers',
    'min'   => 'code can not be less than 10 characters',
]);
```

## 라라벨 어드민이 제공하는 생성 페이지에서만 동작
```php
$form->text('title')->creationRules('required|min:3');
```

## 라라벨 어드민이 제공하는 수정 페이지에서만 동작
```php
$form->text('title')->updateRules('required|min:3');
```

## 테이블의 유니크 속성을 확인하여 벨리데이션 걸기
```php
$form->text('username')
     ->creationRules(['required', "unique:user_table"])
     ->updateRules(['required', "unique:user_table,username,{{id}}"]);
```
