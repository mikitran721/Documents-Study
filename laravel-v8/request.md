# Tóm tắt:

- Đây là phần 1 về Request trong Laravel, nội dung đề cập đến các tương tác với `URL, HEADER`.

# 1. Khái niệm

- Request là một object chứa các thông tin liên quan đến HTTP request hiện tại. Dựa vào object này ta có thể lấy được các thông tin như `input, cookie, file,...`.
- Class request nằm trong `Illuminate\Http\Request` core của Laravel.

# 2. Tương tác với Request

- Mặc định Request object đã được binding vào service container của Laravel rồi, nên ta khi muốn dùng nó thì có thể binding trực tiếp vào trong argument khi cần.
- VD: inject trong Route

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Routes;

Route::get('/', function (Request $request) {
    // code
});
```

- VD: inject rong Controller

```php
// file: controller
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $name = $request->input('name');
    }
}

// file: route
use App\Http\Controllers\UserController;

Route::post('/user', [Usercontroller::class, 'store']);
```

## 2.1 Truy vấn thông tin `url/path`:

- Path của url là dạng `x` của `home.com/x?name=abc`;
- Để lấy path của request ta sử dụng method `path`. VD:

```php
Route::get('/', function (Request $req){
    return "Path: " . $req->path();
});
```

# 2.2 Truy vấn `url` của request

- Khi này ta sử dụng phương thức `url` của request object. VD:

```php
Route::get('/', function (Request $req){
    return "URL: " . $req->url();
});
```

## 2.3 Truy vấn `full url` của request

- Khi này ta sử dụng phương thức `fullUrl` của Request object. VD:

```php
Route::get('/', function (Request $req) {
    return "Full URL: " . $req->fullUrl();
});
```

# 2.4 Thêm query vào trong path url

- Trong trường hợp này ta có thể sử dụng phương thức `fullUrlWithQuery`. VD:

```php
Route::get('/', function (Request $req) {
    return 'Path with query: ' . $req->fullUrlWithQuery(['name' => 'Love PHP']);
});
```

# 2.5 Kiểm tra điều kiện với path url

- Để kiểm tra path hiện tại có match với một path rule nào đó hay không, ta có thể sử dụng phương thức `is`. VD: kiểm tra path hiện tại có bắt đầu bằng admin hay không?

```php
if ($request->is('admin/*')) {
    //code
}
```

- Ta cũng có thể sử dụng phương thức `routeIs` để kiểm tra tương tự như phương thức `is` nhưng là check qua route name. VD: kiểm tra xem path hiện tại có nằm trong route name bắt đầu bằng admin kô?

```php
if ($request->routeIs('admin.*')) {
    // code
}
```

# 2.5 Truy vấn method của request

- Để lấy ra method của request ta có thể sử dụng phương thức `method`. VD: `$req->method()`.
- Ta có thể sử dụng phương thức `isMethod` để kiểm tra phương thức của Request. VD: kiểm tra xem request có phải POST request không?

```php
if ($req->isMethod('post')) {
    // code
}
```

# 2.6 Truy vấn thông tin headers

- Nếu cần truy vấn thông tin liên quan đến header của request ta có thể sử dụng phương thức `header`. VD: lấy ra user-agent của request

```php
Route::get('/', function (Request $req) {
    return 'User agent: ' . $req->header('user-agent');
})
```

- Trong trường hợp request header cần lấy ra không tồn tại thì phương thức `header` sẽ trả về `null`. Nếu muốn thay đổi giá trị default này ta có thể truyền thêm tham số thứ 2 vào hàm `header`. VD:

```php
Route::get('/', function (Request $req) {
    return "Header: " . $req->header('notexist', 'This header does not exist');
});
```

## Truy vấn `bearer token` của request:

- Ta sử dụng phương thức `bearerToken`. VD:

```php
$token = $req->bearerToken();
//same as bellow
$token = $request->header('Authorization', '');
```

## Truy vấn IP của người dùng:

- Ta sử dụng phương thức `ip`. VD:

```php
$ipAddress = $req->ip();
```
