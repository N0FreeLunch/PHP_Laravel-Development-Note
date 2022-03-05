## 코드 해석의 시작, 중지, 파일의 끝
### 코드 해석의 시작과 중지
```
<?php echo 'if you want to serve PHP code in XHTML or XML documents,
                use these tags'; ?>
```
- `<?php` : 열기 태그 : 코드의 해석을 **시작**한다.
- `?>` : 닫기 태그 : 코드의 해석을 **중지**한다.
- `?>` 이후 `<?php` : 중지 된 코드 해석을 다시 **재개**한다.

### 코드 해석의 끝
- php 코드를 실행하는 파일 끝에 도착하면 코드 해석을 **중지**한다.

## `<?=` `?>` 구문
- `<?=` `?>` 구문은 `<?php echo` `?>` 구문과 동일하다.
- HTML과 혼합하여 쓰는 방식을 사용할 때 편하게 구현하기 위해 만든 것.
- php.ini 파일을 이 구문의 활성/비활성을 선택할 수 있다.
- php.ini 파일 안에 `short_open_tag ` 옵션에 true, false를 지정하여 활성화/비활성화를 한다.
- `php` 명령을 사용할 때 옵션으로 `--disable-short-tags`을 추가하면 실행 시 비활성화 된다.
- 짧은 태그는 비활성화 가능하므로 호환성을 위해 가능하면 사용하지 않는 편이 좋다.

## php 태그 이외의 값은 무시
- php 태그 안에 들어 있지 않은 값은 php 엔진에 의해 해석되지 않는다.
```
You can use the short echo tag to <?= 'print this string' ?>.
It's equivalent to <?php echo 'print this string' ?>.
```
- `You can use the short echo tag to`는 해석되지 않는다.
- `<?= 'print this string' ?>`는 해석이 되며 `<?=` `?>` 구문은 `<?php echo` `?>` 구문과 동일하다.
- `It's equivalent to `는 해석 되지 않는다.
- `<?php echo 'print this string' ?>`는 해석이 되며 echo 키워드에 의해서 문자열이 변환 된다.

## echo 문법 사용시 주의사항
```
<?php echo 'if you want to serve PHP code in XHTML or XML documents,
                use these tags'; ?>
```
- 문자열에 개행이 포함되어 있으면 `documents,` 뒤 `들여쓰기 공백 use`앞에 개행이 포함된다.
- `들여쓰기 공백` 문자열 개행 후 들여쓰기 한 공백도 모두 문자열의 출력 결과에 포함된다.

## 닫는 태그 생략
- 닫는 태그 뒤에 실수로 공백이나 새 줄이 추가되는 것을 방지할 수 있다.

## Reference
- https://www.php.net/manual/en/language.basic-syntax.phptags.php
