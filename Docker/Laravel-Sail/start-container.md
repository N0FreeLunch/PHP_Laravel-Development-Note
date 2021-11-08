```
#!/usr/bin/env bash
```
- 베쉬 쉘을 사용하는 스크립트임을 알리는 표시

---

```
if [ ! -z "$WWWUSER" ]; then
    usermod -u $WWWUSER sail
fi
```
-  \-z는 문자열 길이가 0인지 체크하는 것
- \! \-z는 지정한 문자열의 길이가 0이 아닐 때, 곧 $WWWUSER에 값이 존재할 때라는 조건
- usermod는 임시 유저를 발행하는 것, 도커 빌드할 때만 사용할 유저를 만드는 것

---

```
if [ ! -d /.composer ]; then
    mkdir /.composer
fi
```
- \/\.composer 경로에 \.composer 폴더를 만든다.

---

```
chmod -R ugo+rw /.composer
```
-

```
if [ $# -gt 0 ];then
    exec gosu $WWWUSER "$@"
else
    /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf
fi
```
- 
