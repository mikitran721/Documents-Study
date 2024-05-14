# Tóm tắt

- Laravel cung cấp cho ta 1 helper function `url`. Hàm này cho phép ta tạo ra các links trên ứng dụng một cách đơn giản và linh hoạt.
- Nội dung
  1. Generate URL
  2. Truy cập URL hiện tại.
  3. Route URL
  4. Signed URL
  5. Controller action URL
  6. Default value

# 1. Generate URL

- Để generate URL trong Laravel ta sử dụng hàm `url`, cú pháp như sau

```php
url($path, $parameters, $secure);
```

- Trong đó
  - $path là path ta muốn generate
  - $parameters là các param ta muốn truyền vào URL, có thể bỏ trống
  - $secure là tham số quyết định protocol của URL là http hay https, có thể bỏ trống

```php
url('home');
// http://abc.com/home

url('home', ['page', 2]);
// http://abc.com/home/page/2

url('home', ['page', 2], true);
// https://abc.com/home/page/2
```

# 2. Truy cập URL hiện tại

- Để lấy URL hiện tại của request ta sử dụng phương thức `current`. Phương thức này sẽ trả về URL không bao gồm query string. VD: `echo url()->current();`
- Trong TH cần lấy URL bao gồm cả query string, ta có thể sử dụng phương thức `full`. VD: `echo url()->full();`.
- Trong TH cần lấy ra URL của request trước đó, bao gồm cả query string, ta có thể sử dụng phương thức `previous`. VD: `echo url()->previous();`
- Tất cả các cách trên nếu không muốn sử dụng helper, thì có thể thay thế bằng URL class (Illuminate\Support\Facades\URL).

```php
echo URL::current();
echo URL::full();
echo URL::previous();
```

# 3. Route URL

- Trong TH route có sử dụng route name, ta có thể sử dụng hàm `route` để generate ra URL của route đó. VD:

```php
// đối với route thường
Route::get('post', function () {
  //
})->name('post.index');

echo route('post.index'); //http://exam.com/post/
// đối với route có parameters
Route::get('/post/{post}', function () {
  //
})->name('post.show');

echo route('post.show', ['post' => 1]);
// http://exam.com/post/1

// or
Route::get('/post/{post}/comment/{comment}', function () {
  //
})->name('comment.show');
echo route('comment.show', ['post' => 1, 'comment' => 3]);
```

- Trong TH các param truyền vào hàm route không xuất hiện trong route đó thì những tham số đó sẽ được chuyển về dạng query string. VD:

```php
Route::get('/post/{post}', function () {
  //
})->name('post.show');
echo route('post.show', ['post' => 1, 'search' => 'php']);
//http://exam.com/post/1?search=php
```

# 4. Signed URL

- Laravel cho phép ta tạo ra một URL kèm theo một tham số, Laravel gọi nó là `signature`. Khi một route sử dụng chức năng này thì cần implement thêm middleware `signed` để check, nếu như chữ ký sai thì Laravel sẽ trả về http status là `403`. VD:

```php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\\Facades\URL;

Route::get('home', function () {
  return 'home';
})->name('home')->middleware('signed');

echo URL::signedRoute('home');
//http://exam.com/home?signature=d2f0042...
```

- Trong TH ta không muốn sử dụng middleware `signed` mà vẫn muốn kiểm tra signature có đúng hay không, ta có thể sử dụng phương thức `hasValidSignature` trong Request. VD:

```php
Route::get('home', function (Request $request) {
  if (! $request->hasValidSignature()) {
    abort(401);
  }

  return 'home';
})->name('home');
echo URL::signedRoute('home');
//http://exam.com/home?signature=d2f0042...
```

- Trong một số TH ta muốn tạo ra một signed URL chỉ tồn tại trong một khoảng time nhất định nào đó. Ta có thể sử dụng phương thức `temporarySignedRoute` với cú pháp:

```php
URL::temporarySignedRoute($routeName, $timeToLive, $parameters);
```

- VD: Tạo ra temporarySignedRoute cho route home và thời gian sống là 2h.

```php
Route::get('home', function (Request $request) {
  if (! $request->hasValidSignature()) {
    abort(401);
  }
  return 'home';
})->name('home');

Route::get('/', function () {
  return URL::temporarySignedRoute('home', now()->addHours(2));
});
```

# 5. Controller Action URL

- Ta có thể tạo URL thông qua controller action bằng cách sử dụng hàm `action`. VD:

```php
$url = action([HomController::class, 'index']);
```

- Tương tự với TH cần truyền params vào trong action, ta có thể truyền thêm tham số thứ 2 vào hàm action với cú pháp, chức năng tương tự hàm `route` mục trên - 3. VD:

```php
$url = action([UserController::class, 'profile'], ['id' => 1]);
```

# 6. Default value

- Trong một số TH, ta muốn gán giá trị mặc định cho một param nào đó thì ta có thể sử dụng phương thức `default`. VD:

```php
URL::defaults(['locale' => 'vi']);

Route::get('{locale}/about/', function (Request $request) {
  return 'about';
})->name('about');

Route::get('/', function () {
  return route('about');
});
```

- Ta cũng có thể đưa `URL::defaults(['locale' => 'vi']);` vào trong middleware, để thực thi các logic cần thiết để lấy ra locale.
- VD: tạo middleware SetDefaultLocaleForUrls và đưa logic vào đó

```php
class SetDefaulLocaleForUrls
{
  public function handle($request, Closure $next)
  {
    URL::defaults(['locale' => $request->user()->locale]);

    return $next($request);
  }
}
```

- Tuy nhiên khi khai báo middleware ta cần khai báo mức độ ưu tiên sao cho middleware của ta phải thực thi trước middleware SubstitueBidings. Cần config lại trong file `app/Http/Kernel.php`.

```php
protected $middlewarePriority = [
  //..
  \App\Httpp\Middleware\SetDefaultLocaleForUrls::class,
  \Illuminate\Routing\Middleware\SubstitueBingdings::class,
  //..
]
```
