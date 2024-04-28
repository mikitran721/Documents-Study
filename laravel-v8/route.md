- Laravel hỗ trợ 3 loại route chính:
  - Web
  - Cli (command)
  - broadcast
# Route cho Web
- Mặc định khai báo routes cho web thì ta khai báo trong 2 file routes/web.php hoặc routes/api.php
- Nếu muốn khai báo trong file khác cần khai báo trong `app/Providers/RouteServiceProvider.php`
## Đăng ký route
- nếu muốn đăng ký một route hỗ trợ nhiều method thì có thể dùng `match` theo cú pháp `Route::match($method, $uri, $callback)`
- hoặc nhận tất cả các HTTP method với `any` theo cú pháp `Route::any($uri, $callback)`
- Trong đó:
  - $uri là path ta muốn route xử lý
  - $callback là một callback, callable hoặc một array chứa thông tin controller và phương thức tác động đến.
  - $method là một array chứa các phương thức mà ta muốn route này xử lý.
- Nếu muốn đưa logic code vào controller thì sử dụng `Route::routeMethod($uri, [$controllerName, $method])`. Trong đó `$method` là phương thức trong controller chứa logic để xử lý cho route đó.
## Một số route khác
- Route `redirect` với cú pháp `Route::redirect($uri, $redirectTo, $status)`. Trong đó:
  - $uri là path cần xử lý
  - $redirectTo là path là muốn redirect đến.
  - $status là http status mà ta muốn thiết lập, mặc định $status là 302
- Route `view` với cú pháp `Route::view($uri, $viewName, $data)`. Trong đó
  - $uri là path cần xử lý
  - $viewName là view ta muốn render
  - $data là data mà ta muốn truyền vào view.
  - có thể truyền thêm 2 tham số http status và header
  - Route view chỉ làm việc với HTTP request là `get` | `head`
- Ta có thể group các route lại để tiện xử lý qua phương thức `group`
- VD:
```php
Route::prefix('greeting')->group(function () {
    //uri: /greeting/vn
    Route::get('vn', function () {
        return 'xin chao';
    });

    // uri: greeting/en
    Route::get('en', function () {
        return 'hello from miki';
    });
});
```