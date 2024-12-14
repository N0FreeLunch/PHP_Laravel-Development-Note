## 정적 분석

### php rfc에서 나타난 정적 분석 툴에 대한 의견

#### [RFC: Deprecate dynamic properties](https://wiki.php.net/rfc/deprecate_dynamic_properties)
> A common counter-argument is that even if PHP itself does not detect the mistake, static analysis still can. While this is true to a degree, there are a number of problems:

> 1. Static analysis in IDEs (probably the most widespread type of static analysis used in PHP) has to be conservative about diagnostics relating to dynamic properties. For example, PhpStorm treats creation of dynamic properties as only a weak warning (non-intrusive grey underline), because it cannot distinguish whether this dynamic property assignment is indeed a bug, or an intentional use. Treating dynamic property creation as a more severe error would result in false positives in cases where dynamic properties are used intentionally. The #[AllowDynamicProperties] attribute proposed in this RFC makes the cases where dynamic properties are used intentionally explicit. This means that static analysis tooling can both
> a) suppress any diagnostic in explicitly allowed cases and
> b) report a hard error in all other cases, without producing any false positives.

> 2. Static analysis can only analyze assignments with a known object type and property name. This does not include any kind of dynamic assignments, such as those that may be performed by a serializer, hydrator or any other mechanism that directly populates objects without going through a constructor. Static analysis also requires a fully-typed codebase, as the actually assigned property may otherwise not be known.

> 3. On a more philosophical note, I believe that a programming language should be usable without external tooling. While IDE use is widespread among PHP programmers, it should still be possible to write PHP code in a text editor like vim without exposing you to silent bug classes.

- 위 RFC는 객체 속성의 동적 생성을 할 수 없도록 만들자는 내용이다. 이 RFC에 대한 반론으로 논의된 내용 중 하나는 정의하지 않은 속성에 접근할 때 정적 분석이 이를 알려 주기 때문에 php 언어 차원에서 이러한 기능을 굳이 제공하지 않아도 된다는 의견이다.
- 이러한 의견에 동적 속성을 php 언어에서 제거해야 한다고 생각하는 쪽의 입장은 언어 차원에서 동적 속성을 허용하면 동적 속성이 의도된 사용인지 아닌지 정적 분석이 알 수 없기 때문에 정적 분석 도구는 이게 실수 일 수도 있고 아닐 수도 있다는 모호한 경고를 제시하게 된다는 의견을 낸다. 의도적인 동적 속성 사용에 잘못 사용된 것일 수 있지 않을까라고 의견을 내는 것이 보기에 좋지 않다는 의견이다.
- 이러한 의견의 제시를 통해서 알 수 있는 것은, php 언어의 스팩은 언어의 사용에 대한 엄격한 스타일 가이드를 최소화 하고 이러한 언어 사용에 대한 스타일 가이드는 정적 분석 도구로 하면 된다는 의견들이 있는 것으로 보아 정적 분석 도구로 언어의 부족한 점을 보완한다는 것은 일반적인 방식인 것을 알 수 있다. 또한 가능한 정적 추론의 도구가 명확하게 판단할 수 있도록 언어를 좀 더 명확하게 만들고자 하는 의도가 있음을 알 수 있다.
- 물론 정적 분석과 같은 외부 도구 없이도 php의 코딩이 가능해야 하므로 동적 속성 사용에서 발생할 수 있는 실수를 줄이기 위해서 동적 속성을 제거하는 주장하는 것으로 보아 정적 분석을 사용하지 않는 코딩 스타일도 존중 되어야 하는 입장을 갖고 있다.
- 곧 정적 분석을 사용해서 언어가 할 수 없는 부족한 부분에 대한 보완을 하지만, 정적 분석을 사용하지 않았다고 해서 php에서 명시적으로 에러, 경고, 알림을 발생시키지 않는 코딩 스타일이 잘못된 것은 아니라는 것이다.

#### [RFC: Is_Trusted](https://wiki.php.net/rfc/is_trusted#static_analysis)
> Why not use static analysis?

> It will never be used by most developers.

> I still agree with Tyson Andre, you should use Static Analysis, but it's an extra step that most programmers cannot be bothered to do, especially those who are new to programming (its usage tends to be higher among those writing well-tested libraries).

> Also, these tools currently focus on other issues (type checking, basic logic flaws, code formatting, etc), rarely attempting to address Injection Vulnerabilities. Those that do are often incomplete, need sinks specified on all library methods (unlikely to happen), and are not enabled by default. For example, Psalm, even in its strictest errorLevel (1), and running --taint-analysis (rarely used), will not notice the missing quote marks in this SQL, and incorrectly assume it's safe:

- 위의 RFC는 통과되지 않은 RFC이지만, php 언어 스펙을 결정하는 투표를 할 수 있는 일원이 정적 분석에 관해 쓴 것이므로 참고 가능하다.
- 언어의 기능은 정적 분석을 사용할 수 없는 또는 사용하지 않는 정적 분석 툴을 다루는 수준이 아닌 사람들도 충분히 다룰 수 있어야 하므로 언어 차원에서 변수 안에 든 문자열이 신뢰할 수 있는 것인지 없는 것인지 판단할 수 있는 기능을 제공하는 것이 옳다고 주장하고 있다.

### 독선적인 코딩 스타일의 정적 분석

#### [Readonly properties should be assignable outside the constructor](https://github.com/phpstan/phpstan/issues/6562)

phpstan은 readonly 속성에 대해서 생성자에서만 초기화하도록 강제하고 있다. 하지만, php 언어의 사양으로는 생성자에서의 초기화 뿐만 아니라 setter를 통한 외부에서의 초기화도 허용 가능한 사양으로 되어 있다.

실제로 php의 readonly는 처음에 write-once의 속성으로 논의 되었다. [to allow the initialization of object properties after object construction. ... As soon as initialization is done, any other attempt to assign a value to “write-once” properties results in an exception.](https://wiki.php.net/rfc/write_once_properties)으로 객체 생성이후에 프로퍼티를 초기화 할 수 있도록 하며, 한 번 프로퍼티가 할당이 된 이후에는 더 이상 할당할 수 없게 만드는 목적을 가지고 만들어졌다. 그리고 이 속성에 대한 제안이 이뤄진 후 후에 readonly로 키워드명이 바뀌었다.

phpstan의 위의 issue의 내용을 보면 readonly 속성에 값을 할당하는 것은 생성자에서만 초기화 하지 않는 방식의 코딩 스타일을 생각할 수 있음에도 불구하고, phpstan의 작성자는 생성자를 사용한 초기화가 아닌 경우에는 정적 분석의 오류로 보고하는 것을 기본적으로 설정하는 것이 대체로 올바른 코딩스타일이라고 판단하고 있다. 이는 C#의 readonly가 생성자에서만 초기화 할 수 있는 속성이라는 특성에 기인하는 것으로 보인다. 하지만 동일한 이름의 키워드라도 php에서의 도입 의도와 C#의 도입 의도가 다르기 때문에 C#을 기준으로 판단하는 것은 적절하지 않다고 본다. 

php와 C#의 readonly의 차이에 대한 설명으로 php에서 xdebug를 만든 [Derick Rethans의 인터뷰](https://derickrethans.nl/phpinternalsnews-44.html)는 다음과 같이 말한다.
> Write Once Properties can only be initialised, but not modified afterwards. So you can either define a default value for them, or assign them a value, but you can't modify them later. So any other attempts to modify, unset, increment, or decrements them, would cause an exception to be thrown. Basically, this RFC would bring Java's final properties, or C#'s, read only properties to PHP. However, contrary how these languages work, this RFC would allow lazy initialization. It means that these properties don't necessarily have to be initialised until the object construction ends, so you can do that later in the object's life cycle.

객체가 생성된 이후 프로퍼티를 초기화 하는 것을 지연 초기화라고 하는데, php의 readonly는 프로퍼티의 지연 초기화를 지원하지만, C#의 readonly는 프로퍼티의 지연 초기화를 지원하지 않는다.

정적 분석 툴을 통해서 클래스를 객체로 사용할 때, 프로퍼티의 값을 할당하지 않았다면, 프로퍼티에 접근하는 코드를 사용할 수 없도록 만드는 것이 불가능하지는 않을텐데 이렇게 만들어 놓은 것은 올바른 readonly의 사용에 대한 논쟁을 초래할 수 있고 [issue](https://github.com/phpstan/phpstan/issues/6562)에서 주장하고 있다. 이에 대한 phpstan의 개발자는 만약 예외를 허용하려고 한다면, 주석을 통해 phpstan의 감지를 무시하거나, phpstan의 설정을 통해 오해당 오류 메시지를 무시하는 ignore 필터를 작성하라고 조언하며 정적 분석 툴의 공식적인 오류 보고의 제외 사항으로는 생각하고 있지 않은 것을 알 수 있다.

readonly의 지연 초기화를 정적 분석에서 에러로 표기하는 것이 문제가 되는 이유는 다양한 프레임워크와 라이브러리가 지연 초기화를 활용하고 있기 때문이며, 지연 초기화를 사용하는 것이 php 코딩에 있어 유리할 수 있기 때문이다. 객체 생성 이후에 프로퍼티를 할당하고 readonly 속성을 통해 할당한 프로퍼티를 바꿀 수 없도록 하여, 이미 할당이 되었은데, 이를 감지하지 못하고 다시 할당하는 실수를 방지할 수 있기 때문에 유용하다.

php에는 다양한 코딩 스타일이 존재할 수 있지만, 정적 분석은 php의 다양한 코딩 스타일의 일부를 제약하는 역할을 하기도 한다. 그리고 해당 제약이 대체로 옳다고 생각되는 방향일 수는 있지만 절대적으로 옳은 방향은 아닐 수도 있다.

phpstan에서 readonly 관련 에러 메시지가 나오지 않게 하려면 `phpstan.neon` 파일에서 `ignoreErrors:` 부분에 다음과 같은 코드를 추가하면 된다.
```
    ignoreErrors:
        - '#^Class [a-zA-Z0-9\\_]+ has an uninitialized readonly property \$[a-zA-Z0-9_.]+ Assign it in the constructor.$#'
```

#### 런타임에서만 알 수 있는 지식을 알지못한다.

- https://phpstan.org/writing-php-code/solving-undefined-variables
```php
function getUserStatusColor(User $user): srting
{
    if($user->isDelete()) {
        $statusColor = 'red';
    } elseif ($user->isActive()) {
        $statusColor = 'blue';
    }
    return $statusColor;
}
```
- 예를 들어서 위와 같은 코드가 있다고 하자. 유저 객체의 isDelete 메소드와 isActive 메소드는 서로 베타적이라고 하자. isDelete isActive 이외의 케이스는 존재하지 않음에도 불구하고 정적 분석 도구는 위의 코드에서 `$statusColor`가 정의되지 않은 경우가 발생할 수 없다는 것을 알 수 없다. 그래서 `might not be defined`라는 에러를 보고할 것이다.
```php
function getUserStatusColor(User $user): srting
{
    if($user->isDelete()) {
        $statusColor = 'red';
    } elseif ($user->isActive()) {
        $statusColor = 'blue';
    } else {
        assert(false, 'isDelete, isActive 이외는 존재할 수 없는 케이스입니다.');
    }
    return $statusColor;
}
```
- 위와 같은 코드도 정적 분석으로 감지 할 수 없으며 다음과 같은 코드를 생각해 볼 수 있지만, 반환값이 정의되지 않은 경우가 있을 수 있기 때문에 에러가 발생할 수도 있을 것이다.
```php
function getUserStatusColor(User $user): srting
{
    if($user->isDelete()) {
        return 'red';
    } elseif ($user->isActive()) {
        return 'blue';
    } else {
        assert(false, 'isDelete, isActive 이외는 존재할 수 없는 케이스입니다.');
    }
}
```
- php는 기본적으로 런타임에 동작하는 언어라서 위와 같은 코드는 합법적인 코딩 스타일이다. 하지만, 런타임의 실행을 감지할 수 없는 정적 분석은 위와 같은 코드들이 불법에 해당한다.

### 정적 분석의 코드 스타일에 맞춰야 하는가?
- 정적 분석 도구의 장점은 직접 실행하지 않고도 버그 또는 에러가 발생할 수 있어 보이는 코드를 탐지한다는 것이다. 이런 탐지를 위해서 php 코딩으로 가능한 표현 방식의 일부를 제한한다. 대체로 정적 분석이 알려주는 코딩 스타일을 지키는 것은 좋지만, 프레임워크, 아키텍처, 각 프로젝트의 코딩 스타일의 제약사항으로 인해 항상 정적 분석이 제시하는 방식을 따를 수 있는 것은 아니다.
