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
# 2.1 Truyền tham số trong route
- Để cí thể nhận tham số params trong thì ta cần cú pháp `{name}` vào trong `$uri` với name là định danh cho param đó.
- VD
```php
Route::get('user/{id}', function ($id) {
  return "User id: $id";
})
```
- Tuy nhiên nếu ta ko truyền $id vào trong uri thì sẽ bị lỗi 404 dội về. Để truyền vào một param không bắt buộc ta thêm dấu `?` vào sau param string và thêm giá trị default cho callback để ko bị lỗi code. VD:
```php
Route::get('user/{id?}', function ($id = null) {
  if(!$id) {
    return "enter id plz";
  }

  return "User id: $id";
})
```
- Sử dụng nhiều param trong route
```php
Route::get('user/{id}/comment/"\{commentId}', function ($id, $commentId) {
  return "User id: $id and comment id: $commentId";
});
```
- Khi muốn định dạng kiểu dữ liệu hay pattern của param thì cần sử dụng phương thức `where` với cú pháp
```php
where($name, $pattern)
//or
where([$name => $pattern])
```
- VD ta muốn id, commentId bắt buộc phải là số
```php
Route::get('user/{id}/comment/{commentId}', function ($id, $commentId) {
  return "User id: $id and comment id: $commentId";
})->where('id','[0-9]+')->where('commentId', '[0-9]+');

//or
Route::get('user/{id}/comment/{commentId}', function ($id, $commentId) {
  return "User id: $id and comment id: $commentId";
})->where(['id' => '[0-9]+', 'commentId' => '[0-9]+');
```
- Laravel có cung cấp một số các `where` kèm với regex phổ biến như
  - whereNumber($name)
  - whereAlpha($name)
  - whereAlphaNumeric($name)
  - whereUuid($name)
- Nếu muốn định nghĩa pattern cho tất cả các route param name nào đó, có thể sử dụng `Route::pattern($name, $pattern)`. Lúc này tất cả các route param được khai báo và thực thi sau Route::pattern sẽ áp dụng. Để hoạt động tốt với tất cả các loại route thì ta nên khai báo trong phương thức `boot` tại `app/Providers/RouteServiceProvider.php`
- VD tất cả các route có param name là `id` sẽ đều phải là số:
```php
public function boot()
{
  Route::pattern('id', '[0-9]+)
}
``` 
# 2.2 Dependency injection với route
- Dependency injection là một thuật ngữ, chỉ việc giải quyết các phụ thuộc của một object thông qua constructior. Trong laravel dependencty injection xuất hiện ở mọi nơi, với callback trong route cũng có.
```php
Route::get('/users', function (Request $req){
  //code
});
```
- Đối với các route có param ta sẽ phải để các tham số `dependency injection` ở phía trước, các param sẽ đặt sau các dependency.
```php
Route::get('/user/{id}/comment/{commentId}', function (Request $request, $id, $commentId) {
  return "User id: $id and comment id: $commentId";
})->where(['id' => '[0-9]+', 'commentId' => '[0-9]+');
```
# 2.3 Đặt tên cho route:
- Ta có thể đặt tên cho route để tiện quản lý và generate ra route URL. 
- Để gán tên cho route ta chỉ cần sử dụng phương thức `name hoặc as` với cú pháp
```php
name($name)
// or
as($name)
```
- VD: đặt tên cho route có path là `user/profile` tên là profile
```php
Route::get('/user/profile', function () {
  //code
})->name('profile');
```
- Để đặt name prefix cho tất cả các route trong group ta cũng sử dụng phương thức `as hoặc name` với cú pháp tương tự. VD
```php
//1. voi as
Route::prefix('user')->as('user.')->group(function () {
  Route::get('profile', function () {
    //code
  })->name('profile');

  Route::get('setting', function () {
    // code
  })->name('setting');
});

//2. voi name
Route::prefix('user')->name('user.')->group(function () {
  Route::get('profile', function () {
    //code
  })->name('profile');

  Route::get('setting', function () {
    // code
  })->name('setting');
});
```
# 2.4 Generate URL sử dụng route name:
- Để tạo URL qua route ta sử dụng phương thức `route()` với cú pháp: `route($name, $parameter)`. Trong đó:
  - $name: là tên của route
  - $param là một mảng chứa các param truyền vào route. Nếu route không có param thì nó sẽ trở thành URL query string.
- VD:
```php
//routes/web.php
Route::get('/welcome', function () {
  //
})->name('welcome');

Route::prefix('user')->as('user.')->group(function () {
  Route::get('profile', function () {
    //
  })->name('profile');

  Route::get('post/{id}', function ($id) {
    //
  })->name('show.post');
});

// ung dung generate URL qua route
route('welcome');
//http:://miki.vn/welcome

route('user.profile');
//http:://miki.vn/user/profile

route('user.show.post', ['id' => 10]);
//http:://miki.vn/user/post/10

route('user.profile', ['id' => 10])
//http:://miki.vn/user/profile?id=10
```
# 3.1 Middleware Route
- Middleware là một khái niệm chỉ việc cung cấp các bộ filter HTTP request cho ứng dụng. Để thêm middleware cho một route, ta sử dụng phương thức `middleware($middleware)`. Trong đó $middleware là một string array chứa middleware name.
- VD thêm middleware auth cho request /user/profile
```php
Route::middleware(['auth'])->group(function () {
  Route::get('/user/profile', function () {
    //
  });
});
```
# 3.2 Subdomain Route
- Khi muốn sử dụng route cho subdomain thì sử dụng phương thức `domain` với cú pháp `domain($domain)`. VD
```php
Route::domain('{account}.miki.vn')->group(function () {
  Route::get('/user/{id}', function () {
    // 
  });
});
```
# 3.3 Binding Model trong route
- Laravel tự động resolve eloquent model nếu ta định nghĩa chúng trong route hoặc phương thức trong controller.
- VD: auto resolve User theo route
```php
use App\Models\User;

Route::get('/users/{user}', function (User $user) {
  return $user->email;
})
```
- Lúc này khi ta truyền {user} thì laravel sẽ resolve model với {user} tương ứng với primary key trong model và trả về 404 nếu {user} ko có trong DB.
# 3.4 CSRF token trong route
- Tấn công giả mạo chính chủ thể của nó - Cros-site Request Forgery
- Mặc định trong laravel đã enable middleware VerifyCsrfToken, vì thế tất cả các request khác HEAD, GET & POST đều cần phải có token kèm theo. Nếu ko có sẽ trả về http status 419.
- Laravel cho phép binding token vào request qua một trong các cách sau:
  - Truyền vào trong http header với header name là X-XSRF-TOKEN
  - Truyền vào trong parameter string với name là _token.
- Để sinh CSRF token trong laravel ta sử dụng hàm `csrf_token()`. Nếu cần sinh ra html input token thì có thể sử dụng hàm `csrf_field()`.
- VD với `csrf_token()`
```php
<form action="/miki" method="POST">
  <input type="hidden" name="_method" value="PUT">
  <input type="hidden" name="_token" value="{{ csrf_token() }}">
</form>
```
- VD with `csrf_field()`
```php
csrf_field(); //tu tao ra input hidden with name _token
```
- Ngoài ra có thể sử dụng blade directive để sinh ra token_field() với directive @scrf. VD
```php
<form action="/miki" method="POST">
@csrf
</form>
```
# 3.5 Route cache.
- Laravel xử lý route tương đối nặng và cồng kềnh thế nên laravel cung cấp 1 feature cho phép ta cache lại thông tin route vào một file. File này nằm trong bootstrap/cache/. Để cache route ta sử dụng command `php artisan route:cache`.
- Khi route đã được cache, laravel sẽ load data trong file cache ra mà ko quan tâm đến file route nữa. Để apply lại route mới ta có thể run lại lệnh cache một lần nữa, hoặc clear cache rồi cache lại. 
- Để clear cache sử dụng lệnh `php artisan route:clear`;
# 3.6 Hiển thị danh sách route
- Để hiện thị danh sách route đã được đăng ký trong laravel sử dụng câu lệnh `php artisan route:list`. 
- Có thể thêm các option sau:
  + --columns=[Column] chọn các cột hiển thị
  + --compact chỉ hiện thị cột method, URI và action
  + --json xuất data dưới dạng json
  + --method=[Method] lọc các route theo method
  + --name=[NAME] lọc theo route name
  + --path=[PATH] lọc theo path
  + --reverse đảo ngược thứ tự sắp xếp route
  + --sort=[SORT] sắp xếp route theo column. Default là sx theo URI.
# 3.7 Lấy route hiện tại
- Khi cần lấy route hiện tại, ta có thể sử dụng 3 phương thức
  - Route::current() - lấy ra current route dạng Object (Illuminate\Routing\Route)
  - Route::currentRouteName() - lấy ra current route name. Nếu khônh set name thì nó sẽ là null
  - Route::currentRouteAction() - lấy ra current action của route.