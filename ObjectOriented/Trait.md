## 트레이트란?

트레이트란 클래스의 멤버(멤버 변수와 메소드)를 클래스 외부에 정의하여 클래스에 가져다가 쓰는 기능이다.

## 맥락 의존성

클래스를 정의하는데 있어서 맥락이란, 클래스의 기능을 사용하고자 할 때 해당 클래스에 의존적인 기능을 의미한다.

이 의존성은 클래스 내부의 멤버에 대한 접근을 통해 멤버의 상태 변화에 영향을 받고 영향을 주는 것과 상태 변화와 관련 없이 해당 클래스의 의미론적인 맥락에서만 사용되는 것을 뜻한다.

트레이트는 맥락 의존성을 가지도록 설계를 하고 사용할 수도 있고, 맥락 의존성을 가지지 않도록 정의해서 사용할 수도 있다.

## 폴더 구조의 중요성

트레이트가 특정 클래스에 종속적이냐 아니냐를 구분하기 위해서는 트레이트 파일이 위치한 폴더의 위치가 중요하다. 특정 도메인에서 사용되는 트레이트가 있을 수 있고, 특정 클래스에서만 사용하는 트레이트가 있을 수 있고, 여러 클래스에 범용적으로 사용될 수 있는 트레이트가 있을 수 있다. 이 경우 각각의 폴더 위치는 도메인에 의존적이면 해당 도메인을 나타내는 폴더 내부, 공용이면 특정 도메인을 나타내는 곳과는 별개의 폴더, 특정 클래스에서만 사용되면 해당 클래스가 위치한 폴더 또는 그 하위 폴더에 트레이트를 정의하는 것으로 트레이트가 어떤 클래스와 맥락에서 사용되는 것인지 알 수 있게 한다.

## 정적 분석 툴의 제약

php-stan과 같은 정적 분석 툴을 사용해 보면, 트레이트에서 클래스의 멤버를 사용하는 것은 정적 추론이 되지 않기 때문에 트레이트 자체에서 정의된 멤버가 아니라면 사용하지 못하게 코딩 스타일을 제안한다.

php의 문법 자체로는 트레이트에 멤버가 정의되지 않더라도, 사용하는 클래스의 멤버가 정의되어 있다면, 트레이트는 클래스에서 추가해서 사용하는 것이기 때문에 특별한 문법적인 제약은 없다. 그러나 정적 분석 도구는 이를 지적한다. 이를 통해서 볼 때 트레이트는 자체 완결성 곧 메소드에서 사용하려는 멤버는 트레이트 자체에 정의되어 있어야 하고, 클래스의 상태에 의존적으로 코드를 만들기 보다는 클래스의 상태에 관계 없이 트레이트 자체의 완결된 동작을 가질 수 있도록 코드를 작성하는 것이 좋다.

물론 클래스의 멤버에 의존적인 코드를 작성할 수도 있는데, 이 경우, `property_exists`와 같은 코드 또는 `assert`를 통해서 멤버의 존재를 사전 체크한 이후 이용할 수 있도록 코드를 작성하도록 해야 한다. 반드시 클래스의 특정 멤버의 존재를 전제로 할 때는 `assert`를 클래스의 특정 멤버가 존재할 때 코드의 동작을 달리 할 때는 `property_exists`를 사용하지만, 트레이트에 멤버를 만들어 사용하려는 클래스가 트레이트의 메소드를 호출한 후 멤버의 값을 할당하도록 코드를 만드는 쪽을 권장한다.

## 트레이트는 객체 또는 클래스의 의미나 맥락에 의존한다.

만약 자체적으로 완결된 기능을 수행하는 경우에는 트레이트를 굳이 사용할 필요가 없이 자체적으로 완결된 기능을 가진 객체를 불러다가 쓰면 된다.

트레이트를 사용한다는 것은 클래스 또는 객체의 맥락에 의존하는 로직이 들어 있다는 것을 의미한다. 클래스 또는 객체의 맥락에 의존적이라는 것은, 기존 클래스나 객체에 존재하는 특정 기능을 트레이트를 추가하여 기존 메소드의 동작을 추가한 트레이트의 맥락적 추가에 맞게 좀 다르게 사용한다던가, 기존 메소드를 변형한 추가 메소드를 사용하거나 트레이트로 추가된 메소드를 사용하겠다는 의도를 가진 코드를 만드는 것이다.

## 상속과는 다른 공통적인 기능을 구현할 때

단순히 상태변화에 의존적인 기능을 추가하는 것이라면 클래스 내에 멤버를 더 추가하는 방식으로도 가능하다.

유사한 여러 종류의 클래스를 만들어야 하는 경우 기본 기능 이외의 어떤 클래스들에는 기능을 추가하고 어떤 클래스들에는 기능을 변경하기 위한 방법으로 트레이트를 정의하는 방법이 있다.

트레이트라는 것은 여러 유사한 클래스에서 사용하기 위해 상속된 기능 이외의 기능의 추가 변경의 공통된 로직을 정의하기 위해서 사용한다.

## 사용 예

라라벨에서 엘로퀀트 모델을 사용할 때 프레임워크의 Model 객체를 상속하여 만든다. 이때 soft delete의 기능을 쓰고 싶다면 엘로퀀트 모델을 사용하는 클래스 내의 `SoftDeletes` 트레이트를 사용한다. 엘로퀀트를 통해서 데이터를 가져올 때 `deleted_at` 컬럼에 기록된 데이터는 기본적으로 가져오지 않도록 처리된다.

라라벨에서 모델의 사용은 흔한 일이며 모델을 사용할 때 soft delete를 사용하는 쪽이 좋은 경우도 아닌 경우도 있다. 모두 Model 객체를 상속하여 쓰는데 soft delete 기능은 일반화 해서 코드 개발자에게 제공할 수 있는 기능이다. 따라서 기존의 처리 로직을 변경한 것을 사용할 수 있도록 프레임워크에서 사용자의 구현의 불편함을 덜기 위해 미리 정의하여 사용할 수 있게 한 것이다. 
