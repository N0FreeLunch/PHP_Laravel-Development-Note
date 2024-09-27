## Enumerations

### php에서 열거형이 만들어진 목적

어떤 식별자를 함수의 매개변수로 전달할 때 string 타입을 사용하면 실수 할 가능성을 높인다. 이 때문에 이미 몇 가지로 정해진 식별자라면 문자열이 아닌 enum 타입을 사용해서 매개변수로 전달하면 실수를 방지할 수 있다.

다른 많은 언어가 enum을 타입으로 많이 지원을 하고 php도 그에 맞춰 enum을 타입으로 사용할 수 있게 만들어 각종 식별자들을 enum을 사용하여 만들 수 있게 되었다.

### 예제 코드
```php
<?php
enum Suit
{
    case Hearts;
    case Diamonds;
    case Clubs;
    case Spades;
}

function do_stuff(Suit $s)
{
    // ...
}

do_stuff(Suit::Spades);
```

### 열거형의 특징

php의 열거형은 클래스 문법을 기반으로 만들어져서 클래스를 만드는 방식과 유사한 방식으로 만들어지면 'enum 열거형명 { }'의 형태로 만들어진다. 이를 열거형(Enumerations)이라 부른다.

열거형의 가능한 값을 나열할 때는 enum의 중괄호 안에 case 문법을 사용해서 열거형 집합이 나타내는 원소명을 나열한다. 각각의 케이스(case)를 enum이라고 부른다.

'Enumerations명::enum명'의 방식을 통해서 하나의 enum을 사용할 수 있다. 나열되는 항목을 enum이라고 부르며, 항목의 집합을 Enumerations이라고 부르는 것을 알 수 있다.

각각의 enum은 값이므로 변수에 할당을 할 수 있다. Enumerations은 클래스와 같은 심볼이기 때문에 값으로 취급되지는 않으며 타입으로는 사용할 수 있다. 위 예제에서 'do_stuff'라는 함수는 열거형을 사용하여 Suit라는 타입을 갖고, `$s`라는 변수를 갖는다. 변수에는 값이 전달되어야 하므로 enum인 `Suit::Spades`, `Suit::Diamonds`, `Suit::Clubs`, `Suit::Spades`등을 전달할 수도 있다.

열거형은 클래스와 유사하며 내부에 멤버 변수, 멤버 메소드, 정적 멤버, 상수 등 클래스가 사용할 수 있는 다양한 기능을 사용할 수 있다.

## References
- https://www.php.net/manual/en/language.types.enumerations.php
