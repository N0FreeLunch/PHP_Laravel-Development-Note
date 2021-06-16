```
    public function whereKey($id)
    {
        if ($id instanceof Model) {
            $id = $id->getKey();
        }

        if (is_array($id) || $id instanceof Arrayable) {
            $this->query->whereIn($this->model->getQualifiedKeyName(), $id);

            return $this;
        }

        if ($id !== null && $this->model->getKeyType() === 'string') {
            $id = (string) $id;
        }

        return $this->where($this->model->getQualifiedKeyName(), '=', $id);
    }
```
- 매개변수 $id는 여러 형태의 타입으로 존재한다. Model 클래스의 객체 또는 array나 Arrayable 클래스의 인스턴스 또는 단일 원시 타입이다.
- 이 함수의 주요 로직은 매개변수 $id의 처리에 관한 로직으로 구성되어 있다. $id가 단수일 경우와 복수일 경우 처리가 달라진다.
- $id가 Model의 인스턴스일 경우, $id를 단수일 경우와 복수일 경우로 처리할 수 있게 값을 가져온다.
- 먼저 복수일 경우 로직을 쓰고 return 하며, 단수일 경우 로직이 다음에 순차적으로 위치하고 return 한다.
- $id가 단수일 경우는 문자열로 변환하는데, 이는 쿼리 빌더의 where 메서드에 어떤 값이든 집어 넣을 수 있게 하기 위함이다.
- $this->model->getQualifiedKeyName()가 반복되는데 변수에 넣지 않고 그냥 그대로 써 줬다. 변수를 많이 생성하는 코드를 지양함



## Reference
https://github.com/laravel/framework/blob/8.x/src/Illuminate/Database/Eloquent/Builder.php
