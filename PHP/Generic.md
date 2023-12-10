# 제네릭

## 자료구조
- 프로그래밍을 배울 때 자료구조라는 데이터를 다루는 방법을 배운다. 이 자료구조는 데이터를 저장하는 방식에 대한 것이고, 데이터를 저장하는 방식에 따라 데이터를 저장하는 과정과 데이터 더미에서 특정한 데이터를 뽑아 내고자 할 때의 효율이 달라지기 때문에 적절한 데이터 구조를 사용해서 프로그래밍을 해야 한다고 가르친다.

## php에서 자료구조
- php는 연관 배열이라는 자료 구조를 사용한다. 연관배열은 키-벨류 쌍의 데이터를 저장하는 방식이다.
- php에서 키를 지정하지 않고 배열에 데이터를 저장을 해도 0,1,2,3...의 인덱스가 생기는데 php에서 배열은 연관배열이므로 자동으로 키를 생성하기 때문이다.
- 배열이외에도 다른 자료구조의 개념이 존재하며 [spl](https://www.php.net/manual/en/book.spl.php)이라는 php 언어에 내장되어 있는 자료구조를 다룰 수 있는 기능이 있다. 하지만 spl 라이브러리를 사용한 사례는 잘 찾아보기 어려운데, php에서 제공하는 연관 배열으로도 다양한 처리를 할 수 있는 편리함을 제공하기 때문에 불편한 spl 라이브러리를 사용할 필요성을 느끼지 못하는 경우가 많기 때문이다.

### spl 표준 라이브러리
> “SPL data structures are horribly designed.” — Anthony Ferrara [<sup>링크</sup>](https://medium.com/@rtheunissen/efficient-data-structures-for-php-7-9dda7af674cd)
- 하지만 php를 사용하는 대부분이 성능 보다는 비즈니스 로직에서 발생하는 요구사항을 처리하기 위해서 만드는 경우가 대부분이라 성능적인 비판이 있음에도 사용되는데 큰 문제가 없다.
- 연관 배열은 워낙 다양한 방식으로 다뤄야하다보니 특정한 방식으로 데이터를 다뤄야 할 필요성이 있다면 spl 라이브러리로 데이터를 저장하여 데이터 사용 방식에 제약을 두는 편이 좋을 때 사용할 수 있다.

## 제네릭
- 프로그래밍 언어에서 제네릭은 타입에 의존적이지 않는 기능을 제공하는 것이다.
- 어떤 타입을 사용하고자 할 때 지정한 타입의 내부에서 사용할 타입을 함께 지정할 수 있는 문법을 '제네릭 문법'이라고 한다. 어떤 타입을 사용할 때 내부에서 사용할 타입을 다양하게 지정해서 사용할 수 있는 기능으로 내부에서 사용할 타입을 다양하게 지정할 수 있지만 지정할 때는 한 타입만 지정해야 한다.
- 자료구조에서 특히 제네릭을 많이 사용하는데 한 유형의 자료구조가 저장하는 값을 특정 타입에 구애받지 않고 저장할 수 있도록 하기 위함이다. 자료구조의 타입을 지정하고 자료 구조 내부에 저장할 값인 내부 타입을 지정하는 것을 통해서 자료구조에서 저장할 값을 사용할 때의 타입을 정하고 사용할 수 있다.
- 예를 들어 어떤 자료구조를 사용하는데, A라는 타입의 데이터만 저장할 수 있다면, B라는 타입일 때 사용할 수 있는 자료구조를 또 만들어야 하고, C라는 타입일 때 사용할 수 있는 자료구조를 또 만들어야 한다. 하지만, 제네릭 개념을 적용한 자료구조라면 어떠한 타입이든 저장할 수 있는 특성을 갖고 있기 때문에 저장할 데이터의 타입에 관계 없이 사용할 수 있는 유연성을 제공한다.

### 컴파일 언어에서의 제네릭
- 컴파일 언어에서 어떤 자료 구조를 정했다면 저장할 데이터의 형식을 정해야 한다. 어떤 타입을 사용할 때 그 내부의 타입을 지정하는 것이다. 이 때 저장할 데이터가 가진 타입을 지정할 수도 있고 인터페이스를 지정할 수도 있다. 여러 타입을 지정하고 싶은 경우에는 공통의 인터페이스 또는 상위-하위 관계의 클래스 또는 여러 클래스의 공통의 상위 클래스를 타입으로 지정해야 한다. 전혀 관계 없는 두 타입을 자료구조의 데이터 타입으로 지정할 수 없으며 이는 어떤 자료 유형의 데이터 저장소에서 데이터를 꺼내 쓸 때 인터페이스가 달라지면 런타임의 동작에서 치명적인 에러가 발생할 수 있기 때문에 동일한 접근방식을 갖도록 제한을 하기 위해서이다.
- 컴파일 언어는 타입을 통해서 제한된 방식의 코딩 스타일을 제공하고 컴파일을 통해서 기계어나 기계어에 가까운 레벨의 중간언어(JAVA의 Bytecode, C#의 CLI : Common Intermediate Language)로 번역을 한다. 기계어나 중간언어는 컴파일 타임에 모든 타입 안정성을 체크하기 때문에 런타임의 동작에서 타입을 체크하지 않아도 타입으로 인한 에러가 발생하지 않는다. 이런 런타임에서 에러를 방지하기 위해 실행 도중에 타입을 체크하지 않으므로 컴파일 언어는 인터프리터 언어 보다 속도가 빠르다.
- 자료구조의 경우 데이터를 저장하고 꺼내쓰는 작업을 하는데 데이터를 꺼냈을 때 어떤 타입인지 알아야 어떤 접근을 할지 알 수 있다. 컴파일 언어는 런타임에 타입 체크를 하지 않기 때문에 데이터를 꺼냈을 때 어떤 타입인지 체크하는 동작을 하지 않고 꺼내 쓴다. 따라서 값을 사용할 때의 접근 방식을 컴파일 타임에 지정해 두어야 한다. 자료구조의 사용과 동시에 자료 구조 안에 들어갈 타입을 지정하는데 이것이 컴파일 언어에서 제네릭이라고 부르는 문법이다.

### php에서의 제네릭
- php와 같은 동적 언어는 어떤 자료구조에 데이터를 넣을 때 타입 제약이 없다. 타입 제약이 없는 대신 하나의 자료 구조에 여러 타입의 데이터를 넣을 수 있다. 서로 상이한 타입의 값을 넣었다면 각각의 값이 갖는 접근 방식아 다름으로 인해서 런타임에서 코드가 동작할 때 동일한 타입을 사용하면 사용할 수 없는 접근 또는 정의되지 않은 접근을 할 때 에러가 발생한다. 하지만 php의 접근 에러는 프로그래밍 언어에서 통제할 수 있는 것으로 컴파일 언어에서 접근이 원천적으로 차단되어 있는 것과는 다르다.
- 프로그래밍 언어의 관점에서 php는 이미 제네릭의 정의가 말하는 것 처럼 자료 구조에 다양한 타입의 값을 저장할 수 있는 특징을 갖고 있다. 많은 컴파일 언어가 자료구조를 사용할 때 공통의 접근방식을 갖는 제한된 타입을 지정해야 하는 반면, php와 같은 동적 언어는 런타임에 발생하는 에러를 다룰 수 있기 때문에 타입을 지정하지 않더라도 자료구조에 데이터를 저장할 수 있다.

### 제네릭 문법이 없는 PHP
- php는 제네릭의 특성은 갖고 있지만, 제니릭 문법은 지원하지 않는다. 제네릭 문법은 어떤 타입을 사용할 때 그 내부의 타입을 함께 지정하는 것이다.
- 프로그래밍 언어에 타입에 대한 제약 사항이 없다면, 코드를 짤 때 실수할 가능성이 높아진다. php는 이러한 문제를 해결하기 위해서 타입힌트라는 기능을 도입했다. php에서 타입힌트는 클래스의 멤버 변수 및 함수/메소드의 인자와 반환값에 지정할 수 있다.
- 타입힌트를 지정할 수 있는 array와 같은 자료 구조를 사용할 경우 자료 구조 안에 넣을 값의 타입을 지정하여 자료 구조 안의 값을 제한하는 문법을 php에서도 지원하면 컴파일 언어와 같이 자료구조의 값을 넣거나 가져올 때 타입제한을 통해 타입 안정적인 코드를 짜고자 하는 것이 php에서의 제네릭이다.
- php에서는 지정한 타입의 내부에서 사용하는 타입을 지정할 수 없기 때문에 array 타입 내부에 저장할 타입을 타입힌트를 통해서 제한하지 못한다. 이 때문에 어떤 타입의 내부에서 사용할 타입을 지정할 수 없는 상황이 오면 타입힌트를 통한 타입제한을 하지 못하므로 불안한 느낌이 들 때가 있다.
- php에서는 타입힌트로 사용하고자 하는 타입의 내부 타입을 설정하는 제네릭 문법이 없기 때문에 타입힌트 위치에서의 내부 타입을 고정하는 방식이 아닌, 어떤 타입의 내부에서 사용하는 타입을 사용할 때 타입을 확인하는 방식을 사용해야한다.
- 예를 들어 배열의 경우 배열의 데이터를 꺼내 사용하고자 할 때 해당 값의 타입을 확인하는 방식이다. 데이터를 사용할 때 꺼내쓰는 데이터의 타입을 일일이 확인하는 코드를 추가해도 되지만 배열과 같은 iterator에는 콜백함수를 사용할 수 있다. php는 함수에 타입힌트를 지정할 수 있으므로 콜백함수에 타입힌트를 추가하는 것으로 값을 자료구조에 저장하거나 꺼내 쓸 때 타입을 체크할 수 있다.

### foreach문이 권장되지 않는 이유
- php에서 foreach문을 사용할 때는 리스트에 든 각각의 값에 대해 타입 검증을 하지 않고 사용하는 경우가 많다. `is_타입` 또는 `instanceof`로 타입 검증을 할 수 있지만, 이러한 것으로 검증을 했을 때, IDE에 의한 자동완성이 되지 않는 경우가 있을 수 있으며 조건문으로 확인을 한다는 것이 문법적으로 맘에 드는 방법은 아니다. 그래서 코딩의 스타일을 `foreach`를 쓰는 것 보다는 콜백함수를 가능한 사용하는 방식으로 하여 타입힌트로 값을 검증하는 것이 좀 더 괜찮은 코딩 스타일이다.
- php의 배열의 경우 `foreach`를 `array_walk`를 사용하여 콜백함수를 사용할 수 있는 방식을 사용할 수 있고, 배열이 아닌 다른 `iterator`의 경우 라라벨의 컬렉션 패키지를 사용해서 콜백 형태로 만들어서 사용하는 것을 추천한다.