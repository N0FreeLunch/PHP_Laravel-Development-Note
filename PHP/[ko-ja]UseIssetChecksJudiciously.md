## isset을 사용하는 올바른 방법
## issetだらけのコードをやめてください。

### 도입
### はじめに
- 상당히 많은 php 코드에서 `isset`을 남용하고 있다고 생각한다. 어떤 경우에 `isset`을 사용하고, `isset`을 왜 함부로 사용하면 안 되는지 그 이유를 알아보도록 하자.
- 非常に多くのphpコードで、`isset`を乱用していると思います。どのケースで`isset`を使用すべきか、`isset`をなぜむやみに使用してはならないのか、その理由を理解しましょう。
- 적절하지 않게 `isset`을 사용한 코드를 볼 때 마다 불편한 느낌이 있었고 그 이유를 명확히 밝혀 `isset`을 남발하는 코드의 사용을 최소화하기 위한 목적으로 작성하였다.
- `isset`が不適切に使用されたコードを見るたびに好ましくない感じがあり、その理由を明確にして`isset`を乱発するコードの使用を最小化するためにこの記事を作成しました。

### 좋은 코드란 무엇인가?
### 良いコードは何か？
- 좋은 코드란 해석의 다양성을 줄이는 것이다. 해석의 가능성을 줄이는 것은 코드를 빠르고 명확하게 이해할 수 있도록 한다.
- 良いコードとは、解釈の幅を狭めることです。解釈の可能性を減らすことで、コードを迅速かつ明確に理解できます。
- 예를 들어 php에서 `const CONSTANT_VALUE = '불변값'`으로 선언된 값을 사용하면 이 값은 고정된 값이고 변경할 수 없는 값이란 것을 알 수 있다. 변수와 달리 상수는 고정된 값으로 이 값이 처음 선언된 부분에서의 값과 다르다는 것을 생각할 필요가 없으므로 변수의 변경 과정을 추척하지 않아도 된다. 상수를 사용한 것은 값이 변경될 가능성을 줄이는 것으로 해석의 다양성을 줄이는 일이다.
- 例えば、phpで`const CONSTANT_VALUE='不変値'`と宣言された値を使用すると、この値は固定されており、変更できないことがわかります。変数とは異なり、定数は最初に宣言された時点の値と異なる可能性がないため、変数の変更過程を推測する必要はありません。定数を使用することは、値が変更される可能性の判断を減らすことで、解釈の多様性を低減させます。
- 빠르고 명확하게 이해할 수 있는 코드를 만들기 위해서는 프로그래밍 언어에서 지원하는 문법을 잘 이해하여 가능한 적은 양상을 가질 수 있도록 코드를 작성하는 것이 필요하다.
- 迅速かつ明確に理解できるコードを作成するためには、プログラミング言語でサポートされている文法をよく理解し、できるだけ少ない量像を持つようにコードを作成する必要があります。

### isset 문법의 특징
### isset関数
- php에서 `isset`은 대상의 값이 정의되지 않은 경우 또는 값이 `null`인 경우를 판별하는 함수이다.
- `isset`関数は、phpにおいて対象の値が定義されていない場合または値が`null`である場合をを判別する関数です。
- 값이 정의되지 않거나, `null`인 경우에 `isset(대상)`은 `true`를 반환하고 그렇지 않으면 `false`를 반환한다.
- 値が定義されていない場合や`null`の場合、`isset(対象)`は`true`を返します。それとも`false`を返します。

### 정의되지 않은 변수를 확인하는 기능
### 定義されていない変数を確認する機能
- php에서 값이 정의되었는지 정의되지 않았는지 확인하는 기능으로는 `isset`, `??`, `??=` 문법이 존재한다. php에서 많은 기능과 함수가 존재하지만 대상이 정의된 값인지 정의되지 않은 값인지 확인하는 문법은 이들 3가지로 제한적이다.
- phpで値が定義されているか未定義かを確認する手段は、`isset`、`??`、`??=`の機能があります。phpで多くの機能と関数が存在しますが、対象が定義された値なのか未定義なのかを確認する文法はこれら3つに制限されます。
- 따라서 이들 정의되지 않은 대상을 파악하는 문법은 단순히 `null` 확인용으로 쓰는 것 보다는 값의 정의 유무를 판별할 때 사용하는 것으로 하고, 값의 `null` 여부를 판단하는 것은 `is_null(대상)`이나 `대상 === null`의 문법을 사용하는 것이 좋다.
- そのため、これらの定義されていないのかを確認する機能を、単に`null`確認用で使うのではなく、値の定義・未定義を判別するために使うことにして、値が`null`なのかを判断する際には`is_null(対象)`や`対象===null`の文法を使うことをお勧めします。
- 이런 코딩 스타일을 갖게 되면, `isset`을 사용했을 때는 변수의 선언을 확인하는 용도로 바라보게 되고, `is_null`이나 `=== null`을 사용했을 때는 변수의 타입이 `null`인지 확인하는 용도로 바라보게 되어 정의되지 않은 값인지 `null` 값인지 명확하게 이해할 수 있는 로직을 짤 수 있다.
- このようなコーディングスタイルを持つと、`isset`を使用した場合は変数の宣言を確認する用途として考えるようになり、`is_null`や`===null`を使用した場合は変数のタイプが`null`なのかを確認する用途として考えるようになり、定義されていない値なのかを明確に理解できるロジックを組むことができます。

#### `??`, `??=`에 대해 알아보기
#### `??`, `??=`に関して説明
- `??` : `변수 = 대상 ?? 기본값`의 문법은 `변수 = (fn() => isset(대상) ? 대상 : 기본값)();`의 문법과 동일하다. [Null coalescing operator](https://www.php.net/manual/en/language.operators.comparison.php#language.operators.comparison.coalesce)으로 불린다.
- `??` : `変数 = 対象 ?? デフォルト値`の文法は、`変数 = (fn()=>isset(対象) ? 対象 : デフォルト値)();`の文法と同じです。[Null coalescing operator](https://www.php.net/manual/en/language.operators.comparison.php#language.operators.comparison.coalesce)と呼ばれています。
- `??=` : `대상 ??= 기본값`의 문법은 `if(!isset(대상)) { 대상 = 기본값; }`의 문법과 동일하다. [Null coalescing assignment operator](https://www.php.net/manual/en/migration74.new-features.php#migration74.new-features.core.null-coalescing-assignment-operator)으로 불린다.
- `??=` : `対象 ??= デフォルト値`の文法は、`if(!isset(対象)) { 対象 = デフォルト値; }`の文法と同じです。 [Null coalescing assignment operator](https://www.php.net/manual/en/migration74.new-features.php#migration74.new-features.core.null-coalescing-assignment-operator)と呼ばれています。


### 정의된 변수와 정의되지 않은 변수의 구분의 중요성
### 定義された変数と定義されていない変数の区別の重要性
- 정의되지 않은 대상은 php에서 값 자체가 없는 것으로 대부분의 php 함수나 문법은 값의 존재를 상정하고 동작을 수행하기 때문에 `isset`, `??`, `??=` 이외의 코드에서 에러가 발생하는 경우가 대부분이다.
- 未定義の変数は、phpで値が設定されていないものであり、ほとんどのPHP関数や文法は値の存在を前提として動作するため、`isset`, `??`, `??=`以外のコードでエラーが発生する場合がほとんどです。
- 변수를 정의하지 않았기 때문에 php 코드의 많은 부분에서 이를 사용하기 위해서는 `isset`으로 변수의 존재 유무를 확인하고 코드를 짜야 한다.
- 変数を定義していないため、phpコードの多くの箇所でこれを使用するには、`isset`を使用して変数の存在を確認し、コードを構築する必要があります。
- 어떤 로직을 만들 때 로직의 흐름을 전개하는 것과 상관없는 문법적인 요소들이 서로 섞이는 것은 좋지 않다. 로직을 전개하기 전에 로직의 흐름과 관계 없는 것을 미리 처리하고 로직의 흐름만을 기술하는 편이 좋다.
- ロジックを作成する際、ロジックの流れとは関係のない文法的な要素が混ざるのは好ましくありません。ロジックを展開する前に、ロジックの流れに関係ない部分を事前に処理し、ロジックの流れだけを記述する方が良いでしょう。
- 변수를 미리 정하지 않고 코드를 짜게 되면 중간 중간 `isset`이 들어가게 되면서 깔끔하지 못한 코드를 만들게 된다. 따라서 `isset`과 같은 정의되지 않은 변수를 확인하는 코드를 사용할 때는 다음과 같은 순서로 코드가 나열되는 형태를 갖는 것이 좋다.
- 変数を事前に定義せずにコードを組むと、途中で`isset`が入って、きれいではないコードが作られます。そのため、`isset`などの未定義の変数を確認するコードを使用する場合は、コードが以下のような順序で記述されることをお勧めします。
```php
<php?

// 1. 변수를 선언할 때 변수의 기본 값을 지정한다. 변수가 여러 조건에 따라 값이 달라진다면 메소드나 함수 등의 반환 값으로 변수 값을 받도록 하자.
// ↓
// 2. 혹시 변수에 값이 정의되지 않은 경우 'isset', '??', '??=' 의 문법을 사용하여 변수 값이 정해지지 않은 부분이 없는지 확인하고 디폴트 값을 할당한다.
// ↓
// 3. 로직을 전개하는데 필요한 변수들이 다 모인 이후, 로직의 흐름을 나타내는 코드를 작성하도록 하자.
```
- 만약 어떤 흐름의 로직의 결과에 따라 새로운 변수를 사용해서 그 다음 로직에 사용해야 하는 경우 다음과 같은 방식을 사용할 수 있다.

```php
<php?

// 1-1. 변수를 선언할 때 변수의 기본 값을 지정한다. 변수가 여러 조건에 따라 값이 달라진다면 메소드나 함수 등의 반환 값으로 변수 값을 받도록 하자.
// ↓
// 1-2. 혹시 변수에 값이 정의되지 않은 경우 'isset', '??', '??=' 의 문법을 사용하여 변수 값이 정해지지 않은 부분이 없는지 확인하고 디폴트 값을 할당한다.
// ↓
// 1-3. 로직을 전개하는데 필요한 변수들이 다 모인 이후, 로직의 흐름을 나타내는 코드를 작성하도록 하자.
// ↓
// 2-1. 1단계의 로직의 결과에 따라 새로운 변수가 사용되는 경우 중간에 변수를 추가한다. 가능한 이 부분에서 정의되지 않은 변수가 생기지 않도록 한다.
// ↓
// 2-2. 2단계의 로직을 전개하는데 필요한 변수를 추가한 이후, 2단계 로직의 흐름을 나타내는 코드를 작성하도록 하자.
// ↓
// 3. 2단계에서 로직이 끝나지 않고 새로운 변수가 추가되는 경우 3은 2단계의 반복이다.
// ↓
// 4. 3단계에서 로직이 끝나지 않고 새로운 변수가 추가되는 경우 4은 3단계의 반복이다.
// ↓
// ...
```

### 데이터 전달 프로토콜에 대한 이해 결여
### データ伝達プロトコルに対する理解不足
- 여기서 데이터 전달 프로토콜이라고 하는 것은 어떤 핵심 로직을 전개하기에 앞서 미리 필요한 변수를 정의하는 것을 의미한다. 어떤 핵심 로직을 전개하기 위해 필요한 변수를 미리 만들어 둔 변수의 집합을 프로토콜이라고 하고 이를 핵심 로직에 전달하는 것을 데이터 전달이라고 하자.
- ここでデータ伝達プロトコルとは、あるコアロジックを展開する前に、必要な変数を事前に定義することを意味します。ある核心ロジックを展開するために必要な変数をあらかじめ用意しておいた変数の集合をプロトコルにして、これを核心ロジックに伝達することをデータ伝達とします。
- `isset`으로 로직을 짜면 값의 존재와 비존재를 확인하기 편리하다며 `isset` 위주로 사용하는 경우가 많다. 메인테넌스 할 때 값이 전달되는 프로토콜에 대한 이해 없이 그냥 `isset`을 사용해서 값이 있을 땐 처리하고 값이 없을 때 처리하지 않으면 되기 때문에 별다른 고민없이 코드를 짤 수 있기 때문이다.
- `isset`でロジックを組むと、値の存在と非存在を確認しやすいとして`isset`を中心に使用する場合が多いです。メンテナンスする時値の伝達プロトコルに対する理解をしなく`isset`を使用して値がある時に処理し、値がない時に処理しないことで、特に悩みなくコードを組むことができるから`isset`を使用します。
- 하지만 변수의 값을 체크하고 이후 사용하기 위해 값을 할당하기 위한 용도가 아닌, 필요한 값이 전달되지 않았기 때문에 에러가 나서 `isset`으로 로직을 실행을 피하기 위한 방식으로 코드를 짜게되면 코드의 실행 과정에서 값의 전달이 명확한 프로토콜에 의해 일어나는 것이 아닌 중구난방으로 사용되는 코드를 짜게 되어 잘 정의된 로직을 만들기 보다 그냥 돌아가는 로직을 만들 가능성이 높아진다.
- しかし、変数の値をチェックし、その後ロジックで使用するために値を割り当てるための用途ではなく、必要な値が伝わらないからエラーが発生し、`isset`でコードの実行を回避するためにコードを組むと、コードの実行過程で値の伝達が明確なプロトコルによって行われるのではなく、ばらばらにコードが使用されることになり、よく定義されたロジックを構築するよりも、単に動作するコードを作成する可能性が高くなります。
- 그저 동작하는 코드를 만들면 코드에 복잡한 조건이 추가될 때마다 더욱 메인테넌스하기 어려운 로직을 만들게 되고, 전체적인 이해없이 코드를 개선하는 경험이 쌓이다 보니 곰돌이 무릅을 고무 망치로 쳤을 때 반대쪽 발이나 손이 올라가는 짤에 좋아요를 누르게 될 가능성이 높다.
- 単に動作するコードを作成すると、コードに複雑な条件が追加されるたびに、よりメンテナンスしにくいロジックを作り、全体的な理解なしにコードを改善する経験が積まれるため、熊の膝をゴムハンマーで叩いた時、反対側の足や手が上がる絞りに「いいね」を押す可能性が高いです。

### 정의되지 않은 변수 사용시 에러를 나게 처리해야 하는 이유
- 코드를 작성할 때 에러를 처리하는 한 가지 방법은 기대하지 않았던 동작이 일어날 때 에러를 발생시켜서 에러가 발생한 로직을 보완하는 방식이다.
- 미리 필요한 변수의 값을 모두 모은 다음 이들로 로직을 짜게 되면 필요한 변수가 전달되지 않은 경우 에러가 발생하게 되고, 프로그래머는 에러를 확인하고 정의되지 않은 데이터가 왜 전달되었는지 데이터의 전달 프로토콜의 형태를 고민하는 과정을 통해 코드의 흐름을 좀 더 이해할 수 있게 된다. 이를 통해 명확한 데이터 전달의 흐름을 이해할 수 있어서 메인테넌스하기 쉬운 코드를 만들 수 있다.
- 특정 조건에서 정의되지 않은 변수가 전달되기 때문에 어떤 조건에서 변수가 정의되지 않았는지에 대한 고민 없이 `isset`으로 정의되지 않은 변수가 전달 될 때 로직을 실행하지 않는 방식으로 처리하면 더 이상 에러가 발생하지 않기 때문에 데이터 전달 프로토콜에 대한 고민의 기회를 잃어버리므로 이런식의 해결을 추천하지 않는다.

### 비어 있는 값
- 빈 값을 표현하기 위해서 일부러 변수를 정의하지 않는 경우가 있을지도 모른다. 변수가 정의되지 않으면 빈 값이고, 변수가 정의되면 사용하면 되는 값이 되기 때문이다. 하지만 이러한 존재 비존재의 상태를 표현하는데 굳이 `isset`을 사용할 필요가 없으며 `isset`을 대체할 수 있는 php 표현은 상당히 많다.
- 많은 경우 로직의 전개 과정에서 여러 타입에 대한 처리 보다는 하나의 타입에 대한 처리를 하는 기능이 많다. 또한 변수가 갖는 타입이 많아질수록 변수를 사용할 때 로직의 분기가 많아진다. 그래서 변수는 가능한 적은 종류의 타입을 갖는 것을 추천한다.
- 하나의 타입만을 사용하더라도 할당 비할당의 상태를 표현할 수 있는 경우가 있는데 디폴트 값으로 빈 값을 이용하는 방법이다. 보통 문자열의 경우에는 빈 문자열 `""`과 비지 않은 문자열으로, 수의 경우에는 `0`과 `0`이 아닌 수로, 배열은 빈 배열`[]`과 비지 않은 배열 `[1,2,3]`으로 하나의 타입에서 빈 값과 비지 않은 값을 구분하는 방식을 사용하고 디폴트 값으로 빈 값을 할당하는 방식을 사용한다. php의 `empty`란 내장함수는 동일한 타입에서 빈 값을 체크해 주는 함수이고 이를 이용하여 `isset`을 대체할 수 있다.
- 물론 할당된 값이 빈 값에 해당하는 값이어서 빈 값의 여부만으로는 디폴트 값인지 할당된 값인지 구분하지 못할 경우가 있다. 이 경우는 타입을 추가해서 `null`과 함께 사용한다. 또한 오브젝트 같은 경우는 빈 값을 정의하기 까다롭기 때문에 비어 있는지를 나타내기 위해서 `null` 타입을 추가해서 함께 사용할 수 있다.
- 로직을 전개하기 위해 필요한 모든 변수를 미리 정의하고 난 이후에는 해당 값에 어떤 타입의 값이 들어가 있는지 확인할 수 있는 기능은 많다.
- is_​array, is_​bool, is_​callable, is_​countable, is_​double, is_​float, is_​int, is_​integer, is_​iterable, is_​long, is_​null, is_​numeric, is_​object, is_​real, is_​resource, is_​scalar, is_​string
- 이들 키워드는 `null` 타입과 병용하여 값이 준비 되었는지 준비되지 않았는지 확인할 수 있게 해 주며, 중간중간 변수의 타입을 알 수 있게 해 주기 때문에 타입 안정적인 코드를 짜는데 도움을 주기도 한다.
- 어떤 변수의 디폴트 값을 먼저 선언하고자 한다면 각 타입의 빈 값 또는 `null`타입을 사용한 코드를 만들도록 하자.

### 예제 코드
```php
class Message
{
    private readonly string $message;
    private readonly string $name;

    public function setMessage(string $message): self
    {
        $this->message = $message;
        return $this;
    }

    public function setName(string $name): self
    {
        $this->name = $name;
        return $this;
    }

    public function getMessage(): string
    {
        $this->fillUndefinedMember();
        $objectPhrase = empty($this->name) ? $this->name : " to {$this->name}";
        return "Jimmy said{$objectPhrase}, {$this->message}";
    }

    private function fillUndefinedMember(): void
    {
        $this->name ??= "";
    }
}

echo (new Message)->setMessage("Nice to Meet You")->getMessage();
echo PHP_EOL;
echo (new Message)->setName("David")->setMessage("Nice to Meet You")->getMessage();

```
- `setName` 메소드와 `setMessage` 메소드를 사용하여 `undefined`인 멤버에 값을 정의해 주었다.
- `setName` 메소드를 사용하지 않은 경우, `fillUndefinedMember` 메소드에 의해 빈 문자열이 `$name`에 할당된다. 따라서 `setName`는 옵션인 메소드이다.
- `setMessage` 메소드를 사용하지 않은 경우, `fillUndefinedMember`에 의해 디폴트 값이 할당되지 않으므로 `$message`멤버를 사용할 때 에러가 발생한다. 이는 일부러 에러를 발생 시킨 것으로 `setMessage`는 필수 메소드이다.
- `private string $name = "";`으로 처음 멤버 변수를 선언할 때 디폴트 값을 할당해도 되지만, `readonly`를 사용하는 경우에는 변수를 한 번만 할당할 수 있으므로 `setter`로 값을 받는 경우에는 디폴트 값을 멤버 선언과 동시에 할당하지 않았다.
- 이름이 전달되지 않는 경우는 빈 문자열이므로 `empty($this->name)`로 이름이 할당되었는지 되지 않았는지를 `empty` 함수로 확인하였다.
- 위 과정은 `setter` 메소드를 통해 1에 해당하는 '변수의 기본 값 지정' -> `fillUndefinedMember` 메소드를 사용하여 2에 해당하는 '정의되지 않은 경우 디폴트 값 지정' -> `getMessage`의 리턴값에서 '로직을 전개하는데 필요한 변수들이 다 모인 이후, 로직의 흐름을 나타내는 코드를 실행'의 단계로 나눠져 있다.

### 결론
- `isset`을 `null`을 확인하는데 쓰는 것이 아니라, 정의되지 않은 값을 확인하는 것에만 쓰도록 하자.
- `isset`을 사용할 때는 정의되지 않은 값을 확인하고 로직을 실행하지 않는 방식으로 사용하는 것이 아니라, 정의되지 않은 변수에 빠짐 없이 값이 할당되도록 확인 및 기본값을 설정하는 용도로 사용하자.
- 이렇게 `isset`을 사용하는 스타일을 갖게 되면, `isset`을 `null`을 확인하는 용도로 쓴 것인지 해석되는 경우의 수를 줄여서 코드를 해석할 수 있어서 좀 더 빠르고 명확하게 이해할 수 있는 코드를 만들 수 있다.