## PHP에서의 OOP
- php는 OOP 언어의 특징인 class, interface, abstract class, extension (상속) 등을 지원한다.
- 물론 PHP에서는 OOP 언어의 키워드 없이도 로직을 짤 수 있지만, 대부분의 경우 OOP로 구성된 프레임워크를 사용하고 프레임워크의 기능을 사용하기 위해서는 OOP 지식을 알아야 한다.

## 왜 OOP 키워드를 사용할까?
### 객체화의 장점
- 객체화를 하는 것은 하나의 기능에 대한 책임을 분산하는 역할을 한다. 
- 객체의 내부 구현은 숨기고 인터페이스만 노출하여 객체의 인터페이스를 사용하여 더 추상화 된 로직을 짜는 방식을 사용해서 코드의 재상용성을 높인다.

#### 재사용성
- 재상용성 높은 코드를 패키지로 제공하여 다른 사람들이 패키지의 인터페이스에 대한 지식만 알면 쉽게 요구사항에 필요한 로직을 짤 수 있다.

#### 도메인 로직
- 비즈니스 로직을 짤 때 객체화를 하여 인터페이스들로 로직을 구성하면 도메인 지식에 맞는 쪽으로 기능을 분리 및 추상화 해 나갈 수 있다.
- 어떤 목적을 달성하기 위해 로직을 짜는데 요구사항에 맞게 객체를 캡슐화하고, 도메인 용어로 인터페이스를 정의하면 비즈니스의 요구사항과 시스템의 구성이 매칭되기 때문에 이해하기 쉬으며 도메인의 요구사항 변경에도 쉽게 대응할 수 있는 코드를 짤 수 있다.

#### 태스트의 단위
- 태스트 코드를 짜기 위해서는 가능한 작은 단위로 만들어야 한다. 책임에 따라 객체를 만들면 로직상의 문제를 쉽게 파악이 가능하다. 또한 객체별로 독립적인 기능을 담당하기 때문에 객체 외부 코드와의 의존성이 감소한다.

## PHP에서 OOP의 특별한 특징
- 불변성에 대한 키워드, 타입에 대한 키워드를 객체 단위로 제공하고 타입에 대한 키워드를 객체 또는 함수 단위로 제공한다.
- 불변성과 타입에 대한 정의는 멤버 변수에만 지정할 수 있으며, 타입에 대한 제한은 메소드(또는 함수)의 인자에 지정할 수 있도록 제공된다.
- 물론 타입을 제한할 수 있는 PHP의 키워드가 있다. `is_null()`, `is_int()`, `is_string()`, `is_float()` 등등의 기본 타입을 체크할 수 있는 내장 함수 및 `insteadof`와 같이 객체가 주어진 클래스의 타입인지 확인하는 등의 기능이다. 하지만 이런 함수들을 사용하는 것 자체가 변수가 다른 타입으로 변경되는 것을 막기 위한 역할이 아니라, 변수의 값이 해당 타입인지 확인하는 것이기 때문에 한번 정의하면 계속 동일한 타입을 유지하는 변수와 달리 사용할 때마다 일일이 체크를 해 줘야 하는 것이므로 이들 함수를 여러번 사용하게 만들어서 생산성을 저하시키는 방식의 코딩을 유도한다.
- 그렇다면 객체의 멤버로 타입 제한을 거는 방식을 최대한 사용하는 방식으로 코딩하는 법을 생각할 수 있다. 이를 위해서는 객체나 함수를 최대한 많이 만들어서 사용하는 것인데 객체에 대한 보일러 플레이트, 함수에 대한 보일러 플레이트의 양도 작은 것이 아니기 때문에 생산성을 저하시킨다. 특히 메소드 분리의 경우는 사용할만 하지만 객체의 분리는 네임스페이스 use 키워드 등등 보일러 플레이트가 지나치게 많다는 문제가 있다.
- 타입 안정성이 있는 생산성 높은 언어를 만들려면 메소드 내에서도 불변성 및 타입 키워드를 사용할 수 있어야 할 것 같다. nested class로 생성된 오브젝트에 getter만을 사용하는 방식으로 불변 키워드를 사용할 수도 있고 php8에서는 nested class로 생성된 오브젝트에 readonly를 사용해서 불변성을 사용하는 방법도 있지만, 오브젝트가 저장된 변수에 들어간 값이 바뀔 수 있기 때문에 완벽한 해결책은 될 수 없고 객체로 생성하는 것 자체가 보일러 플레이트를 야기한다.


