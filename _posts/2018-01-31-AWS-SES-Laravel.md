---
layout: post
title:  "AWS SES를 Laravel 에서 사용하기"
categories: amazon ses php laravel
date:   2018-01-31 20:33
permalink: /archivers/amazon-ses-laravel-php
---

# SES (Simple Email Service)를 사용하는 일반 절차

1. SES Management Console 에서 Domain 또는 Email Address 확인(등록)
2. 1번에서 확인된 이후에는 등록된 Email Address로만 발송이 가능(디폴트는 Sandbox 환경).
3. Production 환경으로 사용하기 위해 'SES Sending Limit Increase' 를 요청.
  - SES를 사용하고자 하는 간단한 이유를 작성
  - 예상 발송량 기입
4. 3번의 요청이 완료되면 아래와 같은 메일을 받을 수 있다(1시간 이상 소요).

![Limit Increase]({{ base }}/assets/images/2018-01-31/quota-increase-mail.png)

# Laravel 에서 SES를 통해 메일을 발송하는 방법

1. SMTP 이용
2. aws-sdk-php 의 API 이용

크게 위의 두 가지 방법으로 사용 가능하다. 다만, 2번의 경우 KEY ID와 SECRET KEY 가 추가로 필요하다.

# SES SMTP 접속 정보 받기

Management Console > SMTP Settings

- Server Name, Port 정보를 확인한다.

![Limit Increase]({{ base }}/assets/images/2018-01-31/smtp-settings.png)

Management Console > SMTP Settings > "Create My SMTP Credentials" 클릭

- CSV 파일로 저장되는 정보중 Smtp UserName 과 Smtp Password 를 잘 확인해 둔다.

![Limit Increase]({{ base }}/assets/images/2018-01-31/smtp-credentials.png)

# Laravel SMTP 로 메일 보내기

.env 파일 수정

```text
MAIL_DRIVER=smtp
MAIL_HOST=<Server Name>
MAIL_PORT=587
MAIL_USERNAME=<Smtp Username>
MAIL_PASSWORD=<Smtp Password>
MAIL_ENCRYPTION=tls
```

app/config/mail.php

```php
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | Mail Driver
    |--------------------------------------------------------------------------
    |
    | Laravel supports both SMTP and PHP's "mail" function as drivers for the
    | sending of e-mail. You may specify which one you're using throughout
    | your application here. By default, Laravel is setup for SMTP mail.
    |
    | Supported: "smtp", "sendmail", "mailgun", "mandrill", "ses",
    |            "sparkpost", "log", "array"
    |
    */

    'driver' => env('MAIL_DRIVER', 'smtp'),
```
MAIL_DRIVER 를 smtp 로 변경

테스트 메일 발송

```bash
$ php artisan tinker
Psy Shell v0.8.17 (PHP 7.1.9 — cli) by Justin Hileman
>>> Mail::raw('test email', function($message) { $message->from('choo.changho@mydomain.com')->to('choo.changho@test.com')->subject('Testing email'); });
Swift_TransportException with message 'Expected response code 250 but got code "554", with message "554 Access denied: User `arn:aws:iam::112121:user/ses-smtp-user.20180131-11111' is not authorized to perform `ses:SendRawEmail' on resource `arn:aws:ses:us-west-2:049697382328:identity/choo.changho@mydomain.com'
"'
```
위와 같은 오류가 발생한다면 위에서 생성한 SMTP Credentials의 사용자를 IAM에서 찾아 필요한 권한을 추가해 주어야 한다.

![IAM Users]({{ base }}/assets/images/2018-01-31/iam-users.png)

![IAM Summary]({{ base }}/assets/images/2018-01-31/user-summary.png)

![IAM Policy]({{ base }}/assets/images/2018-01-31/policy.png)

뒤에 설명할 aws-sdk-php 의 API 를 이용한 메일 발송시 'SendEmail' 권한도 필요하므로 함께 추가한다.

다시 메일 발송을 시도한다.

```bash
$ php artisan tinker
Psy Shell v0.8.17 (PHP 7.1.9 — cli) by Justin Hileman
>>> Mail::raw('test email', function($message) { $message->from('choo.changho@mydomain.com')->to('choo.changho@test.com')->subject('Testing email'); });
=> null
>>>
```

# aws-sdk-php API 로 메일 보내기

엑세스 키 와 Secret 키 생성

![IAM Access Key]({{ base }}/assets/images/2018-01-31/access_key.png)

'액세스 키 만들기' 버튼 클릭후 생성되는 키 ID 와 Secret KEY 값을 잘 기록해둔다.
이 값들을 .env 파일에 기록한다.

.env

```text
SES_KEY=<KEY ID>
SES_SECRET=<SECRET KEY>
```

테스트를 위한 컨트롤러를 작성하고 브라우저로 결과를 확인하자.

```php
public function mail(Request $request)
{
  $client = SesClient::factory(array(
      'credentials' => array(
          'key'    => env('SES_KEY'),
          'secret' => env('SES_SECRET'),
      ),
      'region' => 'us-west-2',
      'version' => 'latest'
  ));
  try {
      $result = $client->sendEmail(array(
          'Source' => $this->encodeRecipients("보내는 사람 이름 <no-reply@mydomain.com>"),
          'Destination' => array(
              'ToAddresses' => array($this->encodeRecipients("홍길동님 <choo.changho@test.com>"))
          ),
          'Message' => array(
              'Subject' => array('Data' => '안녕하세요 SES!'),
              'Body' => array('Text' => array('Data' => '안녕하세요!!'))
          )
      ));
      $messageId = $result->get('MessageId');
      echo("Email sent! Message ID: $messageId"."\n");
  } catch (SesException $error) {
      echo("The email was not sent. Error message: ".$error->getAwsErrorMessage()."\n");
  }
}

// http://geekw3.com/2015/04/11/amazon-ses-integration-using-php-sdk/ 참고
function encodeRecipients($recipient){
	$recipientsCharset = 'utf-8';
	if (preg_match("/(.*)<(.*)>/", $recipient, $regs)) {
		$recipient = '=?' . $recipientsCharset . '?B?'.base64_encode($regs[1]).'?= <'.$regs[2].'>';
	}
	return $recipient;
}
```

![전송결과]({{ base }}/assets/images/2018-01-31/send_result.png)

환경 변수 값을 읽어오는데 문제가 발생한다면 아래와 같이 설정 캐시값을 초기화 시킨다.

```bash
$ php artisan config:cache
Configuration cache cleared!
Configuration cached successfully!
```

Credentials 정보를 파일로 저장하여 사용하는 방법은 아래와 같다.

~/.aws/credentials

```txt
[default]
aws_access_key_id=XXXXXXXXXXXX
aws_secret_access_key=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

Credentials 정보 설정

```php
$client = SesClient::factory(array(
    'profile' => 'default',
    'region' => 'us-west-2',
    'version' => 'latest'
));
```

# 참고
- [PHP용 AWS SDK를 사용하여 이메일 전송](https://docs.aws.amazon.com/ko_kr/ses/latest/DeveloperGuide/send-using-sdk-php.html)
- [AWS SDK for PHP](https://docs.aws.amazon.com/aws-sdk-php/v2/guide/credentials.html#credential-profiles)
- [Amazon SES integration using PHP SDK](http://geekw3.com/2015/04/11/amazon-ses-integration-using-php-sdk/)
