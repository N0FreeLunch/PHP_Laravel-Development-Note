# 메일

## 메일 서버의 속도
- API 기반의 메일 서버가 SMTP 기반의 메일 서버 보다 빠르다.
- API 기반의 메일 서버는 Mailgun, Postmark가 존재한다.

## 메일 드라이브 연결 라이브러리
### 설치
```
composer require guzzlehttp/guzzle
```

## Mailgun
- 사용하게 되면 나중에 쓰자.
## PostMark
- 사용하게 되면 나중에 쓰자.

## SES 드라이버
- PHP에서 AWS SDK를 사용하여 SES 메일을 전송하기 위해서는 'AWS SDK for PHP'가 설치되어 있어야 한다.
- https://aws.amazon.com/ko/sdk-for-php/ 를 참고하면 정확한 설치와 사용법을 알 수 있다.
- composer로 설치하기 위해서는 composer.json파일의 require 부분을 수정하는 방법을 사용할 수 있다.
```
"aws/aws-sdk-php" : "~3.0"
```
- config/mail.php 경로에 AWS IAM을 통해 발급 받은 SES에 접근을 허용하는 access key ID 와 secret access key를 사용해야 한다.
- key 부분에는 access key ID를, secret 부분에는 secret access key를 입력해 준다.
```
'ses' => [
    'key' => 'your-ses-key',
    'secret' => 'your-ses-secret',
    'region' => 'ses-region',  // e.g. us-east-1
],
```
- IAM secret access key는 중요한 정보이다. 따라서 `.env` 파일 안에 key 값을 적어 줘야 한다.
- 변경 가능한 관련 정보는 한꺼번에 적는 것이 알아보기 쉽기 때문에 보통은 `.env`파일 안에 key, secret, region에 대한 값을 적어 준다.
- .env 파일의 경우에는
```
AWS_SDK_ACCESS_KEY_ID=
AWS_SDK_SECRET_ACCESS_KEY=
AWS_REGION='us-east-1'
```
- 이렇게 env 파일을 사용하여 적어 준다.
```
'ses' => [
    'key' => env('AWS_SDK_ACCESS_KEY_ID'),
    'secret' => env('AWS_SDK_SECRET_ACCESS_KEY'),
    'region' => env('AWS_REGION'),
],
```
- SES 옵션을 추가할 수도 있다. 이에 대해서는 AWS SDK for PHP의 [API 문서](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-email-2010-12-01.html#sendrawemail)를 보고 설정할 수 있다.
```
'ses' => [
    'key' => 'your-ses-key',
    'secret' => 'your-ses-secret',
    'region' => 'ses-region',
    'options' => [
        'ConfigurationSetName' => 'MyConfigurationSet',
        'Tags' => [
            [
                'Name' => 'foo',
                'Value' => 'bar',
            ],
        ],
    ],
],
```

## 라라벨에서 메일 보내는 코드 작성하기
- 메일을 보내는데 관여하는 Mailables이라는 클래스를 사용해서 손 쉽게 메일 보내기 기능을 작성할 수 있다.
- Mailables 클래스의 상속을 받아서 상속 받은 메서드를 통해 메일 작성 및 보내기에 관련한 조작을 쉽게 할 수 있다.

### 설치
- 메일 클래스의 상속을 받는 어떤 메일 도메인을 생성하자.
- App/Mail 디렉토리가 생성된다. 이것이 메일 서비스에 관한 도메인이다.
```
php artisan make:mail OrderShipped
```

## Mailables 작성하기
### build 메서드
- 메일 메서드들을 조합해서 하나의 전송 단위를 만드는 역할을 한다.

### from 메서드
- 송신자를 설정한다.
- build 메서드가 전송 단위를 만드는 역할을 하기 때문에 build 안에서 from 메서드를 사용한다.
```
public function build()
{
    return $this->from('example@example.com')
                ->view('emails.orders.shipped');
}
```
```
return $this->from('example@example.com')
                ->view('emails.orders.shipped');
```
- 위 부분은 메서드 체인의 형식이다.

### global from
- mailable에서 from을 지정하지 않았을 때, 기본적으로 보내는 주소를 설정하는 것
- `config/mail.php` 부분에서 어플리케이션 디폴트 발신자 주소를 지정
```
'from' => ['address' => 'example@example.com', 'name' => 'App Name'],
```

### global reply_to
#### reply_to란?
- 메일을 보내는 사람이 답장을 나에게 할지 다른 사람에게 할지를 적어 두는 것. 메일을 받은 사람은 회신을 할 때 reply_to에 적힌 대상에게 회신을 한다. 물론 받은 메일 쪽을 통해서 회신을 할 수도 있지만, 메일 서비스에서 제공하는 답장 버튼을 클릭하면 reply to에 기록된 사람에게 답신이 간다.
- config/mail.php 부분에서 지정
```
'reply_to' => ['address' => 'example@example.com', 'name' => 'App Name'],
```


## 메일 HTML 작성하기
- 메일의 템플릿을 작성할 수 있는 기능이다.
### .blade 파일을 사용할 때
- view의 블레이드 파일에 메일 양식의 HTML 파일을 작성한다.
- `resource/view` 부분에 작성을 한다.
```
/**
 * Build the message.
 *
 * @return $this
 */
public function build()
{
    return $this->view('emails.orders.shipped');
}
```
- Mailables 클래스의 view 메서드를 사용하여 메일 템플릿으로 지정할 블레이드 파일을 설정한다.

### HTML이나 TXT를 사용할 때
```
/**
 * Build the message.
 *
 * @return $this
 */
public function build()
{
    return $this->view('emails.orders.shipped')
                ->text('emails.orders.shipped_plain');
}
```
- 확장자가 .html이나 .txt에서 동작하는지 확인을 해 봐야 함
- https://stackoverflow.com/questions/42803404/sending-mail-to-multiple-recipients-using-laravel-5-4


## Mailable 클래스의 구조
```
<?php

namespace App\Mail;

use App\Models\Order;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class OrderShipped extends Mailable
{
    use Queueable, SerializesModels;

    /**
     * The order instance.
     *
     * @var Order
     */
    public $order;

    /**
     * Create a new message instance.
     *
     * @param  \App\Models\Order  $order
     * @return void
     */
    public function __construct(Order $order)
    {
        $this->order = $order;
    }

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped');
    }
}
```
- 메일에 관한 로직은 컨트롤러의 서비스 로직에서 불러 쓰는 하나의 단위이다. 디비의 데이터 조작에 관하는 부분을 의미론적 추상화를 한 것이 리포지토리이다. 
- 메일에 관한 부분은 서비스 로직에서 불러다 쓰이는 것이기 때문에 리포지토리와 비슷한 수준의 레이어의 역할을 가진다.
- 레포지토리의 경우 `App\Repository;`의 폴더 영역을 가지는 것과 같이 메일도 `App\Mail;`의 폴더 영역을 가진다.
- 레포지토리와 비슷한 레이어 레벨을 가지기 때문에 `use App\Models\Order;`으로 모델을 주입 당하는 것.

## 뷰에 데이터 전달
- 기본적으로 블레이드 파일에 데이터를 전달하기 위해서는 컨트롤러나 라우터 쪽에서 보내는 값을 담아 줘야 한다.
```
return view('greetings', ['name' => 'Victoria']);
``` 
```
return view('greeting')->with('name', 'Victoria');
```
### public 접근 제한자로 전달하기
- 컨트롤러나 라우터에서 뷰를 호출할 때 데이터를 담는 방식과는 달리 메일 기능을 사용할 때는 Mailable 클래스에서 public 속성을 이용한다.
- Mailable 클래스에서 접근 제한자가 public으로 되어 있는 멤버 변수의 값을 블레이드에서 접근할 수 있다.
```
    public $order;
    
    public function __construct(Order $order)
    {
        $this->order = $order;
    }
```
- 현재 `$this->order `는 public으로 접근할 수 있는 상태이기 때문에 외부에서 이 클래스나 객체에 접근하여 데이터를 가져올 수 있다.
- \_\_construct를 통해서 Order 객체를 주입한 상태에서 데이터를 가져온다객체를 주입한 대상의 주입된 대상에 접근할 수 있다는 것은 Mailable 클래스가 아닌 Mailable 객체로 접근한다는 의미이다. 
- view 메서드로 연결되는 블레이드 파일은 이 멤버를 접근할 수 있다. 블레이드 파일 안에서는 클래스를 접근하거나 객체를 선언해서 접근하는 표현은 없지만 기본적으로 외부에서 Mailable 객체의 멤버에 접근하는 방식으로 사용되고 있음을 알 수 있다.
```
<div>
    Price: {{ $order->price }}
</div>
```

### with 메서드를 사용하여 데이터를 메일 뷰에 전달하기
> 만약 여러분이 이메일 데이터의 유형이 템플릿에 전달되기 전에 수정을 가하고 싶다면, with 메소드를 사용하여 수동으로 데이터를 뷰에 전달할 수 있습니다.
- 위와 같은 도큐먼트의 설명은 데이터의 반영 시점이 다르다는 의미를 가지고 있는데, 직접 사용해서 분석이 필요한 것 같다.

```
<?php

namespace App\Mail;

use App\Models\Order;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class OrderShipped extends Mailable
{
    use Queueable, SerializesModels;

    /**
     * The order instance.
     *
     * @var Order
     */
    protected $order;

    /**
     * Create a new message instance.
     *
     * @param  \App\Models\Order $order
     * @return void
     */
    public function __construct(Order $order)
    {
        $this->order = $order;
    }

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped')
                    ->with([
                        'orderName' => $this->order->name,
                        'orderPrice' => $this->order->price,
                    ]);
    }
}
```
#### 접근 제한자 설정
- 외부에서 $order 멤버에 접근할 수 없도록 접근 제한자 protected를 설정하였다.
```
protected $order;
```

#### with 
- 접근할 수 없는 멤버를 블레이드 파일에 보내기 위해서는 with 메서드를 사용한다.
```
    public function build()
    {
        return $this->view('emails.orders.shipped')
                    ->with([
                        'orderName' => $this->order->name,
                        'orderPrice' => $this->order->price,
                    ]);
    }
```
- 위와 같이 key value를 전달하면 메일 블레이드에서는 key 값을 사용해여 value 값을 표시할 수 있다.
```
<div>
    Price: {{ $orderPrice }}
</div>
```

#### 라우터 컨트롤러의 뷰와 비교
```
return view('greeting')->with('name', 'Victoria');
```
- 라우터나 컨트롤러에서 블레이드로 데이터를 전달 할 때와 같은 동일한 표현성을 위해 with 메서드를 만들어 둔 것인지, public 접근 제한자와 다른 어떤 목적을 위해 만들어진 것인지 좀 더 분석이 필요함.
- https://laravel.com/api/5.8/Illuminate/View/View.html
```
with(string|array $key, mixed $value = null)
Add a piece of data to the view.
```
- Mailable에 존재하는 view 메서드가 아닌, 컨트롤러에서 사용하는 view 메서드의 경우에도 with의 인자를 array로 받을 수 있다는 것을 알 수 있다.
- https://laravel.com/api/5.8/Illuminate/Mail/Mailable.html
```
$this
with(string|array $key, mixed $value = null)
Set the view data for the message.
```
- Mailable에 존재하는 view 메서드도 array 뿐만 아닌 key, value를 담을 수 있는 형식을 제공한다.

## 첨부파일
- 메일을 보낼 때 파일을 보낼 수도 있다.
- 그런데 이 파일은 라라벨 폴더 안에 존재 해야 한다.
```
/**
 * Build the message.
 *
 * @return $this
 */
public function build()
{
    return $this->view('emails.orders.shipped')
                ->attach('/path/to/file');
}
```

### mime 타입 지정
```
/**
 * Build the message.
 *
 * @return $this
 */
public function build()
{
    return $this->view('emails.orders.shipped')
                ->attach('/path/to/file', [
                    'as' => 'name.pdf',
                    'mime' => 'application/pdf',
                ]);
}
```
- 메일로 첨부하는 파일의 데이터가 적절한 형식인지 확인을 하기 위한 작업
- 확장자 위조를 막기 위한 최소한의 보안 체크를 위한 것


## 메일 발송하기
- Mail 파사드(`Illuminate\Support\Facades\Mail`)를 사용해서 메일을 보낼 수 있다.
- mailable 클래스는 메일을 작성하기 위해 사용하며 Mail 파사드의 send 메서드를 사용해서 작성된 메일을 보낼 수 있다.

### 예제의 구성
```
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Mail\OrderShipped;
use App\Models\Order;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Mail;

class OrderController extends Controller
{
    /**
     * Ship the given order.
     *
     * @param  Request  $request
     * @param  int  $orderId
     * @return Response
     */
    public function ship(Request $request, $orderId)
    {
        $order = Order::findOrFail($orderId);

        // Ship order...

        Mail::to($request->user())->send(new OrderShipped($order));
    }
}
```
- 위 예제는 controller에서 mailable 클래스로 만들어진 OrderShipped을 사용한다.
- 이 때 OrderShipped은 의존성 주입을 사용하지 않는다. 모듈을 교체할 필요가 없는 대상이라면 결합도를 줄일 필요가 없기 때문에 의존성 주입을 굳이 사용할 필요는 없다.
- 메일에 관한 서비스 로직을 따로 분리하지 않고 컨트롤러에 메일을 담는 로직을 사용하였다. 서비스로직이 복잡해 질 경우 분리를 할 수도 있다.

### 메일 파사드와 mailable 사용하기
- `use Illuminate\Support\Facades\Mail;` 메일 파사드를 사용한다.
- `use App\Mail\OrderShipped;` OrderShipped은 mailable 클래스로 만들어진 대상이다.
```
Mail::to($request->user())->send(new OrderShipped($order));
```
- Mail 파사드의 send 메서드를 통해서 mailable에 지정한 설정을 담아 메일 전송을 시도한다.

### Mail 파사드의 역할
- 메일 파사드의 멤버에 대한 설명이 있는 API 문서 : https://laravel.com/api/5.8/Illuminate/Mail/PendingMail.html

#### to
> Set the recipients of the message.
```
Mail::to($request->user())->send(new OrderShipped($order));
```
- 수취인을 설정한다.
- 이메일 주소(`'user1@example.com'`), 하나의 사용자 인스턴스(`$request->user()` 또는 `Auth::user()`), 사용자들의 컬렉션(`collect(['user1@example.com', 'user2@example.com','user3@example.com'])`)을 인자로 받는다.
- 그런데 컬렉션 뿐만 아니라 배열도 받는다. `['user1@example.com', 'user2@example.com','user3@example.com']` 이런 배열도 받을 수 있다. (여러 배열에 대한 태스트를 한 설명 : https://stackoverflow.com/questions/26584904/laravel-mailsend-sending-to-multiple-to-or-bcc-addresses )
- API 문서를 보면 `mixed $user`라고 되어 있는데, 여러가지 형태르 받을 수 있다는 것을 의미하는 것 같은데 여기에 대한 소스코드를 찾으면 넣는 게 좋을 듯
- to에 여러 메일을 넣게 되면, 메일을 받는 사람은 같이 메일을 받은 사람을 알 수 있게 되기 때문에 이 경우에는 반복문을 사용해야 한다.
- cc, bcc 메서드들도 `mixed $user`로 되어 있기 때문에 to에 해당하는 여러가지 형식의 메일 주소를 받아 쓸 수 있다.


#### send
> Send a new mailable message instance.
```
mixed send(Mailable $mailable)
```
- $mailable 객체를 받는 방식으로 구성되어 있다.

### 체이닝
- 메일 파사드의 to, cc, bcc 메서드는 $this를 반환하기 때문에 메서드 체이닝 방식으로 사용할 수 있다.
```
use Illuminate\Support\Facades\Mail;

Mail::to($request->user())
    ->cc($moreUsers)
    ->bcc($evenMoreUsers)
    ->send(new OrderShipped($order));
```


### 반복해서 메일 보내기
- to를 사용할 경우, 메일 수신자는 누구에게 보낸 메일인지 수신자 리스트를 확인할 수 있게 된다. 이를 해결하기 위해서 여러 수신자들에게 메일을 따로 보내는 방식을 사용해야 한다. 반복문을 통해서 send를 한 번씩 보내는 방식을 사용한다.
```
foreach (['taylor@example.com', 'dries@example.com'] as $recipient) {
    Mail::to($recipient)->send(new OrderShipped($order));
}
```

### 라라벨 5.2버전 까지 메일 발송 방식
https://laravel.com/docs/5.2/mail#sending-mail
```
Mail::send('emails.reminder', ['user' => $user], function ($m) use ($user) {
            $m->from('hello@app.com', 'Your Application');
            $m->to($user->email, $user->name)->subject('Your Reminder!');
        });
```

## 메일러를 선택해서 보내기
- config\mail.php 경로의 파일을 보면 `'default' => env('MAIL_MAILER', 'smtp'),` 디폴트 메일러를 지정하는 부분이 있다. 코드에 메일러를 지정하지 않을 경우 디폴트 메일러를 사용하지만, 디폴트메일러가 아닌 다른 메일러를 사용하고 싶을 경우에는 다음과 같이 메일러를 지정할 수 있다.
```
Mail::mailer('postmark')
        ->to($request->user())
        ->send(new OrderShipped($order));
```



---

## Reference
https://laravel.kr/docs/8.x/mail
