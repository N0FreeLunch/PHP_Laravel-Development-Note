## 최근 병렬 프로그래밍의 추세
- 메모리 락의 변수 저장부분에 락을 거는 방식으로 프로그래밍을 했던 과거의 뮤텍스와 세마포어 방식은 생상성과 관리적인 측면에서 좋지 않은 프로그래밍이었다.
- 메모리 락을 컨트롤 하는 방식의 병렬성을 구현했다면 이를 만드는 사람의 실력도 상당해야 하며, 해당 애플리케이션을 메인테인스 하는 사람도 쉽사리 손을 댈 수 없는 문제점이 있다.
- CPU의 단일 스레드 성능의 향상은 이뤄지고 있지만 한계에 다랐을 만큼 큰 발전이 없으며, 코어 수를 늘리는 방식으로 성능 향상을 주도하는 시대라서 많은 코어 수를 사용한 프로그래밍이 가능한 언어에 대한 관심이 커지는 추세이다.

## 병렬 프로그래밍이 가능한 인터프리트 언어
- 인터프리트 언어에서 병렬 프로그래밍이 가능한 것은 함수형 언어인 lisp, Standard ML 등이 있지만 병렬 프로그래밍을 가능하게 하는 웹 프레임워크의 생태계가 존재하지는 않아 보인다.
- 다른 여러 인터프리트 언어에서 함수형 언어가 아니더라도 병렬 프로그래밍을 지원할 수 있지만 웹 프로그래밍으로 쓸만한 언어는 웹과 관련된 커뮤니티 생태계가 갖춰져야 하는데 생태계가 활성화 되어 있지 않다. (조금이 아니라 거의 없는 정도의 수준. 한땀한땀 프로그래밍을 하고 싶다면.... 쓸 수도 있을 듯)
- 대부분의 인터프리트 언어는 싱글 스레드 내에서의 동시성을 구현할 수 있게 만들어져 있지만 인터프리트 엔진은 싱글 스레드에서 구현되어 있으므로 병렬 프로그래밍을 하기 위해서는 멀티 프로세스 방식을 사용한다. 예를 들어 PHP는 API 통신을 통한 방식으로 결과를 받는 식으로, 자바스크립트는 웹 워커를 사용한 방식을 쓰는 등의 프로그래밍 방식을 지원한다.
- 파이썬의 경우 스레드 방식으로 병렬성을 구현할 수는 있지만, 코루틴 방식이 아니기 때문에 병렬 프로그래밍의 구현이 어렵다.

## 왜 PHP인가
- php는 최근 swoole이란 기술이 등장하였으며, swoole은 코루틴을 사용한 동시성 뿐만 아닌 코루틴을 사용한 병렬 프로그래밍을 가능하게 한다.

## swoole의 장점
- 기존 웹 프레임워크와 라이브러리와의 호환성 문제가 있을 수 있다. 다행히도 php에서 가장 인기있는 웹 프레임워크인 라라벨의 경우는 swoole과의 호환성과 swoole을 이용한 발전 가능성을 모색하고 있다.
- 코루틴 방식을 사용한 병렬 프로그래밍은 함수형을 사용한 동시성 프로그래밍이 아니기 때문에 여러 기존 프로그래밍 방식이나 라이브러리와의 호환성을 가질 수 있다.

## swoole의 문제점
- 핵심 기여자에 중국인의 분포가 많아서 PHP 코어에 적극적으로 기여할 수 있는 의사 소통자들이 적다.
- PHP 코어와 함께 발전하지 않는다면 호환성 문제로 나중에 도태될 가능성이 있다. 다행히도 PHP 도큐먼트에 swoole 관련 라리브러리에 대한 설명이 추가되는 것으로 보아 소통이 조금씩 이뤄지고 있는 듯.
- swoole을 통한 병렬 프로그래밍을 하기 위해서는 CGI 방식을 버려야 하고 PHP-FPM을 통한 생태계를 버리면서 기술을 사용해야 한다.
- swoole의 발전은 영문권 국가에서 이끌고 있지 않기 때문에 사용에 대한 정보를 얻기가 어렵고, 최근 등장한 신생 기술이기 때문에 자료가 적고 학습 가능한 사람이 적다는 문제가 있다.