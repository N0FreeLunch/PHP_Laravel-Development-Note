## 제네릭이란 무엇인가?

## 자바를 사용한 제네릭 사용에 대한 설명
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


### 제네릭 사용하기
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
- `Cage<T>`에서 `<T>`는 T로 지정한 타입을 받을 수 있는 클래스를 만들겠다는 의미이다. 
- `animals` 이란 변수는 `List<T>` List 타입으로 `<T>` 타입을 사용하는 객체이다.
- `push (T animal)`은 push라는 메소드는 T 타입의 매개변수 animal을 가진다는 의미이다.
- `this.animals.add(animal);`은 `List<T>` T 타입을 받는 List 타입의 객체인 animals에 타입이 T인 animal을 넣은 것이다.
- `List<T> getAll()` List 타입의 객체를 반환하는 메소드인데 이 때 List 타입은 내부에 T 타입을 사용하는 List 타입의 객체를 반환한다는 뜻이다.


#### 참고
- 하위 타입은 상위 클래스 타입이 가진 모든 속성을 가지고 있어야 하는데 상위 클래스 타입의 메소드는 Animal 타입을 인자로 받고 하위 클래스 타입의 메소드는 Tiger 타입을 인자로 받는다.
- 상위 클래스 타입이 가진 메소드는 Animal 타입의 인자를 처리하고, 하위 클래스 타입이 가진 메소드는 Tiger 타입의 인자를 처리한다. 
- 상위 타입 클래스의 메소드는 Animal 타입의 인자를 처리하는데 Tiger 타입의 인자를 처리할 수 있을까? 
- 상위 타입 클래스의 메소드는 Animal이 가진 모든 속성을 처리한다. Tiger에도 Animal이 가진 모든 속성이 있기 때문에 상위 타입 클래스의 메소드가 Tiger 타입을 처리할 수 있다.

```
Cage<Animal> ca = new Cage<Tiger>();
```
- `Cage<Animal> ca` 객체 내부에 Animal 타입을 전달하는 Cage 타입 클래스인 ca에 `new Cage<Tiger>()` 객체 내부에 Tiger 타입을 전달하는 Cage 타입 클래스를 인스턴스화 한 대상을 대입하였다.
- 클래스 내부로 전달하는 타입에서 상위 타입 Animal을 전달하는 ca에 하위 타입 Tiger를 클래스 내부로 전달하는 객체를 집어 넣는 것이 문제가 되는데 왜 그런지 알아보자.
 
```
Cage<Tiger> ct = new Cage<Tiger>();
Cage<Animal> ca = ct; // 이게 되면
ca.push(new Lion()); // Lion은 Animal이므로 가능
List<Tiger> tigers = ct.getAll(); // Lion 리스트 리턴
```
- 만약 `Cage<Animal> ca = ct` 가 된다면 `ca`가 객체 내부로 전달하는 타입은 Animal 타입이므로 Animal 타입의 하위 타입인 Lion을 `ca.push(new Lion());`방식으로 넣을 수 있다.
- `ca`는 `Lion` 타입을 클래스 내로 전달하여 `getAll()` 메소드로 반환되는 Lion 타입을 List 객체 내부로 전달한 List 객체를 반환한다.
- 그런데 이를 `List<Tiger> tigers`라는 객체 내부에 Tiger 타입을 전달하는 List 타입 변수인 tigers에 대입을 한다고 하면 내부로 전달하는 타입이 같거나 상위 하위 타입의 관계가 아니고 서로 다른 종류의 타입이기 때문에 에러가 발생한다.

### 무변성 (invariant)
- A가 B의 상위 타입일 때
- GenericType<A>가 GenericType<B>의 상위 타입이 아니면 변성이 없다고 해서 무변성이라 한다.
```
Animal animal = new Tiger();
Cage<Animal> ca = new Cage<Tiger>();
```
- Animal은 Tiger의 상위 타입 
- `Cage<Animal>`는 `Cage<Tiger>`의 상위 타입이 아니다.
- 위 두 조건을 만족할 때가 무변성(무공변)



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
- https://edykim.com/ko/post/php-generics-with-phpstan/
