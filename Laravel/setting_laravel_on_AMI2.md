# AWS AMI2에 라라벨 설치
### 패키지 최신 버전으로 패칭
- 처음 인스턴스를 만든 상태에서는 왠만하면 최신이라 실행할 필요 없지만 혹시 모르니 실행하자.
```
sudo yum update
```

### 메모리 스왑 공간 형성 
- nano 같은 인스턴스를 사용할 경우 메모리 부족으로 패키지 설치가 되지 않을 수 있다.
```
sudo dd if=/dev/zero of=/swapfile bs=1G count=4
```

### Install php and nginx
- 웹 서버를 세팅하기 위한 php 설치 및 nginx 설치
```
sudo amazon-linux-extras install php7.4 nginx1 
```

### Install redis
- 레디스가 필요하면 추가 설치
```
sudo amazon-linux-extras install redis4.0
```

### 라라벨 확장 php 패키지 설치
- 라라벨을 구동하는데 필요한 최소 확장 php 패키지이다.
```
sudo yum install  php-fpm php-cli \
php-bcmath php-ctype php-json php-mbstring openssl php-pdo php-tokenizer  php-xml
```

### php 확장 패키지 추가
- 이외에도 필요에 따라 php 확장 패키지를 설치할 수 있다.
```
php-mysqlnd php-opcache php-zip php-process php-sqlite php-common
```

### Install composer
```
sudo amazon-linux-extras install epel
sudo yum install composer
```
- epel은 yum 명령어로 설치 가능한 패키치를 확장 시켜주는 역할 이것을 설치 해야 yum을 통한 composer 설치가 가능하다.
- yum 명령어를 통해 설치할 수도 있지만, php를 설치했다면 composer 공식 패이지의 php 명령을 통한 설치도 가능하다.


### Install git
- 리포지토리 클론을 위한 git 설치
```
sudo yum install git
```

### Install NVM
- nodejs가 필요한 경우 노드 버전관리 프로그램 nvm을 설치하자.
https://github.com/nvm-sh/nvm
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```
- nvm 명령을 사용하기 위해서 환경변수를 설정하자.
```
vi ~/.bashrc
```
```
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")" [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```
를 추가 해 준다.

```
source ~/.bashrc
```
설정된 환경 변수를 현재 쉘에 반영한다.

### install nodeJS
- 최신버전의 노드 js 설치
```
nvm install node
nvm use default
```

- repository clone
```
cd /usr/share/nginx
```
```
sudo git clone https://저장소/개인 또는 조직명/리포지토리명.git
```
> tips| 서버는 여러명이 관리할 수 있기 때문에 개인이 관리하는 저장소인 다음과 같이 적지 않는 것이 좋아 보임
```
sudo git clone https://계정@저장소/개인 또는 조직명/리포지토리명.git
```


### nginx에서 접근 가능하도록 권한 설정
```
sudo chown nginx:nginx -R your_repository
```

# prepare to install php package
프로젝트의 의존성 패키지를 설치하기 위한 쓰기 권한 부여
```
sudo mkdir vendor
sudo chmod 777 vendor
sudo chmod 777 bootstrap/cache
sudo chmod -R 777 storage
```

### setting env file 
```
sudo vi .env
```

### php 의존성 패키지 설치
```
composer install
```

### 서빙 환경을 위한 폴더 권한
- 설치할 때만 필요한 쓰기 권한 해제
```
sudo chmod -R 755 vendor
```

### nginx 설정
```
cd /etc/nginx/conf.d
```
```
sudo vi domain.conf
```
nginx config 설정 (https://laravel.kr/docs/8.x/deployment)
```
server {
    listen 80;
    server_name example.com;
    root /srv/example.com/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        #fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_pass unix:/var/run/php-fpm/www.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```
> tips | php7.4 버전에서 php-fpm은 공식 문서에 나온
```
#fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
```
> 이 아니라
```
fastcgi_pass unix:/var/run/php-fpm/www.sock;
```
> 으로 사용해야 됨 php-fpm이 들어 있는 .sock 파일의 경로를 찾아서 적어 줘야 한다.

### run php fpm
```
sudo service php-fpm start
```

### run nginx
```
sudo service nginx start
```

### php-fpm과 nginx가 정상 독작하는지 확인
```
sudo service php-fpm status
sudo service nginx status
```
