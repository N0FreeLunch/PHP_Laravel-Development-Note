## flush

## file pointer

## php에서 파일을 읽는 방법
- 파일 전체를 한 번에 읽는다.
- 파일을 열어서 조금씩 읽어들인다.

## 파일 전체를 한 번에 읽을 때의 문제점
- 파일 전체를 한 번에 읽어서 메모리에 파일 내용을 적재해야 하는데 메모리의 크기가 제한되어 있다.

## stream 방식
- 대상 컨텐츠의 내용을 일부분 읽고, 다음 작업에서 다음 부분을 읽어 들일 수 있다.

## 파일을 열고 닫기
- `fopen`과 같은 함수로 파일을 열었다면 `fclose`와 같은 함수로 파일을 닫아 줘야 한다.


## Reference
- https://stackoverflow.com/questions/5249279/file-get-contents-php-fatal-error-allowed-memory-exhausted
