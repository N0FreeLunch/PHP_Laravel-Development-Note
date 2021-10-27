

## sample table 만들어 보기
```
php artisan make:migration CreateMoviesTable
```

### schema 세팅하기
```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateMoviesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('movies', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->integer('director');
            $table->string('describe');
            $table->tinyInteger('rate');
            $table->enum('released', [0,1]);
            $table->timestamp('release_at');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('movies');
    }
}
```

### migarate하기
```
php artisan migrate
```

## Model 만들기
```
php artisan make:model Movies
```

## Controller 만들기
```
php artisan admin:make MovieController --model=App\\Models\\Movie
```

## Controller 내부 form 세팅
```
    /**
     * Make a form builder.
     *
     * @return Form
     */
    protected function form()
    {
      $form = new Form(new Movie);

      // Displays the record id
      $form->display('id', 'ID');

      // Add an input box of type text
      $form->text('title', 'Movie title');

      $directors = [
          'John'  => 1,
          'Smith' => 2,
          'Kate'  => 3,
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

## 유저 생성하기
```
http://127.0.0.1:8000/admin/demo/users
```
- 유저를 생성한다.
- id 1인 유저의 이름은 John
- id 2인 유저의 이름은 Smith
- id 3인 유저의 이름은 Kate


## 접속
```
http://127.0.0.1:8000/admin/demo/movies
```

