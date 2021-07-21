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

### Mailables 작성하기
#### build 메서드
- 메일 메서드들을 조합해서 하나의 전송 단위를 만드는 역할을 한다.

#### from 메서드
- 송신자를 설정한다.
- build 메서드가 전송 단위를 만드는 역할을 하기 때문에 build 안에서 from 메서드를 사용ㅎ한다.
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

#### global from
- mailable에서 from을 지정하지 않았을 때, 기본적으로 보내는 주소를 설정하는 것.
- `config/mail.php` 부분에서 어플리케이션 디폴트 발신자 주소를 지정
```
'from' => ['address' => 'example@example.com', 'name' => 'App Name'],
```
- global reply_to
```
'reply_to' => ['address' => 'example@example.com', 'name' => 'App Name'],
```


## 메일 HTML 작성하기
- view의 블레이드 파일에 메일 양식의 HTML 파일을 작성한다.
- `resource/view` 부분에 작성을 한다.
```
public function build()
{
    return $this->view('emails.orders.shipped');
}
```



---

## Reference
https://laravel.kr/docs/8.x/mail
