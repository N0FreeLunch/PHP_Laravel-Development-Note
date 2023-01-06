## 참조란 무엇인가?
> References in PHP are a means to access the same variable content by different names. They are not like C pointers; for instance, you cannot perform pointer arithmetic using them, they are not actual memory addresses, and so on. See What References Are Not for more information. Instead, they are symbol table aliases. Note that in PHP, variable name and variable content are different, so the same content can have different names. The closest analogy is with Unix filenames and files - variable names are directory entries, while variable content is the file itself. References can be likened to hardlinking in Unix filesystem.

## php에서 참조의 정의
- 동일한 변수의 내용에 접근할 때 다른 이름으로 접근할 수 있는 것을 의미한다.

### C언어의 포인터와 다르다.
- C언어의 포인터는 포인터로 메모리의 주소를 지정하면 해당 메모리 위치에서 할당된 영역만큼의 공간을 갖는다.
- 컴퓨터의 저장공간은 연속적이라는 개념을 가지고 있다. 특정 메모리 위치에서 값을 할당할 수 있는 공간은 해당 위치로 부터 연속되어 있는 공간만을 할당할 수 있다.
- PHP와 같은 고수준의 언어의 경우 저수준 언어의 포인터와 다르게 메모리의 특정 위치를 가리키지는 않으며 메모리의 특정 위치로 부터 연속적인 공간을 할당받는 것은 아니다. 하나의 값이라도 PHP 엔진의 구현에 따라 메모리에 저장하는 방식은 다양하다.

## PHP에서 값은 상태변화를 공유하는 대상이다.
- PHP에서의 참조는 PHP라는 언어에서 하나의 값이라는 개념적인 대상이 PHP엔진에 의해 추상화 된 것이며, 이 추상적인 개념이 제공하는 인터페이스(조작방법)를 통해서 값의 상태를 변경하는 기능에 접근하는 것을 의미한다.
- 곧 PHP에서 참조라는 것은 PHP의 기본 값이 갖는 상태변화를 공유하는 개념적 대상에 접근하는 것이라 할 수 있다.

## PHP에서 참조란 값의 인터페이스에 접근하여 값의 상태를 컨트롤 하는 것이다.
- PHP에서 하나의 값이란 추상화된 개념으로 참조는 값이란 개념이 갖는 상태변화를 변화시키고 그 값을 가져올 수 있는 인터페이스에 접근하는 것을 의미한다.

## 값의 인터페이스의 예
### 배열
- `$변수['key']`라는 문법을 통해서 배열 타입의 값이 제공하는 인터페이스 중에서 `'key'` 프로퍼티의 값을 가져오라는 지시를 할 수 있다.
- `$변수['key'] = 값`라는 문법을 통해서 배열 타입의 값이 제공하는 인터페이스 중에서 `'key'` 프로퍼티의 값을 정의 또는 변경하는 지시를 할 수 있다.

### 클래스
- `클래스명::멤버`라는 문법을 통해서 클래스 타입의 값이 제공하는 인터페이스 중에서 `멤버` 프로퍼티의 값을 가져오라는 지시를 할 수 있다.
- `클래스명::멤버 = 값`라는 문법을 통해서 클래스 타입의 값이 제공하는 인터페이스 중에서 `멤버` 프로퍼티의 값을 정의 또는 변경하는 지시를 할 수 있다.

### 객체
- `$변수->멤버`라는 문법을 통해서 객체 타입의 값이 제공하는 인터페이스 중에서 `멤버` 프로퍼티의 값을 가져오라는 지시를 할 수 있다.
- `$변수->멤버 = 값`라는 문법을 통해서 객체 타입의 값이 제공하는 인터페이스 중에서 `멤버` 프로퍼티의 값을 정의 또는 변경하는 지시를 할 수 있다.

## Reference
- https://www.php.net/manual/en/language.references.whatare.php
