# Iterator

## 배열의 문제

PHP의 foreach와 같은 문법은 배열을 순회할 수 있는 기능을 제공한다. php에서 배열은 array의 형태를 docblock generic이나 array shape로 적어주지 않는 한, 배열 안에 든 원소가 무엇인지 정적 분석툴로 추론할 수 없다는 단점을 가진 문법이다.

배열의 타입 추론이 docblock에 의존하는 문제 때문에, 보일러 플레이트가 좀 더 있더라도, DTO 클래스를 사용하거나, `Iterator` 클래스를 사용하는 문법 등을 활용하는 방법이 추천되기도 한다. 하지만 이 방식은 지나치게 장황할 수 있어서 타입 추론이 잘 되지만, 많은 개발자들에게 광범위하게 채택되지 않는 문제점이 존재한다.

## Iterator 인터페이스 알아보기

```php
interface Iterator extends Traversable {
    /* Methods */
    public current(): mixed
    public key(): mixed
    public next(): void
    public rewind(): void
    public valid(): bool
}
```
