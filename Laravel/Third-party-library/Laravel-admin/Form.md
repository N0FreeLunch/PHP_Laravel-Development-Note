# Form

## Form 메소드의 기능
- 일반적으로 라라벨의 메소드 edit에 해당하는 부분이다.
- 라라벨의 라우터에서 접근을 할 때 resource 메소드를 사용한다. 이는 컨트롤러의 엑션에 접근할 때 edit 메소드에 해당하는 것이다.
- form 메소드는 접근제한자가 protected이다. edit 메소드로 접근을 하면 form 메소드로 설정한 화면이 나오도록 설정되어 있는 것은 라라벨 어드민의 컨트롤러(AdminController) 상속을 통해 이뤄진다.

## Form의 기본 버튼
- 기본적으로 수정화면인 form 화면에서 제공하는 버튼은 list, delete, view를 제공한다.
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

