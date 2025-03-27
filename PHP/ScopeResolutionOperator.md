# 범위 결정 연산자

> The Scope Resolution Operator (also called Paamayim Nekudotayim) or in simpler terms, the double colon, is a token that allows access to a constant, static property, or static method of a class or one of its parents. Moreover, static properties or methods can be overriden via late static binding.

범위 결정 연산자는 php에서 `::` 구문을 사용한다. 범위 결정 연산자로 엑세스 가능한 것은 특정 클래스 또는 부모 클래스에 대해 (const 키워드로 클래스의 멤버를 정의하는) 상수, (클래스의 멤버를 static 키워드로 정의하는) 정적 멤버 프로퍼티 및 정적 메소드, 늦은 정적 바인딩을 통해 오버라이딩 된 정적 프로퍼티 및 메소드에 접근 가능하다.

## References
- https://www.php.net/manual/en/language.oop5.paamayim-nekudotayim.php
