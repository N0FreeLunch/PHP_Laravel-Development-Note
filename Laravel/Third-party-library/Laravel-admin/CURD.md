## CURD 메소드
- 라라벨에서 CURD 메소드는 index, show, create, store, update, destroy 등으로 구성된다.
- laravel-admin에서는 grid, detail, form으로 구성된다.

### 라라벨 기본 액션의 구성
- 컨트롤러의 엑션에 따라 페이지의 PATH 구성이 달라지는데 룰이 있다.

#### index
- `GET` 
- /controller/access/path

#### show
- `GET`
- /controller/access/path/{id}

#### create
- `POST`
- /controller/access/path

#### store
- `POST`
- /controller/access/path/{id}

#### edit
- `GET`
- /controller/access/path/{id}/edit

#### update
- `PUT/PATCH` 
- /controller/access/path/{id}

#### destroy
- `DELETE`
- /controller/access/path/{id}

### laravel-admin에서의 액션의 구성
#### grid
- `GET`
- /controller/access/path

#### detail
- `GET`
- /controller/access/path/{id}

#### form
- `GET`
- /controller/access/path


### grid
- 데이터 리스트를 보여준다.

### detail
- 하나의 데이터의 세부 정보를 보여준다.

### form
-  데이터의 생성 및 수정 기능을 담당한다.

### controller 예제
```
<?php

namespace App\Admin\Controllers;

use App\Models\User;
use Encore\Admin\Controllers\AdminController;
use Encore\Admin\Form;
use Encore\Admin\Grid;
use Encore\Admin\Show;

class UserController extends AdminController
{
    protected $title ='Users';

    protected function grid()
    {
        $grid = new Grid(new User());

        $grid->column('id', __('Id'));
        $grid->column('name', __('Name'));
        $grid->column('email', __('Email'));
        $grid->column('password', __('Password'));
        $grid->column('created_at', __('Created at'));
        $grid->column('updated_at', __('Updated at'));

        return $grid;
    }

    protected function detail($id)
    {
        $show = new Show(User::findOrFail($id));

        $show->field('id', __('Id'));
        $show->field('name', __('Name'));
        $show->field('email', __('Email'));
        $show->field('password', __('Password'));
        $show->field('created_at', __('Created at'));
        $show->field('updated_at', __('Updated at'));

        return $show;
    }

    protected function form()
    {
        $form = new Form(new User());

        $form->textarea('name', __('Name'));
        $form->textarea('email', __('Email'));
        $form->textarea('password', __('Password'));

        return $form;
    }
}
```
- `protected $title` : CURD 모듈의 이름을 지정한다.
- `protected function grid()` : 페이지에 표시할 목록들을 설정한다.
- `protected function detail($id)` : details 페이지에 표시할 목록들을 설정한다.


