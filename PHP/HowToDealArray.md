## php에서 배열의 특징
- php에서는 배열이란 하나의 타입에 `[1,2,3,4,5]`와 `['a' => 1, 'b' => 2, 'c' => 3, 'd' => 4, 'e' => 5]`의 두 가지 방식을 사용할 수 있다. 첫 번째는 인덱싱 배열이고 두 번째는 연관 배열이라고 부른다.
- 정적 언어에서는 인덱싱 배열은 키가 존재하지 않는 대상이다. 메모리의 어떤 위치에 데이터를 일정 바이트 간격으로 순차적으로 읽게 된다. 이런 방식으로 접근을 하기 때문에 배열의 각각의 인덱스에 해당하는 값에 접근할 때 빠른 속도를 보여준다.
- php에서의 배열은 연관 배열과 같은데 이는 `[1,2,3,4,5]`은 `[0 => 1, 1 => 2, 2 => 3, 3=> 4, 4 => 5]`와 동일하기 때문이다. 인덱싱 배열로 만들어도 어차피 php에서는 모두 연관배열로 처리한다.
- php의 밸경은 인덱싱 배열이나 연관 배열이나 처리 방식의 동일함으로 인해서 동일한 타입으로 만들어져 있다.

### 배열의 문제
- 함수나 클래스는 사용상의 스펙을 만드는 작업이다. 하지만 매개변수에 배열을 사용하게 되면, 배열 안의 어떤 값을 이용해야 할지 해당 코드만 보고서는 알 수 없다는 단점이 있다. 함수나 클래스 등 어떤 캡슐화 된 어떤 기능을 사용하기 위해서는 어떤 값을 전달해야 할지를 알아야 한다. 하지만, 배열 타입힌트만를 가진 대상을 받을 때는 배열으로 받을 수 있는 형태의 다양성으로 인해서 어떤 값을 받아야 할지 애매한 경우가 많다.
- 복잡한 소프트웨어를 만들 수록 각 단위 기능의 테스트 가능성은 중요하다. 일반적으로 테스트가 가능하다는 것은 마치 순수함수와 같이 인풋에 대해 아웃풋을 가질 수 있어서 따로 떼어 내었을 때 인풋을 넣었을 때 아웃풋의 결과를 확인할 수 있는 것을 의미한다. 그런데 배열을 인풋으로 받으면 배열의 어떤 형태를 인풋으로 받아야 할지 알 수 없기 때문에 동작이 잘 이뤄지는 것인지 모호한 경우가 생길 수 있다.
- 특정 기능만을 따로 떼어 냈을 때 어떤 인풋을 넣어 주어야 아웃풋을 확인할 수 있는지 넣어주는 값을 정해야 하는데, 배열의 경우 그 형태가 다양해서 어떤 값을 넣어 주어야 할지 생각하기 어려운 경우가 많다. 결국 스펙만을 보고서는 어떤 입력에 어떤 결과값을 갖는지 추측하기 어려운 경우가 많이 발생할 수 있고, 내부 구현을 확인할 수 밖에 없는 코드를 만들어 낼 가능성이 있다.
- 전달된 배열을 내부에서 어떻게 처리를 할지 어떤 값을 전달해야 해당 기능의 스펙에 맞는 동작을 만들어 낼 수 있을지를 확인하기 위해서 내부의 코드의 처리 과정을 확인해야 한다는 것이다. 프로그래밍에서 추상화는 내부의 로직을 확인하지 않더라도 어떤 기능을 가졌는지 추론할 수 있도록 만드는 것인데 php의 배열을 사용하게 되면 함수나 메소드 스펙만 봐서는 어떤 배열의 값을 전달해야 할지 알 수 없게 되는 문제점이 생긴다.

#### 문자열과의 비교
- 문자열 매개변수의 예를 생각 해 보자. 일반적인 코딩 스타일에서 문자열 그대로에 대해 어떤 일반적인 처리를 하는 것이 아니라, 어떤 식별자 또는 구분자로 문자열을 사용하는 코딩 스타일을 좋게 여기지 않는 경우가 많다. 그래서 문자열의 구분자나 식별자를 사용하는 코딩 스타일의 경우 enum을 사용하거나 커스텀 클래스를 만들어 `new CutstomTeyp(문자열)`의 생성자로 문자열을 전달하고 `__invoke` 매직 메소드를 통해서 벨리데이션 된 문자열을 사용하는 방법을 사용한다. 이는 문자열은 너무 다양한 값이 들어 올 수 있기 때문에 구분자 또는 식별자로 사용할 경우에는 이러한 스타일을 제약할 수 있도록 enum이나 커스텀 class를 만들어 타입힌트로 이들 타입을 사용하는 것을 추천한다.
- 문자열이 너무 다양한 값을 가지기 때문에 기능의 스펙을 정하기 위해서 enum이나 커스텀 class를 만들어 타입힌트로 사용하는 것과 마찬가지로 배열 또한 단위 기능의 스펙상의 제약을 걸기 위해 타입힌트를 사용하는 방법을 생각하는 편이 좋다.