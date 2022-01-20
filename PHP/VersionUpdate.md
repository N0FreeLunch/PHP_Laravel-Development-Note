## PHP의 버전 업데이트 방법

### 모든 기능의 동작을 체크하는 방식으로 업데이트 하기
- php 공식 문서의 depreciate 대상을 모두 확인하기
- php 다음 버전을 올릴 때, 먼저 depreciate 대상인 항목을 살핀다.
- 라라벨 프로젝트 내의 모든 코드, 프레임워크, 라이브러리를 포함한 depreciate 대상을 모두 확인해야 한다.
- 이 때, 사용할 수 있는 전략으로는 태스트 서버 실제 서버는 기존 버전으로 두고 로컬에만 PHP 버전업을 한 후, 오랜기간 로컬에서 다양한 태스트를 진행한 후 개발 서버에 반영하고 프로덕션 서버에 업로드 하는 방법으로 사용할 수 있다.

### TDD를 통한 버전 업데이트
- 태스트 커버리지를 100% 가까이 올리는 방식으로 php 버전업을 하고 태스트를 실행하고 문제가 없는지 확인하는 방식으로 버전업을 한다.
- 하지만 대부분의 회사와 프로젝트는 TDD를 하고 있지 않기 때문에 TDD를 통한 php 버전 업그레이드는 보편적인 방법은 아니다.

### 호환성 검사 툴을 통한 버전 업데이트
- 해서 모든 코드에 대한 호환성 검사를 진행한다.
- https://github.com/PHPCompatibility/PHPCompatibility