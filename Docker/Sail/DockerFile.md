```
FROM ubuntu:21.04
```
- 우분투 이미지로 만든다.

```
LABEL maintainer="Taylor Otwell"
```
- dockerfile의 LABEL 키워드는 도커 이미지에 벤더명, 저자명, 버젼 정보등의 메타데이타를 설정하는 부분

```
ARG WWWGROUP
```
- dockerfile이 실행될 때 WWWGROUP 이라는 메타데이터를 세팅한다.

```
WORKDIR /var/www/html
```
- 작업할 폴더 설정


### ENV
- ENV 키워드는 환경 변수를 지정하겠다는 의미
- dockerfile에서 공통적인 변수로 쓰기 위한 불변값을 저장하는 용도로 쓰인다.


```
ENV DEBIAN_FRONTEND noninteractive
```
- 대화형 쉘 스크립트를 실행할 때 커멘드라인 대화창이 나오지 않게 설정한다.
- `ENV DEBIAN_FRONTEND=noninteractive` 이렇게 쓸 수도 있다.

```
ENV TZ=UTC
```
- TZ을 UTC로 설정한다.

```
ENV NODE_VERSION=16
```
- NODEJS 버전을 16 버전으로 지정한다.


```
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```
- 환경 변수에 지정한 타임존을 $TZ를 사용하여 스크립트 안에 넣어 주었다.
- 리눅스 타임존을 $TZ에 해당하는 값으로 지정하였다.


### 우분투 패키지 
- 라라벨을 설치하는데 필요한 php 및 php 확장 그리고 우분투 환경을 사용하는데 필요한 패키지들을 설치한다.
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

```
RUN setcap "cap_net_bind_service=+ep" /usr/bin/php7.4
```

```
RUN groupadd --force -g $WWWGROUP sail
```

```
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail
```

```
COPY start-container /usr/local/bin/start-container
```

```
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
```

```
COPY php.ini /etc/php/7.4/cli/conf.d/99-sail.ini
```
- php 설정 파일

```
RUN chmod +x /usr/local/bin/start-container
```

```
EXPOSE 8000
```
- 어떤 포트 번호로 컨테이너를 공개할 것인지를 지정
- 외부에서 이 컨테이너에 접속할 때 EXPOSE로 지정된 포트로 접속하게 된다.

```
ENTRYPOINT ["start-container"]
```

---

## Reference : https://github.com/laravel/sail/blob/1.x/runtimes/7.4/Dockerfile

