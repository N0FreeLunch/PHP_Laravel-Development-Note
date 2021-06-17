# AWS AMI2에서 무료 SSL 설치

- Let's encrypt에서 제공하는 SSL 인증서를 통해서 웹 사이트에 HTTPS를 적용할 수 있다.
- Let's encrypt에서 제공하는 사용법만으로는 SSL을 적용하기 무척 까다롭기 때문에 이 까다로운 과정을 자동화 해 주는 certbot을 이용해야 한다.
- AWS AMI는 레드헷리눅스(REPL) 기반이다. CentOS6은 REPL6, CentOS7은 REPL7, CentOS8은 REPL8을 기반으로 한다. AMI2는 REPL7을 기반으로 한다.
- certbot 패키지는 REPL7을 지원하지만 AMI2는 지원하지 않는다.
- certbot의 공식 페이지 (https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)에 소개된 REPL7을 기반으로 설치하는 방식과 다른 방식으로 설치해야 SSL을 적용할 수 있다.
 
 
### AMI에 SSL 적용을 위한 공식 문서
- AWS에서 apache 기반의 서버에 certbot을 설치할 수 있는 가이드를 마련한다. 이를 Nginx용으로 바꾸는 작업이 필요하다.
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html#letsencrypt

### Prepare
- certbot으로 nginx에 SSL 인증을 추가하기 위해서는 HTTP로 접속할 수 있는 세팅이 되어 있어야 한다.

### Start
만만한 $home 폴더에서 작업을 시작하자.
```
cd ~
```

## REPL 패키지 설치
- certbot은 REPL을 기반으로 하고 있기 때문에 AMI에 REPL 패키지를 설치할 수 있는 기반을 마련해 줘야 한다.
- 이 과정은 https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html#letsencrypt 에 나온 대로이다.

### REPL 패키지를 다운로드 한다.
```
sudo wget -r --no-parent -A 'epel-release-*.rpm' https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/
```
### 다운 받은 REPL 패키지를 설치한다.
```
sudo rpm -Uvh dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-*.rpm
```
### AMI의 yum 명령어에 REPL 패키지를 사용할 수 있게 등록한다.
```
sudo yum-config-manager --enable epel*
```
### 패키지 리스트 확인
```
sudo yum repolist all
```

## Nginx에 SSL 적용
- Nginx에 적용하기 위해 https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html#letsencrypt 에 나온 과정과는 다르게 진행한다.

### certbot 패키지 설치
```
sudo yum install certbot python-certbot-nginx
```

### certbot 실행
```
sudo certbot
```
또는
```
sudo certbot --nginx -d subdomain.rootdomain.com
```
명령어를 입력한다.
```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): your@mail.address

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: n
```
성공적으로 완료 되었다면 인증 갱신이 자동으로 이뤄지는지 체크하자.

## cron 설정
### 인증 갱신
```
sudo certbot renew --dry-run
```

### cron 등록
- 주기적으로 인증 갱신이 이뤄질 수 있도록 crontab을 설정하자.
```
39      1,13    *       *       *       root    certbot renew --no-self-upgrade
```

### 변경한 cron 적용
```
sudo systemctl restart crond
```
