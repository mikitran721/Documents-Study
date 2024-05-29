# Nội dung

- Trong Laravel tất cả các error, exception đều chạy qua class `App\Exceptions\Handler` để làm nhiệm vụ ghi log rồi hiển thị đến user.
- Mục lục:
  - Cấu hình
  - Exception Handler

# 1. Cấu hình

- Cấu hình `debug` trong `config/app.php` là tham số để quyết định có hiển thị thông tin lỗi đến user hay ko. Mặc định tham số này được xác định qua `APP_DEBUG` trong file '.env', và nếu là true thì gặp lỗi Laravel sẽ hiển thị các thông tin lỗi đó lên màn hình và ngược lại là false thì Laravel hiện thị status code 500 trên màn hình.
- Chỉ nên bật mode debug trên môi trường code | local.

# 2. Exeption handler

- Tất cả các exception đều được xử lý qua `app/Exceptions/Handler`. Trong class này ta có thể khai báo custom logic trong method `register`, method này sẽ rất hữu dụng khi tracking error của bên thứ 3.
- Ta có thể tùy chỉnh context của exception = cách overwrite lại phương thức `context` của class cha.
- VD: thêm URL gây lỗi vào context

```php
protected function context()
{
  return array_merge(parrent::context(), [
    'url' => URL::current(),
  ]);
}
```

- Nếu muốn loại bỏ một exception nào đó, không muốn report exception nữa. Ta có thể cấu hình trong `$dontReport` property.
- VD: Loại bỏ ProductNotFoundException ra khỏi ds report

```php
protected $dontReport = [
  ProductNotFoundException::class,
];
```

- Mặc định, Laravel sẽ tự động convert các Exception về dạng HTTP response. Tuy nhiên, nếu cần custom response cho một exception nào đó, ta có thể sử dụng method `renderable`.
- VD: render ra view errors.product_not_found khi gặp ProductNotFoundException.

```php
public function register()
{
  $this->renderable(function(ProductionNotFoundException $exception) {
    return response()->view('errors.product_not_found', [], 500);
  });
}
```

- Để thay đổi các view error trong Laravel, ta có thể tạo ra ccs view trong thư mục `resources/views/errors` với tên là các mã lỗi tương ứng. VD: 404.blade.php
- Ta có thể publish hết các error view trong core của Laravel ra thư mục `resources/views/errors` để chỉnh sửa lại. Bằng cách sử dụng command:

```php
php artisan vendor:publish --tag=laravel-errors
```

- Trong TH, ta muốn render ra một view error nào đó mà không muốn raise (tạo) exception trong đoạn code đó. Ta có thể dùng hàm `abort` với cú pháp: `abort($code, $message, $headers)`. Trong đó:
  - $code là mã http code mà ta muốn render ra.
  - $message là message ta muốn render kèm theo http code, có thể empty.
  - $header là các thông số header ta muốn đẩy vào response, có thể empty.
