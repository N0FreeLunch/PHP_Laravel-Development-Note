## DTO란 무엇인가?
- DTO는 data transfer object의 약자이다.
- 프로그래밍이란 컴퓨터를 이용해서 데이터의 변화를 진행시켜 나가는 과정이라고 할 수 있다. 결과를 유도하거나 어떤 기능을 만들기 위해 데이터를 변화시키는 것이 DTO이다.
- 데이터를 변화시키는 방식은 일반적으로 어떤 변수에 데이터를 담아서 변화시키는 과정이 있으며, 함수형 프로그래밍과 같이 변수에 데이터를 담아 변화시키지 않는 방식의 프로그래밍 방식도 존재한다.
- 이때, 어떠한 저장기능을 가진 대상의 구조를 일관성 있게 유지하면서 데이터의 변화를 유도하기 위한 방식으로 DTO를 사용한다.

## DTO의 특징
- getter/setter로 구성되어 있다. getter만 존재하는 DTO도 있으며, getter/setter가 존재하는 DTO도 있다.
- getter만 존재하는 DTO는 정해진 구조의 저장대상에서 데이터를 계속 가져다 쓰기 위해서 사용하며, getter와 setter가 함께 존재하는 DTO는 DTO라는 일관된 구조 내에서의 값의 변화를 유도하기 위해서 사용한다.
- DTO에는 비즈니스 로직을 넣지 않는다. 이는 데이터의 전달의 역할만 담당하게 하기 위해서이다.


## PHP에서 DTO를 사용해야 하는 이유
- PHP에서의 데이터 전달은 변수를 통해 전달하거나 연관 배열을 통해 데이터를 전달한다.
- 스칼라 데이터를 담은 변수를 통한 데이터 전달은 스칼라 값 하나만들 통해 데이터가 전달되기 때문에 관심사를 한 번에 모을 수 없는 단점이 있다.
- 연관 배열을 통한 데이터 전달은 관심사를 한번에 모을 수 있고 연관 배열이 너무 유연하기 때문에 사용하기는 쉽지만 플로우의 전후 데이터 구조가 정해져 있지 않다보니 데이터 구조의 변화가 있는지 확인을 해야하는 불편함이 생긴다. 또한 연관 배열 내부에는 타입을 지정할 수 없기 때문에 타입의 변화가 생겨 플로우를 진행하는 도중 타입 불일치로 문제가 발생할 여지를 차단한다.
- DTO를 사용하는 것은 객체를 만들어 써야하고, 제대로 쓰기 위해서는 생성자, setter, getter를 만들어써야 하기 때문에 지나친 보일러 플레이트를 만들어 낸다는 점이다.

## getter/setter가 필요한가?
- getter와 setter는 객체 내부의 변수 통제용으로 사용하는 것이며, 멤버 변수를 객체 밖으로 노출시키지 않는 이유는 변수가 외부에서 컨트롤 되는 상황을 막기 위해서이다. 아주 단순한 객체라고 하더라도 추후 외부의 출력과 입력에 로직이 추가될 가능성이 있기 때문에 가능한한 getter와 setter를 사용해서 객체를 만들도록 한다.
- 하지만 DTO의 경우 특별한 비즈니스 로직을 담지 않는다. 데이터 전달을 위해 할당할 수 있는 값의 형태와 범위를 제한하는 용도로 멤버 변수는 비공개로 하고 getter와 setter를 사용할 수 있지만 엄격한 값의 제한이 필요하지 않으며, 예상 외의 변수가 없으며 컨트롤이 가능한 범위 내에 있으며 단순 타입 제한 정도의 역할로서 객체를 사용할 수도 있다.

### nested class
- 클래스 내부에서 클래스를 선언할 수 있다.
```
class User
{
    protected $money;
    public function __construct()
    {
        $money = new class {
            public $account = 100000;
            public $cash = 5000;
            public $stock = 50000;
        }
    }
}
```
- 클래스 내부에서 간단하게 객체를 선언할 수 있다.
- 이렇게 nested class를 만들어서 사용하게 되면, 이는 이 클래스 내부에서 통제되는 용도로 주로 사용한다. 주로 선언된 클래스 내에서 다뤄지기 때문에 외부 요인에 의해 저장된 값에 예외 사항이 생길일이 거의 없다.
```
class User
{
    public $money;
    public function __construct()
    {
        $money = new class {
            public int $account = 100000;
            public int $cash = 5000;
            public int $stock = 50000;
        }
    }
}
```
- php 7.4 부터는 멤버에 타입을 선언할 수 있기 때문에 간단한 구조와 타입이 제한된 저장소로서의 역할을 한다.
- 위의 구조는 객체 내에서 단순하게 만들 수 있는 데이터 전달 객체이다.
- 흔히 getter와 setter를 정의하는 데이터 전달 객체는 객체와 객체 간의 데이터 전달을 필요로 할 때, DTO를 독립적으로 정의하여 사용하는 것을 목적으로 한다. 독립적인 객체이기 때문에 어떤 문맥에서 사용될 수 있을지 모르기 때문에 객체 멤버 변수는 메소드에 의해 통제 되도록 한다.


## 객체지향의 DTO와 함수형 프로그래밍의 모나드
- 함수형 프로그래밍에서는 모나드라는 개념이 있다. 쉽게 말해서 자연수와 자연수의 덧셈 연산의 결과는 항상 자연수이다. 연산의 결과가 자연수이기 때문에 결과를 가지고 자연수 연산을 해도 자연수이다. 어떤 연산에 대해 특정 범위(여기서는 자연수)에 관해 닫혀있다는 것이 중요한 개념인데, 덧셈에 대한 연산은 자연수에 대해 닫혀있다라고 수학에서는 표현한다.
- 함수형 프로그래밍에서 모나드는 객체지향 프로그래밍에서 DTO와 비슷한 개념을 가지고 있다. 데이터의 구조를 연산(함수형 프로그래밍에서는 함수를 사용한 전후) 전후에도 연산할 수 있도록 프로그래밍 플로우의 진행 전후에도 일정하게 유지시킨다는 점에서 비슷한 개념을 가진다.
- 이렇게 연산 구조를 일정하게 유지하는 것은 동일 데이터 구조를 가지고 있기 때문에 무언가를 적용할 때도 재사용할 수 있으며, 프로그래밍의 진행 플로우에서도 일정한 구조내에서 값이 변하며 일정 틀 안에서의 값의 변경이라는 점을 알 수 있다는 것 때문에 변화의 흐름을 추측하고 해석하기 쉽다는 장점이 있다.
- 함수형 프로그래밍에서 모나드를 사용하면 연산 전후의 데이터의 구조가 일치하고 동일한 데이터 구조를 통해 계속 함수를 재사용해서 결과값을 만들어 나갈 수 있다. 마찬가지로 객체지향에서도 DTO의 사용을 통해서 여러 객체에 대한 재사용을 할 수 있는 또는 하나의 도메인에 대해서 동일한 데이터 구조의 변화를 통해 데이터 변화의 플로우를 만들어 나갈 수 있다.
- 물론 DTO보다는 모나딕한 구조를 가지는 것이 재사용성에서 훨씬 압도적이기는 하지만 모나딕한 구조를 정의하는 것이 쉬운 것이 아니기 때문에 대부분 특정 타입 변수의 리스트 형식의 구조로 퉁친다. 이에 반해 DTO는 특정 관심사에 대한 데이터를 주제별로 정의하기가 쉽다는 장점이 있다.


## PHP에서 DTO를 사용하는 방법
### 직접 DTO 객체를 만드는 방법
- 연관 배열을 DTO로 만들어 주는 매퍼 라이브러리 또는 툴 코드를 작성

### 라이브러리를 통한 DTO 객체 만들기
- https://github.com/spatie/data-transfer-object // spatie에서 나온 신뢰할 수 있는 DTO 라이브러리
- 연관 배열을 DTO 객체로 만들어 주는 기능을 제공하기 때문에 적은 코드로 DTO 객체를 만들어 쓸 수 있는 장점이 있다.
- PHP의 기본 문법을 사용하지 않고 라이브러리 의존적이라서 새로운 개념을 배워야한다는 압박감을 줄 수 있지만, DTO 개념을 PHP에서 사용하고자 한다면 필수적이며, 객체지향의 원리에 맞고 타입 제한이 되어 있는 데이터전달 객체를 사용한 프로그래밍을 하려면 굉장히 유용한 방법이다.


## DTO의 구조
- DTO의 구조는 단순해야 한다.
- DTO는 데이터를 전달하는 목적으로 사용하기 때문에 비즈니스에 관한 로직을 처리를 하지 않는다.
- DTO에 들어 오는 데이터의 범위를 제한 하기 위해서 잘못된 값이 들어 올 경우, 에러를 발생 시키는 등의 로직을 처리할 수 있다.
```
class BlogData
{
    /** @var string */
    private $title;
    
    /** @var Status */
    private $status;
    
    /** @var \DateTimeImmutable|null */
    private $publishedAt;
   
   /**
    * @param string $title 
    * @param Status $status 
    * @param \DateTimeImmutable|null $publishedAt 
    */
    public function __construct(
        $title,
        $status,
        $publishedAt = null
    ) {
        $this->title = $title;
        $this->status = $status;
        $this->publishedAt = $publishedAt;
    }
    
    /**
     * @return string 
     */
    public function getTitle()
    {
        return $this->title;    
    }
    
    /**
     * @return Status 
     */
    public function getStatus() 
    {
        return $this->status;    
    }
    
    /**
     * @return \DateTimeImmutable|null 
     */
    public function getPublishedAt() 
    {
        return $this->publishedAt;    
    }
}
```
- 객체 외부에서 사용할 수 없는 멤버 변수를 선언한다.
- 생성자를 통해 객체 생성 시 멤버 변수에 값을 할당한다.
- 각각의 멤버를 객체 외부에서 불러 올 수 있는 메소드를 작성한다.


### PHP 8.0에서의 DTO 구현
```
class BlogData
{
    public function __construct(
        private string $title,
        private Status $status,
        private ?DateTimeImmutable $publishedAt = null,
    ) {}
    
    public function getTitle(): string
    {
        return $this->title;    
    }
    
    public function getStatus(): Status 
    {
        return $this->status;    
    }
    
    public function getPublishedAt(): ?DateTimeImmutable
    {
        return $this->publishedAt;    
    }
}
```
- 클래스 내부에 멤버 변수를 따로 선언하지 않고, 생성자의 인자를 통해 멤버 변수를 생성할 수 있다. 변수의 초기할당과 동시에 
- 하지만 getter는 만들어 줘야 하는 불편함은 여전히 남아 있다.


### PHP 8.1에서 DTO의 구현 (setter only)
```
class BlogData
{
    public function __construct(
        public readonly string $title,
        public readonly Status $status,
        public readonly ?DateTimeImmutable $publishedAt = null,
    ) {}
}
```
- readonly는 변수 안에 들어 있는 값을 바꿀 수 없게 만든다. 따라서 멤버를 바꾸기 위한 setter를 정의해도 사용할 수 없다.
- 변수에 객체가 담겨 있을 경우 객체를 가리키는 주소를 바꿀 수 없는 것이므로 다른 객체를 할당할 수 없다. 하지만 이미 할당된 객체의 내부를 변경할 수는 있다.
- read only를 사용한다는 것은 value object로 객체를 사용할 때 유용하다.


## Reference
- https://library.gabia.com/contents/8488/
- https://medium.com/musinsa-tech/%EB%AA%A8%EB%8D%98-php%EC%97%90%EC%84%9C-%EB%B0%B0%EC%97%B4-%EB%8C%80%EC%8B%A0-dto-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-d9da20ea716e
- https://stitcher.io/blog/php-81-readonly-properties
- https://dev.to/scottshipp/avoid-getters-and-setters-whenever-possible-c8m
