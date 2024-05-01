- Ta có thể trả về HTML trong route, nhưng nếu HTML quá dài thì code sẽ rối. Nên ta cần đưa chúng vào view để dễ quản lý.
# 1. Tạo view
- Mặc định, các view sẽ được đặt trong thư mục resource/views. Nếu muốn thay đổi có thể vào config/view.php thay đổi lại giá trị của `paths`.
- Mặc định laravel hỗ trợ view có đuôi là `.html, .php, .css, .blade.php` nếu trong thư mục có cùng các view trùng tên thì sẽ ưu tiên theo thứ tự `.blade.php, .php, .css, .html`
# 2. Render view
- Để render view ta có thể sử dụng hàm view() hoặc View::make() với cú pháp bên dưới, với:
  - $view là path đến view (tính từ views)
  - $data là mảng data muốn truyền vào trong view. Tham số này có thể trống.
```php
view($view, $data);

//hoặc
use \Iluminate\Support\Facades\View;

View::make($view, $data);
```
- VD:
```php
// routes/web.php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('home');
})->name('home');
```
- VD với view có data truyền vào
```php
// resources/views/home.php

<title><?=$title?></title>
//...
<h1><?=body?></h1>

// routes/web.php
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('home', ['title' => 'Miki Tran', 'body' => 'Love PHP']);
})->name('home');
```
- Nếu view của ta nằm trong một thư mục con trong `resources/views` thì ta có thể sử dụng dấu `. để đại diện cho ký tự /`. VD: với file view `resources/views/home/index.php` thì render như sau
```php
// routes/web.php
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('home.index', ['title' => 'Miki Tran', 'body' => 'Love PHP']);
})->name('home');
```
# 3. View first trong laravel
- Trong một số trường hợp, ta cần render ra view tồn tại đầu tiên trong danh sách view thì có thể sử dụng phương thức `first` trong View object với cú pháp: `use \Iluminate\Support\Facades\View; View::first($views, $data);`. Trong đó:
  - $views là mảng path đến view (tính từ thư mục views)
  - $data là mảng data cần truyền vào view đó - nó có thể bỏ trống. VD:
```php
use \Iluminate\Support\Facades\View;

View::first(['custom.admin', 'admin'], $data);
```
# 4.Kiểm tra view tồn tại
- Để check xem một view có tồn tại không, ta sử dụng phương thức `exists` với cú pháp `View::exists($view);`. Trong đó $view là path đến view cần kiểm tra.
- VD: kiểm tra thư mục views có view nào tên `home` không?
```php
use \Iluminate\Support\Facades\View;

if (View::exists('home') {
    //ton tai
})
```
# 5. Truyền data cho tất cả view
- Trong laravel để truyền data chung cho tất cả view, ta có thể sử dụng phương thức `View::share()`. Để cho app được hoạt động tốt và chính xác nhất, ta nên đưa `view share` vào trong `provider`.
- VD: share data có name là siteTile cho tất cả các view trong laravel. Khi thực hiện xong, trong các view có thể sử dụng biến $siteTile
```php
// file: Providers\AppServiceProvider.php
use \Iluminate\Support\Facades\View;

public function boot
{
    View::share('siteTitle', 'Love PHP');
}
```
# 6. View composer
- View composer là một class hoặc một callback nó sẽ được gọi khi view render. Nếu ta muốn đưa data vào view khi render thì View composer cũng là một giải pháp hay.
- Trong một số trường hợp nhiều view cần dùng chung một đoạn logic (nhưng ko phải tất cả) và truyền vào view thì ta nên sử dụng view composer cho code đỡ bị lặp lại.
- Khi này ta cần đăng ký view composer vào trong provider để Laravel biết. Và laravel có cung cấp cho ta 2 cách để khai báo view composer là dùng `closure hoặc class`.
## 6.1 View composer dùng `closure`
- Cú pháp khai báo
```php
View::composer($views, function ($view) {
    //code
});
```
- Trong đó: $views là view ta muốn tác động. Nếu muốn tác động đến nhiều view ta có thể truyền vào một mảng & nếu muốn tác động hết thì chỉ cần khai báo ` $view = '*' `
- VD: thêm $title vào trong view `home` sử dụng view composer - closure.
```php
View::composer('home', function ($view) {
    $view->with('title', 'Love PHP');
})
```
## 6.2 View composer dùng `class`
- Sử dụng cú pháp `View::composer($views, $className);`
- Trong đó:
  - $views là view ta muốn tác động. Nếu muốn tác động đến nhiều view ta có thể truyền vào một mảng & nếu muốn tác động hết thì chỉ cần khai báo ` $view = '*' `
  - $className là tên của class chưa có logic composer. Class composer này bắt buộc phải có method `compose` có visibility là `public` và nhận tham số truyền vào là một View object.
- VD: thêm $title vào trong view `home` sử dụng view composer - class
```php
//file app/Http/View/Composers/SiteNameComposer.php
namespace App\Http\View\Composers;

use App\Repositories\UserRepository;
use Illuminate\View\view;

class SiteNameComposer
{
    protected $users;

    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }

    public function compose(View $view)
    {
        $view->with('title', 'Love PHP');
    }
}

//Khai bao trong provider: Providers\AppServiceProvider.php
use App\Http\View\Composers\SiteNameComposer;

View::composer('home', SiteNameComposer::class);
``` 