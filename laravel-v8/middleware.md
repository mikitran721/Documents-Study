# Tóm tắt (16)

- Nội dung gồm:

1. Khái niệm
2. Khai báo Middleware
3. Đăng ký Middleware
4. Mức độ ưu tiên middleware
5. Middleware parameter

# 1. Khái niệm

- Middleware là phần mềm có nhiệm vụ kết nối các thành phần phần mềm hoặc ứng dụng với nhau. Phần mềm này bao gồm một tập các dịch vụ, cho phép sự tương tác giữa các tiến trình chạy trên 1 hoặc nhiều máy khác nhau. Công nghệ Middleware đã được phát triển để cung cấp khả năng hoạt động tương hỗ, phục vụ cho các kiến trúc phân tán, thường được để hỗ trợ và đơn giản hóa các ứng dụng phân tán phức tạp.
- Trong Laravel, middleware cung cấp một cơ chế thuận tiện để `kiểm tra và lọc các HTTP request` đến ứng dụng của ta.
- VD: middleware `Authenticate` có tác dụng kiểm tra xem người dùng đang thực thi request đã đăng nhập hay chưa? Nếu chưa đăng nhập sẽ redirect về trang login và ngược lại thì cho phép thực hiện request.
- Trong Laravel, tất cả các middleware sẽ được đặt trong thư mục `app/Http/Middleware`.

# 2. Khai báo | Tạo middleware

- Để tạo middleware trong Laravel ta sử dụng command: `php artisan make:middleware MiddlewareName`. Trong đó: MiddlewareName là tên của middleware muốn tạo.
- VD: tạo middleware có name là EnsureTokenIsValid `php artisan make:middleware EnsureTokenIsValid`.
- Lúc này Laravel sẽ sinh ra một file middleware nằm trong thư mục `app/Http/Middleware` với file name là tên middleware ta mới nhập. File có nội dung:

```php
// file: app/Http/Middleware/EnsureTokenIsValid
public function handle(Request $request, Closure $next)
{
    return $next($request);
}
```

- Trong đó: `return $next($request)` chính là đoạn code cho phép request tiếp thục được thực thi.
- VD: sẽ redirect về URL /home khi request không có token, hoặc token không là 'lovephp.com'

```php
// file: app/Http/Middleware/EnsureTokenIsValid
public function handle(Request $request, Closure $next)
{
    if ($request->input('token') !== 'lovephp.com') {
        return redirect('home');
    }

    return $next($request);
}
```

- Lúc này request user gửi lên nếu không có input token hoặc khác 'lovephp.com' thì request sẽ được redirect về URL có path /home.

# 3. Đăng ký middleware

- Để đăng ký middleware vào trong app, ta khai báo chúng trong file `app/Http/Kernel.php`. Mặc định, Laravel có 3 loại middleware chính là:
  1. Global middleware: Các middleware được khai báo trng thuộc tính $middleware là global middleware, các middleware này sẽ được thực thi cho tất cả các request.
  2. Group middleware: các middleware được khai báo trong thuộc tính $middlewareGroups sẽ được thực thi khi ta gọi chúng. Mặc định Laravel định nghĩa ra 2 group là web và api tương ứng với các route trng web.php và api.php
  3. Route middleware: các middleware được khai báo trong thuộc tính $routeMiddleware sẽ được thực thi khi ta gọi tên chúng.
- VD: khai báo middleware `EnsureTokenIsValide` vào trong $routeMiddleware với name là `validate_token`

```php
protected $routeMiddleware = [
    //..
    //..
    'validate_token' => \App\Http\Middleware\EnsureTokenIsValid::class
];
```

- Lúc này nếu route nào cần dùng thì chỉ cần khai báo middleware là `validate_token` là được. VD

```php
Route::get('/user', function () {
    //
})->middleware('validate_token');
```

# 4. Mức độ ưu tiên Middleware

- Thông thường middleware sẽ được thực thi theo thứ tự từ trên xuống. Nhưng nếu muốn thay đổi thứ tự ưu tiên của một middleware nào đó. Ta có thể thêm vào trong thuộc tính `$middlewarePriority` trong file `app/Http/Kernel.php`.
- Thuộc tính $middlewarePriority chỉ work đối với middleware không phải là global.

# 5. Middleware parameter

- Trong một số trường hợp, ta muốn truyền thêm vào tham số cho middleware để thực thi các logic cho từng tham số đó. Ta có thể thực hiện tương tự như VD dưới

```php
// file: app/Http/Middleware/EnsureTokenIsValid
public function handle(Request $request, Closure $next, $redirectTo = 'home')
{
    if ($request->input('token') !== 'lovephp.com') {
        return redirect($redirectTo);
    }

    return $next($request);
}
```

- Lúc này khi gọi middleware ta có thể truyền thêm tham số vào middleware như sau:

```php
Route::get('/', function () {
    return redirect()->back();
})->middleware('validate_token:/login');
```

- Trong đó, đằng sau dấu `:` sẽ là tham số truyền vào middleware, nếu middleware có nhiều tham số truyền vào ta có thể sử dụng dấu `,` để ngăn cách biến.
