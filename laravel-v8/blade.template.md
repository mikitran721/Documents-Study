# 1.1 Giới thiệu
- Blade là 1 công cụ giúp ta xử lý cú pháp trong view một cách ngắn gọn, logic. Hơn nữa, blade cho phép ta sử dụng cả PHP thuần trong đó.
- Tất cả blade template sẽ được biên dịch về mã PHP thuần và cache lại nên về cơ bản sử dụng blade template cũng ko làm cho app chạy chậm đi.
- Để sử dụng Blade template trong Laravel ta chỉ cần tạo view với đuôi file `.blade.php`.
- Blade template về cơ bản giống view thông thường, nên ta có thể sử dụng như view thông thường.
# 1.2 Hiển thị dữ liệu trong Blade
- Có thể hiển thị dữ liệu trong Blade template với cú pháp `{{ code }}`. Trong cặp {{}} sẽ là code PHP.
- VD:
```php
//file routes/web.php
Route::get('/', function () {
    return view('home', ['name' => "Poo Phoong"]);
})->name('home');

//file: resources/views/home.blade.php
Hello, {{ $name }}!
```
- Cặp dấu {{}} tượng trưng cho câu lệnh `echo` trong PHP kết hợp với hàm `htmlspecialchars` để phòng chống lỗi XSS attack. Tức là ta sẽ không thể in ra mã HTML khi sử dụng cặp {{}} này được.
- Mặc định Laravel sẽ bật chế độ `double encoding` trong hàm `htmlspecialchars` nếu ta muốn tắt nó có thể sử dụng `Blade::withoutDoubeEncoding` để tắt chúng đi. Ta nên đặt nó trong Provider để cho nó hoạt động tốt.
- VD: tắt double encoding trong Blade
```php
//file: app/Providers/AppServiceProvider.php

public function boot()
{
    Blade::withoutDoubleEncoding();
}
```
- Khi này, khi muốn hiển thị ra dữ liệu nguyên bản của data truyền tới ta sử dụng cặp dấu `{{ !! $var !!}}`. VD:
```php
//file: routes/web.php
Route::get('/', function() {
    return view('home', ['name' => "<span style='color: red;' Love PHP </span>"]);
})->name('home');

//file: resources/views/home.blade.php
Hello, {{ !! $name !!}}
```
- Lưu ý: vì các cặp {{}}, {{!!!!}} chỉ tương ứng với câu lệnh `echo` trong PHP, nên nó chỉ nhận `string` truyền vào thôi.
- Để hiển thị dữ liệu dạng json encode trong Blade, ta có thể sử dụng @json với cú pháp `@json($array, $flag)`. Trong đó:
  - $array là mảng dữ liệu muốn encode.
  - $flag là kiểu encode ta muốn - có thể bỏ trống. 
- VD:
```php
app = @json($array);
//same: app = <?php echo json_encode($array); ?>;
```
# 1.3 Blade & JS Framework:
- Vì hiện khá nhiều JS framework cũng sử dụng cặp {{}} để in ra dữ liệu. Nếu nếu ta không muốn Blade compile đoạn code nào đó thì chỉ cần thêm ký tự `@` vào trước là được.
- VD: `Hello, @{{ $name }}`
- Trong trường hợp ta không muốn Blade compile nhiều dòng code thì có thể sử dụng `@verbatim directive`. VD
```php
@verbatim
    <div class="container">
        Hello, {{ $name }}
    </div>
$endverbatim
```
# 1.4 Comment trong Blade
- Để comment trong blade sử dụng {{ -- comment --}}, với comment là nội dung muốn comment.