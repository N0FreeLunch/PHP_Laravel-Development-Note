
---

```
FROM ubuntu:21.04
```
- 우분투 이미지로 만든다.

---

```
LABEL maintainer="Taylor Otwell"
```
- dockerfile의 LABEL 키워드는 도커 이미지에 벤더명, 저자명, 버젼 정보등의 메타데이타를 설정하는 부분

---

```
ARG WWWGROUP
```
- dockerfile이 실행될 때 WWWGROUP 이라는 메타데이터를 세팅한다.

---

```
WORKDIR /var/www/html
```
- 작업할 폴더 설정

---

### ENV
- ENV 키워드는 환경 변수를 지정하겠다는 의미
- dockerfile에서 공통적인 변수로 쓰기 위한 불변값을 저장하는 용도로 쓰인다.

---

```
ENV DEBIAN_FRONTEND noninteractive
```
- 대화형 쉘 스크립트를 실행할 때 커멘드라인 대화창이 나오지 않게 설정한다.
- `ENV DEBIAN_FRONTEND=noninteractive` 이렇게 쓸 수도 있다.

---

```
ENV TZ=UTC
```
- TZ을 UTC로 설정한다.

---

```
ENV NODE_VERSION=16
```
- NODEJS 버전을 16 버전으로 지정한다.

---

```
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```
- 환경 변수에 지정한 타임존을 $TZ를 사용하여 스크립트 안에 넣어 주었다.
- 리눅스 타임존을 $TZ에 해당하는 값으로 지정하였다.

---

### 우분투 패키지 
- 라라벨을 설치하는데 필요한 php 및 php 확장 그리고 우분투 환경을 사용하는데 필요한 패키지들을 설치한다.

---
```
RUN apt-get update \
    && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python2 \
    && mkdir -p ~/.gnupg \
    && chmod 600 ~/.gnupg \
    && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
    && apt-key adv --homedir ~/.gnupg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E5267A6C \
    && apt-key adv --homedir ~/.gnupg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C300EE8C \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu hirsute main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
    && apt-get update \
    && apt-get install -y php7.4-cli php7.4-dev \
       php7.4-pgsql php7.4-sqlite3 php7.4-gd \
       php7.4-curl php7.4-memcached \
       php7.4-imap php7.4-mysql php7.4-mbstring \
       php7.4-xml php7.4-zip php7.4-bcmath php7.4-soap \
       php7.4-intl php7.4-readline php7.4-pcov \
       php7.4-msgpack php7.4-igbinary php7.4-ldap \
       php7.4-redis php7.4-xdebug \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && curl -sL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get install -y yarn \
    && apt-get install -y mysql-client \
    && apt-get install -y postgresql-client \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
```

---

```
RUN setcap "cap_net_bind_service=+ep" /usr/bin/php7.4
```
- setcap 명령어는 유저가 루트 권한을 쓰기 위해서 사용하는 명령어이다. 루트 권한을 세분화 하여 (커널, 파일 소유권, kill권한, 네트워크, 부팅 등) 각 필요한 부분만 권한을 할당할 수 있는 장점이 있다.
- php은 네트워크 통신에 관한 권한이 필요하고, php를 유저 권한으로 실행하기 위해서 php 실행파일에 네트워크 통신 권한을 부여하였다.

---

```
RUN groupadd --force -g $WWWGROUP sail
```
- 동일한 그룹 아이디가 있더라도 반드시 그룹을 생성하기 위해 --force 옵션이 추가되어 그룹 아이디가 있다면 다른 그룹 아이디로 그룹을 생성한다.
- \-g는 그룹 아이디를 지정하는 것이며 $WWWGROUP는 그룹 아이디를 받는다. 

---

```
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail
```
- s 옵션으로 유저가 사용할 쉘을 지정할 수 있다. 리눅스에 해당 유저로 로그인 하면 베쉬 쉘을 쓰겠다는 것.

---

```
COPY start-container /usr/local/bin/start-container
```
- start-container라는 파일을 도커 안에 세팅한다. 유저 권한을 가졌다면 start-container 명령으로 start-container 파일을 실행할 수 있다.

---

```
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
```
- nginx 설정 파일을 도커 안에 세팅한다.

---

```
COPY php.ini /etc/php/7.4/cli/conf.d/99-sail.ini
```
- php 설정 파일을 도커 안에 세팅한다.

---

```
RUN chmod +x /usr/local/bin/start-container
```
- start-container 파일에 실행권한을 추가한다.
- 실행권한은 sh와 같은 명령어 없어도 파일명으로 실행할 수 있도록 하는 것이다.

---

```
EXPOSE 8000
```
- 어떤 포트 번호로 컨테이너를 공개할 것인지를 지정
- 외부에서 이 컨테이너에 접속할 때 EXPOSE로 지정된 포트로 접속하게 된다.

---

```
ENTRYPOINT ["start-container"]
```
- WORKDIR에서 설정한 경로의 start-container 명령을 실행한다. 
- `/usr/local/bin/start-container`에 파일이 위치하여 있기 때문에 유저 권한이라면 어느 경로에서든 start-container 명령을 실행 할 수 있다.


---


## Reference
- https://github.com/laravel/sail/blob/1.x/runtimes/7.4/Dockerfile

