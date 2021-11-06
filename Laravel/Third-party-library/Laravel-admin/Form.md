# Form
- 라라벨 어드민의 Form 클래스를 사용한다.
```
use Encore\Admin\Form;
```

## Form 메소드의 기능
- 일반적으로 라라벨의 메소드 edit에 해당하는 부분이다.
- 라라벨의 라우터에서 접근을 할 때 resource 메소드를 사용한다. 이는 컨트롤러의 엑션에 접근할 때 edit 메소드에 해당하는 것이다.
- form 메소드는 접근제한자가 protected이다. edit 메소드로 접근을 하면 form 메소드로 설정한 화면이 나오도록 설정되어 있는 것은 라라벨 어드민의 컨트롤러(AdminController) 상속을 통해 이뤄진다.

```
protected function form()
{
    $form = new Form(new Movie);

    // Displays the record id
    $form->display('id', 'ID');

    // Add an input box of type text
    $form->text('title', 'Movie title');

    $directors = [
        1 => 'John',
        2 => 'Smith',
        3 => 'Kate',
    ];

    $form->select('director', 'Director')->options($directors);

    // Add textarea for the describe field
    $form->textarea('describe', 'Describe');

    // Number input
    $form->number('rate', 'Rate');

    // Add a switch field
    $form->switch('released', 'Released');

    // Add a date and time selection box
    $form->datetime('release_at', 'release time');

    // Display two time column
    $form->display('created_at', 'Created time');
    $form->display('updated_at', 'Updated time');

    return $form;
}
```

#### display
```
$form->display('id', 'ID');
```
- 첫 번째 인자 : 모델의 컬럼
- 두 번째 인자 : 표시되는 항목의 타이틀
- 생성화면에서는 나타나지 않고 수정화면에서는 표시되도록 되어 있다.

#### text
```
$form->text('title', 'Movie title');
```
- 첫 번째 인자 : 모델의 컬럼
- 두 번째 인자 : 표시되는 항목의 타이틀
- 생성화면에서는 텍스트를 입력할 수 있는 빈 폼, 수정화면에서는 텍스트를 입력할 수 있는 레코드 데이터가 들어 있는 폼이 생성되어 표시된다.

#### select
```
$directors = [
    1 => 'John',
    2 => 'Smith',
    3 => 'Kate',
];

$form->select('director', 'Director')->options($directors);
```
- select의 첫 번째 인자 : 모델의 컬럼
- select의 두 번째 인자 : 표시 되는 항목의 타이틀
- options의 첫 번째 인자 : select 테그의 리스트로 표시 될 항목이 연관 배열의 value이고, form의 엑션으로 전송될 데이터가 연관 배열의 key이다.

#### textarea
```
$form->textarea('describe', 'Describe');
```
- 첫 번째 인자 : 모델의 컬럼
- 두 번째 인자 : 표시되는 항목의 타이틀
- textarea 태그처럼 폼의 크기를 자유롭게 늘릴 수 있다.
- 폼의 세로 길이 보다 내용의 세로 길이가 길어지면 스크롤이 생기는 방식이다.

#### number
```
$form->number('rate', 'Rate');
```
- 첫 번째 인자 : 모델의 컬럼
- 두 번째 인자 : 표시되는 항목의 타이틀
- 숫자를 1씩 더하고 뺄 수 있는 +, - 버튼이 있다.

#### switch
```
$form->switch('released', 'Released');
```
- 첫 번째 인자 : 모델의 컬럼
- 두 번째 인자 : 표시되는 항목의 타이틀
- on/off 전환 스위치 기능

#### dateTime
```
$form->dateTime('release_at', 'release time');
```
- 첫 번째 인자 : 모델의 컬럼
- 두 번째 인자 : 표시되는 항목의 타이틀
- 달력으로 날짜를 선택할 수 있으며, 텍스트의 날짜 포멧으로 입력할 수도 있다.
- 기존 날짜가 없는 경우 달력 버튼을 누르면 디폴트로 오늘 날짜가 선택되어 있다.

## Form의 기본 버튼
- 기본적으로 생성화면 또는 수정화면인 form 화면의 form 컨텐츠 영역 오른쪽 상단에 list, delete, view를 제공한다.
- list는 index 화면으로 이동하는 역할을 하며, delete는 수정 화면에 나온 대상을 삭제하는 역할을 하며 view는 상세 화면을 보여준다.
- 각 화면은 아래의 표현으로 표시되지 않게 할 수 있다.
```
protected function form()
{
    // code ...
    $form->tools(function (Form\Tools $tools) {

        // Disable `List` btn.
        $tools->disableList();

        // Disable `Delete` btn.
        $tools->disableDelete();

        // Disable `Veiw` btn.
        $tools->disableView();

        // Add a button, the argument can be a string, or an instance of the object that implements the Renderable or Htmlable interface
        $tools->add('<a class="btn btn-sm btn-danger"><i class="fa fa-trash"></i>&nbsp;&nbsp;delete</a>');
    });
    
    // code ...
    
    return $form;
}
```

#### disableList
```
$tools->disableList();
```
- 화면에 디폴트로 있는 list 버튼을 제거한다.

#### disableDelete
````
$tools->disableDelete();
````
- 화면에 디폴트로 있는 disableDelete 버튼을 제거한다.

#### disableView
````
$tools->disableView();
````
- 화면에 디폴트로 있는 disableView 버튼을 제거한다.

#### add
````
$tools->add('<a class="btn btn-sm btn-danger"><i class="fa fa-trash"></i>&nbsp;&nbsp;delete</a>');
````
- form 화면의 form 컨텐츠 영역 오른쪽 상단에 list, delete, view 버튼 이외의 버튼을 기본 버튼 왼쪽에 추가한다.
- 이 버튼 자체로는 아무 기능이 없기 때문에 기능을 추가 해 줘야 한다.
