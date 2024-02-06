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
- phpstan은 readonly 속성에 대해서 생성자에서만 초기화하도록 강제하고 있다. 하지만, php 언어의 사양으로는 생성자에서의 초기화 뿐만 아니라 setter를 통한 외부에서의 초기화도 허용 가능한 사양으로 되어 있다.
- 예를 들어 프레임워크에 의한 생성자 주입을 하게 되면, 생성자에 대한 권한을 프레임워크에 빼앗기게 되어 객체 내부의 readonly 속성을 생성자에서 초기화할 수 없는 문제가 있을 수 있다. 이를 위해서 setter를 사용한 readonly 속성 초기화를 사용하는 경우가 있을 수 있다.
- 위 issue의 내용을 보면 readonly 속성에 값을 할당하는 것은 생성자에서만 초기화 하지 않는 다양한 코딩 방식을 생각할 수 있음에도 불구하고, phpstan의 작성자는 생성자를 사용한 초기화가 아닌 경우에는 정적 분석의 오류로 보고하는 것을 기본적으로 설정하는 것이 대체로 올바른 코딩스타일이라고 판단하고 있다. 만약 예외를 허용하려고 한다면, 주석을 통해 phpstan의 감지를 무시하거나, phpstan의 설정을 통해 오해당 오류 메시지를 무시하는 ignore 필터를 작성하라고 조언하며 정적 분석 툴의 공식적인 오류 보고의 제외 사항으로는 생각하고 있지 않은 것을 알 수 있다.
- php에는 다양한 코딩 스타일이 존재할 수 있지만, 정적 분석은 php의 다양한 코딩 스타일을 일부 제약하는 역할을 하기도 한다. 그리고 해당 제약이 대체로 옳다고 생각되는 방향일 수는 있지만 절대적으로 옳은 방향이 아닐 수도 있다.
