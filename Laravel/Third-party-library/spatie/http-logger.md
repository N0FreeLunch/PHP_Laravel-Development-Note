## HTTP 로거란?
- 리퀘스트와 리스폰스 로그를 남기는 것

## 관련 패키지
- https://github.com/spatie/laravel-http-logger

## spatie의 laravel-http-logger 의 한계
- 리퀘스트만 기록하고 리스폰스를 기록하지 않는다.
- 기본적으로는 미들웨어의 로그를 디비나 외부 저장소에 저장할 수 없다.

## 미션
- 스파티 패키지의 구현방식을 통해 리스폰스까지 기록하는 기능을 만들 때 best practice를 찾아 보자.
