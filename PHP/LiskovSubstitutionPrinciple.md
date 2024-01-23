## 리프코프 치환 원리
```php
class ParentClass {
	function runParentMethod()
	{
		return "parent\n";
	}
}

class ChildClass extends ParentClass {
	function runChildMethod()
	{
		return "child\n";
	}
}

function testLiskovSubstitution(ParentClass $obj) {
	echo $obj->runParentMethod();
	if(method_exists($obj, 'runChildMethod')) {
		echo $obj->runChildMethod();
	}
}

testLiskovSubstitution(new ChildClass);
```
위 코드의 결과는
```
parent
child
```
이다.

### 리스코프 치환 원칙이란?
- OOP 언어에서 어떤 클래스 타입의 객체를 전달했다면 전달된 곳에서는 타입으로 지정한 클래스 가진 공개 멤버만을 사용해야 한다.
