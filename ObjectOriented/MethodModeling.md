## 메소드 모델링

### 객체를 자동차에 비유하기
- 운전자는 자동차를 조작하는 사람이다. 자동차의 움직임은 운전자의 조작에 의해 결정된다. 곧 자동자라는 객체를 다루는 대상은 운전자이다.
- 핸들, 엑셀레이터, 브레이크, 클러치, 기어는 사용자가 조작을 하는 기능이며, 계기판은 사용자가 확인하는 기능이다. 이들은 운전자에게 제공된 자동차의 인터페이스이다.
- 자동차는 운전자로 하여금 운전이라는 목적을 수행하게끔 만들어져 있으며, 운전을 하기 위한 인터페이스는 제공하지만 운전자에게 자동차의 각 부분을 세밀하게 컨트롤 하는 인터페이스를 제공하는 것에는 제한을 둔다.

### 제약조건
- 브레이크와 엑셀을 동시에 밟으면 브레이크의 동작이 우선되어야 한다.
- 기어를 변경할 때는 클러치를 밟아야 하고 클러치가 밟힌 상태에서 기어를 한 단계씩 조정한다.
- 핸들은 왼쪽 또는 오른쪽 방향으로 돌아가야 한다. 양방향으로 동시에 돌아갈 수 없다.
- 계기판에는 속도, RPM, 연료량, 온도 등의 다양한 데이터가 표시된다.

### 무엇을 인터페이스로 할 것인가?
- 자동차의 인터페이스는 핸들, 엑셀레이터, 브레이크, 기어, 계기판이다. 왜? 이런 인터페이스를 제공해야 자동차를 다루기 쉽기 때문이다. 이들 인터페이스는 운전이라는 목적을 위해서 존재하는 자동차의 인터페이스이다.
- 운전에서 중요한 것은 상대 속도이다. 인간은 정확한 속도를 측정할 수 없기 때문에 점진적으로 속도를 증가시키거나 감소 시켜야 하며, 방향을 급작스럽게 변경하면 안 되기 때문에 점진적으로 방향을 증가시키거나 감소 시켜야 한다.

### 도메인
- 바퀴를 직접 인간이 다루는 것은 물리적으로 불가능하다. 바퀴의 움직임을 변경할 수 있는 자동차의 기능을 사용해서 변경해 주어야 한다. 따라서 자동차라는 개념이 만들어졌고 자동차의 움직임을 조정하는 기능은 핸들, 엑셀레이터, 브레이크, 기어라는 개념이 형성되었다.
- 바퀴의 방향과 속도를 직접 변경하는 인터페이스를 물론 만들 수 있지만, 자동차라는 개념이 형성된 것에 맞게 프로그래밍 언어로 모델링 하는 것은 자동차를 다루는 대상과 엔지니어간의 의사 소통을 자연스럽게 하는 역할을 한다.
- 도메인 기반의 모델링을 하려면 메소드에 핸들, 엑셀레이터, 브레이크, 기어라는 개념을 부여해야 한다.

### 메소드를 어떻게 모델링 할 것인가?
```php
class Car
{
    public function handleLeft(int $angle): self
    {
    }

    public function handleRight(int $angle): self
    {
    }

    public function speedControl(int $upDownPressure): self
    {
    }

    public function chagneGear(bool $upDown, bool $pressClutch = true): self
    {
    }

    public function dashboard(): DashboardDto
    {
    }

    public function getDirection(): int
    {
    }

    public function getCurrentGear(): GearEnum
    {
    }
}
```
```php
    public function handleLeft(int $angle): self
    {
    }

    public function handleRight(int $angle): self
    {
    }
```
- 헨들의 경우 왼쪽으로 돌리거나 오른쪽으로 돌리거나 하나만 할 수 있다. 따라서 `handle(int $leftAngle, int $rightAngle)`와 같은 코드를 사용하지 않는다.
- `handle(int $angle)`으로 `$angle`이 음수인 경우 왼쪽으로 `$angle`이 양수인 경우 오른쪽으로 변경하는 코드를 만들 수도 있다. 하지만 이런 규칙을 변수명으로 표시하기 모호하다. 주석을 달거나 `handle(int $angleMinusLeftPlusRight)`이런 식의 변수명을 달아 줘야 한다. 
```php
    public function speedControl(int $upDownPressure): self
```
- 브레이크와 엑셀은 대개의 경우 동시에 밟을 일이 없다. 자동차의 인터페이스로는 브레이크와 엑셀이 각각 존재하지만, 프로그래밍으로는 `break`와 `accelerator`를 따로 만들지 않고, `speedControl(int $upDownPressure)`으로 `$upDownPressure` 값으로 `+` 또는 `-`를 설정할 수 있다. 이렇게 하면 브레이크와 엑셀을 동시에 밟는 경우를 객체 모델링을 할 때 배제 할 수 있다. 브레이크와 엑셀을 각각 따로 만들면 다음과 같다.
```php
    public function break(int $pressure): self
    {
    }

    public function accelerator(int $pressure): self
    {
    }
```
```php
    public function chagneGear(bool $upDown, bool $pressClutch = true): self
    {
    }
```
- 기어를 변경할 때는 클러치를 밟고 기어를 한 단계씩 변경하는 것이 일반적이다. 클러치는 엔진의 동력을 자동차 바퀴에 전달할 때 동력을 전달하거나 차단하는 역할을 한다. 클러치를 누른 경우 자동차의 엔진의 동력이 바퀴에 전달되지 않고 헛돌게 되고 이 때 기어를 바꾸어 기어의 변경에 부하가 걸리지 않도록 한다.
- 기어를 변경할 때는 클러치를 항상 바꾸도록 `chagneGear(bool $upDown, bool $pressClutch = true)`와 같이 한 메소드를 동작할 때 함께 설정하도록 만들었다. 기본적으로 기어를 변경할 때는 클러치를 밟지만 경우에 따라 밟지 않을 수도 있다. 기본 값을 true으로 두고 예외적인 경우 false로 사용할 수 있도록 만들었다.
- 기어는 한 단계씩 변경을 해야 한다. 따라서 `bool $upDown`으로 여러단을 변경시킬 수 있는 수치의 입력을 방지하도록 만들었다.

### 메소드는 제한된 표현력을 가진다.
```php
chagneGear(bool $upDown, bool $pressClutch = true)
```
- 위의 코드에서 현재의 기어가 무엇인지 알고 기어를 올리거나 내릴 것인지 알 수 없다.
```php
    public function dashboard(): DashboardDto
    {
    }

    public function getCurrentGear(): GearEnum
    {
    }
```
- 데시보드의 속도와 현재 기어를 확인한 후 기어를 높일 것인지 줄일 것인지를 판단 해 줘야 한다.
- 메소드로 모든 제약 조건을 드러낸다고 생각하기 보다는 어느 정도의 제약 조건을 부여하여 도메인에 대해 어느 정도 이해하고 있는 사람이 객체와 객체의 인터페이스(퍼블릭 메소드)를 보고 어떤 역할을 하는지 파악할 수 있도록, 제약 조건을 추측할 수 있을 정도로만 만들어 주면 된다.
