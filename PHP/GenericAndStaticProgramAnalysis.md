## 제네릭이란 무엇인가?

```
List<String> codes = new ArrayList<>();
codes.add("1"); // OK
codes.add(1); // compile error
```


```
public class Tiger extends Animal {
}
```
- Tiger 클래스는 Animal 클래스의 상속을 받는다.

```
Animal a = new Tiger()
```
- Tiger 객체는 Animal 타입의 변수 a에 할당할 수 있다.
- 상속한다는 것은 더 세분화하고 구체화하겠다는 의미이다.
- Animal 타입의 변수 a에 Tiger 타입을 할당할 수 있는 이유는 Tiger 타입이 Animal 타입이 가진 모든 속성을 가지고 있기 때문이다.
- 하위 타입은 상위 타입의 모든 속성을 가지고 있다는 것이 중요하다.

```
public class Cage<T> {
  private List<T> animals = ...;
  public void push (T animal) {
    this.animals.add(animal);
  }
  public List<T> getAll() {
    return animals;
  }
}
```

```
Cage<Animal> ca = new Cage<Tiger>();
```
- 위 코드는 에러가 난다.
- 하위 타입은 상위 클래스 타입이 가진 모든 속성을 가지고 있어야 하는데 상위 클래스 타입은 Animal 타입을 인자로 받고 하위 클래스 타입은 Tiger 타입을 인자로 받는다.
- 상위 클래스 타입이 가진 메소드는 Animal 타입의 인자를 처리하고, 하위 클래스 타입이 가진 메소드는 Tiger 타입의 인자를 처리한다. 상위 타입 클래스는 Animal 타입의 인자를 처리하는데 Tiger 타입의 인자를 처리할 수 있을까?

## PHP에서 제네릭 도입이 어려운 이유

## 정적분석 (제네릭에 대한 대안)

### PHPstorm의 정적 분석 지원
- 

### PHPstan을 통한 정적분석
- PHP stan은 PHP 생태계에서 인기 있는 정적 분석 도구이다.


## Reference
- https://wiki.php.net/rfc/generics
- https://blog.jetbrains.com/phpstorm/2021/07/phpstorm-2021-2-beta/
- https://youtu.be/PtM44sO-A6g
