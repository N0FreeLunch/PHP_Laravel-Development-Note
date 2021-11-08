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
- 파일 권한을 보여주는 `-rwxr-x----`에서 숫자를 자릿수라고 할 때
- 1 : 디렉토리 타입
- 2-4 : 소유주(user)
- 5-7 : 유저 그룹 (group)
- 8-10 : 타 유저 권한 (other)
- ugo라는 것은 user, group, other를 대상으로
- +라는 것은 권한을 추가한다.
- rw는 read와 write
- user, group, other를 대상으로 읽기, 쓰기 권한을 부여하겠다는 의미

---

```
if [ $# -gt 0 ];then
    exec gosu $WWWUSER "$@"
else
    /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf
fi
```
- 


---

## Reference
- https://github.com/laravel/sail/blob/1.x/runtimes/7.4/start-container
