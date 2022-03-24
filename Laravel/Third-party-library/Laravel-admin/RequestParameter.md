## 라라벨 어드민에서 파라메터
- 라라벨 어드민에서 Form 태그에서 전달 된 파라메터는 일반적인 파라메터의 방식과는 조금 다르다.
- 일반적인 POST 통신의 파라메터 하나당 key : value 형식을 갖는 것에 반해 라라벨 어드민은 pjax를 사용하여 특정한 방식으로 key : value를 보낸다. 단순 key, value 뿐만 아니라 여러가지 부가적인 정보들을 섞어서 보낸다.
- 따라서 라라벨의 Request 객체에서 파라메터를 획득할 수 없는 문제가 있다. 그리고 라라벨 어드민 실행 컨텍스트 내에서 `Illuminate\Http\Request` 객체를 사용해서 값을 받아오지 못하는 문제도 있다.
- 라라벨 어드민에서 Form 태그를 정의하는 방식은 라라벨 어드민의 Form 객체를 사용하는 것이며, 라이브러리를 사용할 때 Form 객체에 Request 객체에서 받은 파라메터를 직접 집어넣지 않으며 라라벨 어드민 라이브러리가 알아서 전달 받은 파라메터를 처리한다. 그러므로 라라벨 어드민 라이브러리에서 Request에서 파라메터를 처리할 수 있는 기능을 지원하지 않는다면, 리퀘스트로 전달 받은 파라메터를 필터링 할 수 없는 문제가 있다.
- 하지만 라라벨 어드민은 리퀘스트 파라메터를 직접적으로 컨트롤 할 수 있는 기능을 제공하지는 않는다.

## 해야 할 것
- 사용할 파라메터만 서비스로직에 전달 될 수 있도록 하여 허가하지 않은 파라메터가 전달 되어 원치 않는 값의 변경이 일어나지 않도록 하는 것이 필요하다.
- 이를 위해서 리퀘스트 파라메터에서 원하는 값만 얻을 수 있도록 다음과 같은 방식을 사용한다.
- 하지만 라라벨 어드민을 사용하여 form을 구성할 경우 다음과 같은 방식을 사용할 수 없다.

### 화이트리스트 방식
```
$request->only(['param1', 'param2', 'param3']);
```
### 블랙리스트 방식
```
$request->except(['param4', 'param5', 'param6']);
```

## 폼 태그로 받은 파라메터를 무시하는 방법
- 라라벨 어드민이 전달받은 리퀘스트 파라메터에 대한 처리 기능을 제공하지는 않지만, 전달 받은 파라메터가 저장되지 않도록 하는 기능을 제공한다.
- 파라메터를 무시한다기 보다는 받은 파라메터를 저장되지 않도록 하는 역할을 한다.
- 다음 방법들은 블랙리스트 방식만 지원하고 화이트리스트 방식을 지원하지 않는 문제점이 있다.

### ignore 메소드를 이용하는 방법
```
$form->ignore('column1', 'column2', 'column3');
```
```
$form->submitted(function (Form $form) {
    $form->ignore('username');
 });
```

### 모델의 값 업데이트를 확인하여 업데이트를 방지하는 방법
```
// callback after form submission
$form->submitted(function (Form $form) {
    $modelChanges = $form->models()->getChanges();
    unset($modelChanges['param1'], $modelChanges['param2'], $modelChanges['param3'] ...);
});
```

```
// callback before save
$form->saving(function (Form $form) {
    $modelChanges = $form->models()->getChanges();
    unset($modelChanges['param1'], $modelChanges['param2'], $modelChanges['param3'] ...);
});
```

```
// callback after save
$form->saved(function (Form $form) {
    $modelChanges = $form->models()->getChanges();
    unset($modelChanges['param1'], $modelChanges['param2'], $modelChanges['param3'] ...);
});
```
