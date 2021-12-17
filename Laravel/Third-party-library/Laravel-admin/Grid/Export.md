## CSV export
- laravel-admin에서는 기본적으로 Grid 화면에서 CSV 다운로드 버튼을 제공한다.
- CSV 다운로드는 선택한 row만 다운로드 하는 방식, 현재 페이지의 row를 다운로드 하는 방식, 전체 페이지를 다운로드 하는 방식이 존재한다.
- 선택한 row만 다운로드 하기 위해서는 row select 버튼을 활성화 해야 한다. row select 버튼을 활성화 하면 row를 check box 형식으로 선택할 수 있다.
- default 설정의 CSV 다운로드는 엘로퀀트 모델에서 기본적으로 조회 가능한 모든 컬럼을 CSV 파일에 표시한다.

### row select
- row select는 기본적으로 활성화 되어 있다.
- 비활성화 하려면 다음 메소드를 호출한다.
```
$grid->disableRowSelector();
```

### export 버튼
- export 버튼은 기본적으로 활성화 되어 있다.
- 비활성화 하려면 다음 메소드를 호출한다.
```
$grid->disableRowSelector();
```

## CSV 컬럼 커스터 마이징 하기
- Grid 화면에서 export 버튼을 누르면 다운로드_대상_클래스의 export 함수가 호출된다.
- 함수가 호출 될 때는 새 창을 띄워서 호출되기 때문에 현재 열린 페이지에는 아무런 이상이 없다.
```
$grid->exporter(new 다운로드_대상_클래스());
```

### 다운로드 대상 클래스를 만들어 보자
- CSV 다운로드 클래스 만들기
- Reference : https://github.com/z-song/laravel-admin/blob/master/src/Grid/Exporters/CsvExporter.php
- Reference : https://github.com/z-song/laravel-admin/blob/1.5/src/Grid/Exporters/CsvExporter.php (좀 더 단순한 형태의 예전 버전)
- 좀 더 단순한 형태의 예전 버전을 기준으로 설명한다.
- https://github.com/z-song/laravel-admin/blob/1.5/src/Grid/Exporters/AbstractExporter.php 부분의 AbstractExporter 추상 클래스로 인해 이를 상속 받은 클래스는 항상 export 메소드를 가져야 한다.
- `$grid->exporter(new 다운로드_대상_클래스());` 방식으로 호출하면 '다운로드_대상_클래스'의 export 메소드를 내부적으로 호출하도록 되어 있다.
```php
<?php

namespace Encore\Admin\Grid\Exporters;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Collection;
use Illuminate\Support\Str;
use Illuminate\Support\Arr;

class CsvExporter extends AbstractExporter
{
    /**
     * {@inheritdoc}
     */
    public function export()
    {
        $filename = $this->getTable().'.csv';

        $headers = [
            'Content-Encoding'    => 'UTF-8',
            'Content-Type'        => 'text/csv;charset=UTF-8',
            'Content-Disposition' => "attachment; filename=\"$filename\"",
        ];

        response()->stream(function () {
            $handle = fopen('php://output', 'w');

            $titles = [];

            $this->chunk(function ($records) use ($handle, &$titles) {
                if (empty($titles)) {
                    $titles = $this->getHeaderRowFromRecords($records);

                    // Add CSV headers
                    fputcsv($handle, $titles);
                }

                foreach ($records as $record) {
                    fputcsv($handle, $this->getFormattedRecord($record));
                }
            });

            // Close the output stream
            fclose($handle);
        }, 200, $headers)->send();

        exit;
    }

    /**
     * @param Collection $records
     *
     * @return array
     */
    public function getHeaderRowFromRecords(Collection $records): array
    {
        $titles = collect(Arr::dot($records->first()->toArray()))->keys()->map(
            function ($key) {
                $key = str_replace('.', ' ', $key);

                return Str::ucfirst($key);
            }
        );

        return $titles->toArray();
    }

    /**
     * @param Model $record
     *
     * @return array
     */
    public function getFormattedRecord(Model $record)
    {
        return Arr::dot($record->getAttributes());
    }
}
```
- 위의 예제로는 커스터마이징 하기 어렵다.
