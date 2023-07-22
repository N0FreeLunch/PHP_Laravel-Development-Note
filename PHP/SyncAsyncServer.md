## PHP
- PHP는 기본적으로 동기식 방식으로 서버 프로그래밍을 한다.

## 속도가 빠른 언어와 속도가 느린언어의 차이는 크지 않다.
- 일반적인 비즈니스로직을 만들 때 한 번의 리퀘스트 리퀘스트 생명 주기에 데이터베이스에 여러번 쿼리를 날리게 된다.
- 최적화를 통해서 쿼리를 날리는 횟수를 많이 줄일 수도 있지만, 관리하기 쉬운 코드를 만들기 위해서는 기능별로 추상화를 하게 되고, 이런 추상화된 기능을 사용하다보면 최적화로는 쿼리 몇 번 보내지 않을 일을 여러 번 쿼리를 나눠서 보내게 된다.
- 서버 프로그래밍에서 하나의 리퀘스트를 처리 할 때 여러번의 쿼리 요청에 의해 성능의 병목이 발생하게 되고 결국 리스폰스를 응답 받는데까지 걸리는 시간이 늘어나게 된다.
- 데이터베이스 쿼리를 처리하는 시간에 비해 프로그래밍 언어에 의한 로직 처리는 상대적으로 굉장히 빠른 시간 내의 처리속도를 가진다. 따라서 빠른 프로그래밍 언어를 쓰든 느린 프로그래밍 언어를 쓰든 체감할 정도의 차이를 느끼기 힘들 수 있다.
- 'IO처리를 대기하는 시간 + 프로그래밍 언어의 처리 시간'이라고 했을 때, A언어는 '프로그래밍 언어의 처리 시간'이 5, B언어는 '프로그래밍 언어의 처리 시간'이 15, C언어는 '프로그래밍 언어의 처리 시간'이 50, D언어는 '프로그래밍 언어의 처리 시간'이 100이라고 해 보자. 비즈니스 로직이 복잡해지고 기능에 따른 추상화가 늘어날수록 'IO처리를 대기하는 시간'이 늘어나게 된다. 상대적인 비율로 보았을 때 이 속도를 50이라고 해 보자. A언어로 처리되는 시간은 55, B언어로 처리되는 시간은 65, C언어로 처리되는 시간은 100, D언어로 처리되는 시간은 150이 된다. 언어의 처리 속도는 10배씩 또는 그 이상의 차이가 나도 한번의 리퀘스트를 처리하는데까지 걸리는 시간의 차이는 2~3배를 넘지 않는다.

## 속도가 빠른 언어의 이점을 살리려면
- 빠른 속도의 언어가 가진 이점을 최대한 살리기 위해서는 'IO처리를 대기하는 시간'을 최대한 줄여야 한다. 쿼리 최적화를 통해서 'IO처리를 대기하는 시간'을 최대한 줄여서 언어가 가진 속도의 장점을 최대한 살려야 한다. 하지만, 쿼리를 보내는 횟수를 적게 하려면 할 수록 하나의 쿼리로 받아오는 데이터를 통해서 여러가지 비즈니스 로직을 처리해야 한다.
- 쿼리를 통해서 받는 데이터는 리스트형식의 데이터를 받으며 리스트 형식의 데이터를 처리하기위해서는 추상화된 기능에 리스트를 보내고 해당 리스트를 적절하게 가공한 결과를 받는 방식으로 프로그래밍 되어야한다. 이런 데이터 처리에 적합한 방식은 객체지향적인 프로그래밍보다는 함수형을 사용한 프로그래밍에 적합하다.

## 속도가 빠른 언어의 장점
- 속도가 빠른 언어의 장점은 하나의 리퀘스트를 처리하는 시간은 느린 언어와 크게 차이가 나지 않을 수 있지만, CPU의 사용량이 굉장히 적다는 장점이 있다. CPU 자원이 100이라고 할 때 동일한 시간이 주어졌다면, A 언어는 100/5 = 20이므로 20개의 리퀘스트를 해당 CPU로 처리할 수 있는 반면, B언어는 100/15 = 6.666... 이므로 6~7개의 리퀘스트를 처리할 수 있으며, C언어는 100/50이므로 2개의 리퀘스트를 처리할 수 있으며, D언어는 100/100 1개의 리퀘스트를 처리할 수 있다. 따라서 대량의 리퀘스트를 처리해야 하는 작업인 경우 CPU의 한계로 인해 속도가 느린 언어는 서버를 증량하는데 언어의 속도 차이만큼 서버를 늘려야 한다는 단점이 있다.
- 또한 컴파일 언어의 경우, 인터프리터 언어에 비해 메모리도 적게 먹는 편이기 때문에 동일한 서버 사양에서 더 많은 리퀘스트를 처리할 수 있다는 장점이 있다. 제공하는 서비스가 가져다 주는 이익이 크다면 속도가 느린 언어를 사용해서 서버를 늘리는 것에 큰 비용을 지불해도 괜찮지만, 광고 등의 규모의 트레픽을 통해서 돈을 버는 서버라면 작은 규모의 서버에 다량의 트레픽을 처리할 수 있는 속도가 빠른 언어를 사용하는 것이 좋다.
- 하지만 이는 스프링 MVC와 같은 스레드 단위의 리퀘스트 처리에 맞는 논리는 아니다. 아무리 언어의 속도가 빨라도 웹 서비스에는 일반적으로 'IO처리를 대기하는 시간'이 걸릴 수 밖에 없고 스레드 단위로 리퀘스트를 처리하게 되면 'IO처리를 대기하는 시간'에 의해 하나의 스레드가 리퀘스트 처리를 완료하기까지 시간이 걸릴 수 밖에 없기 때문이다. 스레드 기반의 리퀘스트 처리는 언어의 속도에 따라 리퀘스트를 처리하는 단위를 무한정 계속 늘리기 어려운 점이 있고 스레드를 많이 늘리면 늘릴수록 스레드간의 컨텍스트 스위칭 비용이 증가하게 되면서 효율이 떨어지기 때문에 생성할 수 있는 스레드의 갯수에 한계가 있다. 스레드 수의 한계는 리퀘스트 수의 한계이고 리퀘스트 수에 한계를 갖는다는 것은 언어가 가진 속도적인 이점을 최대한 사용할 수 없게 만든다는 단점이 있다.
- 언어의 속도를 최대한으로 끌어 올리기 위해서는 고 언어와 같은 고루틴(코루틴)기반의 리퀘스트 처리를 할 수 있는 언어여야 웹 애플리케이션에서 해당 언어가 가진 속도를 최대로 끌어 올릴 수 있다. 또는 엘릭서의 BEAM과 같은 엑터 모델 기반의 언어가 언어의 속도를 최대로 끌어 올리는 서버 프로그래밍을 할 수 있다.
- 어차피 대량의 트레픽을 처리하기위해서는 쿠버네틱스 등을 사용해야 하지만, 어느 정도의 트레픽을 처리하기 전까지는 쿠버네틱스와 같은 학습 코스트가 드는 언어의 사용을 피하고 싶을 때가 있다. 이럴 때는 속도가 빠른 언어를 사용하는 편이 좋으며, 트레픽 보다는 지속적인 비즈니스 로직의 개선이 일어나야하고 좀 더 쉽게 짤 수 있는 언어를 사용하기 위해서는 속도가 좀 느리더라도 사용하기 편한 언어를 쓰는 것이 좋다.

## 비동기 서버
- 비동기 서버의 대표적인 예는 NodeJS의 서버들과 스프링 webflux와 같은 프레임워크를 예로 들 수 있다.
- NodeJS 계열의 서버들은 하나의 스레드 위에서 또는 webflux의 경우 고정된 스레드 언어 및 프레임워크에 내장된 이벤트 루프를 통해서 이벤트 큐에 쌓인 처리 대상들을 순차적으로 처리하는 방식을 가지고 있다.
- 고루틴, 코루틴 기반의 언어와 달리 비동기 서버는 IO 블락이 생겼을 때, 코어를 잠시동안 유휴 상태로 전환하지 않고 다른 처리를 하는 고루틴, 코루틴을 쓰레드에 할당하는 방식으로 유휴 상태의 코어를 최소화 하는 것과 다르게 IO 블락이 생기지 않도록 코드를 짜는 방식으로 만들어지는 서버이다. IO 블락이 발생한다는 것은 프로세스, 쓰레드, 코/고루틴으로 처리되고 있는 상태가 일시적으로 멈추고 해당 처리를 맡고 있던 CPU의 스레드에 다른 프로세스, 쓰레드, 코/고루틴을 맡긴다는 것을 의미한다.
- IO 블락이 발생하지 않게 하기 위해서, 비동기 서버는 이벤트를 만들어서 메모리의 큐 형태의 구조를 만들어 이벤트를 적재를 하고 IO 처리에 걸리는 이벤트는 따로 메모리에 보관을 하여 CPU의 스레드는 적재된 이벤트를 계속 처리하는 방식을 의미한다. 만약 IO 작업이 완료되었다면 IO 처리를 대기하던 이벤트는 다시 처리 대상이 되어 스레드에 할당된다. 스레드에 할당된 프로세스의 IO 블락이 일어나지 않고 IO 블락이 일어날만한 대상은 따로 처리를 하기 때문에 이벤트를 처리하는 스레드는 큐에 쌓인 이벤트를 계속 처리해야 하므로 큐에 적대된 대상이 모두 처리될 때까지 유휴상태가 되지 않는다.
- 비동기 서버는 큐에 쌓인 이벤트를 충분히 해소할 정도의 이벤트 처리 속도를 가지기 위해서 하나의 이벤트 처리에 연산량이 많은 작업을 할당하지 않도록 해야 하므로 무거운 연산 작업이 적은 광고 서버나 메시징 서버에 주로 사용된다.

## 비동기 방식의 단점
### 메모리 관리
- 비동기 서버는 이벤트를 처리하는 구조이기 때문에 특정 이벤트가 많은 CPU 리소스를 가져갈 경우 큐의 이벤트가 처리되어 소진되지 않고 이벤트가 쌓이기만 하므로 충분한 메모리 사이즈를 가져야 한다. 메모리 사이즈가 부족하면 서버가 터진다는 단점이 있으므로 CPU 리소스를 잡아 먹는 작업을 최소화하여 이벤트 큐가 쌓이지 않게 관리하는 등의 메모리를 잘 관리하기 위한 방안을 마련해야 한다.
- CPU 연산량이 많은 작업은 이벤트를 처리하는 스레드와 구분된 별도의 루틴, 스레드, 프로세스에서 처리하여 그 결과를 받아오는 방식을 통해서 처리하여 CPU 스레드가 하나의 이벤트를 처리할 때 많은 시간을 잡아먹지 않도록 한다.

### 디버깅 복잡성 증가
### 코드의 추상화 복잡성 증가
### IO 처리가 아니면 효과가 적다
- 서버에서 계산 관련 작업을 해야 하는 경우에는 CPU의 사용량이 높아지게 된다. 비동기 서버는 대량으로 쌓인 리퀘스트 이벤트, 콜백코드로 생기는 이벤트를 처리해야 하는데 CPU 사용량이 높은 작업이 들어오게 되면 이를 처리하지 못하게 되고 메모리에 적재된 다양한 이벤트들이 처리되지 않게 되고 계속적으로 발생하는 전달되는 리퀘스트에 의해 이벤트 관련 메모리가 쌓이게 되면서 서버가 터질 위험이 있다.
- 비동기 서버에서 CPU 사용량이 많은 연산 관련 로직 처리를 위해서 비동기 애플리케이션 자체에 연산 로직을 두지 않고 연산량이 많은 작업을 따로 처리하는 스레드나 프로세스를 따로 할당하거나 별도의 서버에서 처리하게 하고 그 결과값을 받는 방식으로 서버를 구성하는 방식을 사용한다. 자바스크립트에서는 이러한 처리를 위해 웹 워커라는 기술을 사용해 별도의 스레드에서 처리된 결과 값을 받는 방식을 지원하고 있다.
- 서버에서 비즈니스로직 계산관련 로직을 처리하기 보다는 클라이언트에서 이런 CPU 사용량이 높은 로직을 처리해서 서버로 전달하고 서버는 데이터의 입출력만을 위한 용도로 자바스크립트 진영에서는 NodeJS와 SPA(싱글 페이지 애플리케이션) 구조를 사용한다. NodeJS 서버는 단순히 디비에 데이터를 입출력하는 용도로만 사용하고 각종 비즈니스 로직과 연산 로직을 서버에서 처리하지 않고 클라이언트에서 처리할 수 있는 아키텍처를 구축하기 위해 React나 Vue를 사용한 SPA를 사용한다. 하지만 서버에서 처리하는 로직이 줄어들 수록 각종 처리에 관한 로직을 확인하고 추적할 수 있기 때문에 보안상 괜찮을까라는 우려가 있다.

### 굳이 비동기 서버를 만들어야 할까?
- 위에서 설명한 코드의 복잡성의 증가 및 대량의 트레픽이 아닌 경우에는 들인 비용에 비해서 효과를 보기 어려운 것이 비동기 서버이다.

## PHP는 사용하기 편한 언어일까?
- 여기서 사용하기 편하다는 것은 PHP라는 느린 언어를 사용할만큼 사용성에 편의를 주는 언어인가 하는 것이다.