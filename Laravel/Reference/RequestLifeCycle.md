# 라이프 사이클

### 왜 라이프 사이클이라고 부르는가?


### 라라벨의 모든 요청의 시작점
- '라라벨 프로젝트 폴더'/public/index.php가 시작점이다.
- HTTP 리퀘스트는 기본적으로 요청한 파일을 리스폰스로 보내주는 역할을 하며 특별한 설정이 되어 있지 않다면, 요청한 URL의 PATH 부분에 해당하는 파일을 리스폰스로 제공한다.
- 실서버에는 nginx, apache 등을 통해서 public 폴더만 공개하도록 설정한다.
- 라라벨의 public 폴더만 공개가 되므로, 
- 네트워크 리퀘스트가 nginx, apache등을 앞선 설정을 통해 라라벨 어플리케이션으로 요청 데이터가 들어 온 경우 연결되는 파일을 지정한다.


---

Reference : https://laravel.kr/docs/8.x/lifecycle


descripted by N0FreeLunch

