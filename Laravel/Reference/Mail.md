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


### Mailable 클래스의 구조
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

### 뷰에 데이터 전달
- 기본적으로 블레이드 파일에 데이터를 전달하기 위해서는 컨트롤러나 라우터 쪽에서 보내는 값을 담아 줘야 한다.
```
return view('greetings', ['name' => 'Victoria']);
``` 
```
return view('greeting')->with('name', 'Victoria');
```
- 컨트롤러나 라우터에서 뷰를 호출할 때 데이터를 담는 방식과는 달리 메일 기능을 사용할 때는 Mailable 클래스에서 public 속성을 이용한다.
- Mailable 클래스에서 접근 제한자가 public으로 되어 있는 멤버 변수의 값을 블레이드에서 접근할 수 있다.
```
    public function __construct(Order $order)
    {
        $this->order = $order;
    }
```
- 현재 `$this->order `는 public으로 접근할 수 있는 상태이기 때문에 view 메서드로 연결되는 블레이드 파일은 이 멤버를 접근할 수 있다.
```
<div>
    Price: {{ $order->price }}
</div>
```



---

## Reference
https://laravel.kr/docs/8.x/mail
