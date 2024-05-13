# Tóm tắt

- Tùy chọn dùng với controller

  - `--invokeable` tạo single controller
  - `--resource` tạo resource controller
  - `--model=ModelName` binding với Model

- Ta có thể viết code logic vào trong call back function của Route, nó sẽ dễ hình dung logic. Nhưng trên thực tế, ứng dụng sẽ có rất nhiều Route, lúc này nếu như code logic mà đặt trong Route luôn thì file sẽ rất lớn và code lúc này sẽ trở nên rối hơn.
- Trong Laravel, Controller sẽ là nơi chứa các logic code thay thế cho Route. Lúc này ta có thể gom nhóm một loạt các logic có chung một nghiệp vụ vào một class cho dễ quản lý.
- VD: Controller UserController sẽ chứa các logic code liên quan đến user như: hiển thị, thêm mới, update, xóa,...
- Mặc định, Controller trong Laravel được đặt trong thư mục `app/Http/Controllers`
- Nội dung gồm:
  1. Tạo Controller
  2. Single Action Controller
  3. Middleware trong Controller.
  4. Resource Controller.
  5. API Resource Routes.

# 1. Tạo Controller

- Trong Laravel tất cả các Controller đều phải kế thừa class Controller (App\Http\Controllers\Controller). Nếu không kế thừa class Controller này thì ta sẽ không sử dụng được các tính năng kèm theo của Laravel như `middleware, authorize,...` bằng việc gọi phương thức.
- Để tạo mới một Controller trong Laravel ta sử dụng artisan command hoặc có thể tạo bằng tay. Câu lệnh như sau:

```php
php artisan make:controller ControllerName
```

- VD: Tạo controller với name là ContactController.

```php
php artisan make:controller ContactController
```

- Lúc này Laravel sẽ generate một file ContactController.php nằm trong thư mục `app/Http/Controllers`.
- Trong TH muốn tạo controller trong một thư mục con nằm trong app/Http/Controllers thì ta cần điền chèn thêm path chứa thư mục con đó kèm theo controller name. VD: Tạo mới controller ContactController trong thư mục app/Http/Controllers/Pages.

```php
php artisan make:controller Pages/ContactController
```

- Lúc này ở Route ta muốn assign logic cho Controller, ta truyền tham số thứ 2 là một mảng với tham số 1st là class path của controller, tham số thứ 2 là method thực thi.
- VD: thêm method `show` vào trong ContactController để hiện thị view & assign vào route với path /contact

```php
// file: app/Http/Controllers/ContactController.php
public function show()
{
  return view('contact.form');
}

// file: routes/web.php
Route::get('/contact', [ContactController::class, 'show']);
```

# 2. Single Action Controller

- Trong một vài trường hợp, nếu ta chỉ muốn một controller class thực thi một hành động duy nhất, thì ta có thể thêm một phương thức rồi assign chúng vào route. Hoặc ta có thể viết chúng trong phương thức `__invoke` rồi trong route ta sẽ không cần phải truyền thêm phương thức.
- VD: Single action Controller

```php
// file: app/Http/controllers/ProvisionServer.php
public function __invoke()
{
  // ...
}

// routes/web.php
Route::get('/contact', ProvisionServer::class);
```

- Đối với TH muốn tạo một controller như trên, ta có thể truyền thêm tham số `--invokable` khi chạy `make:controller` artisan command.

# 3. Middleware trong Controller

- Trong Laravel ta có thể assign middleware vào trong controller với method `middleware`. VD: assign middleware auth vào trong controller UserController

```php
public function __construct()
{
  $this->middleware('auth');
}
```

- Nếu ta chỉ định middleware cho một vài method nào đó trong controller, ta có thể đặt middleware trong method đó, hoặc sử dụng phương thức `only`.
- VD: middleware cho method edit trong UserController

```php
// #c1
public function __construct()
{
  $this->middleware('auth')->only('edit');
}

public function edit()
{
  //code
}

// #c2
public function edit()
{
  $this->middleware('auth');
}
```

- Nếu ta cần middleware hoạt động với tất cả action trong controller và bỏ qua một vài action nào đó, ta có thể sử dụng phương thức `except`. VD: assign middleware cho controller UserController và bỏ qua cho action index & show

```php
$this->middleware('auth')->except(['index', 'show']);

//or
$this->middleware('auth')->except('index', 'show');
```

# 4. Resource Controller

- Resource controller là một dạng controller cung cấp sẵn các action: index, create, store, show, edit, update, destroy để thực thi các hành động CRUD data.
- Để tạo resource controller ta sử dụng command:

```php
php artisan make:controller ControllerName --resource
```

- Lúc này để assign route controller ta sử dụng phương thức resource

```php
Route::resource('photos', PhotoController::class);
```

- Để xem danh sách các route, sử dụng command

```php
php artisan route:list
```

- Trong TH ta muốn sử dụng thêm route model binding trong route thì khi tạo controller có thể sử dụng command

```php
php artisan make:controller ControllerName --resource --model=ModelName
```

- Trong một số TH, ta ko muốn sử dụng tất cả các action trong controller thì ở route ta sử dụng phương thức `except` để xác định các action không muốn sử dụng.
- VD: loại bỏ action index & show trong controller PhotoController

```php
Route::resource('photos', PhotoController::class)->except(['index', 'show']);
```

- Hoặc ta có thể sử dụng phương thức `only` để xác định action được phép sử dụng. VD: chỉ sử dụng action index & show trong controller PhotoController

```php
Route::resource('photos', PhotoController::class)->only(['index', 'show']);
```

- Trong một số TH, ta muốn thay đổi route name cho một action nào đó trong resource controller, ta có thể sử dụng phương thức `names`. VD: thay đổi route name cho action `create` thành `photos.build`

```php
Route::resource('photos', PhotoController::class)->names([
  'create' => 'photos.build'
]);
```

- Ta cũng có thể thay đổi route parameters cho resource controller, bằng cách sử dụng phương thức `parameters`. VD: thay đổi tham số photo thành photo_id.

```php
Route::resource('photos', PhotoController::class)->parameters([
  'photo' => 'photo_id'
])
```

# 5. API resource Routes

- Trong TH ứng dụng của ta chỉ cần cung cấp action như một API result, thì ta có thể sử dụng API resource route. Lúc này các route sẽ bỏ qua các action `create, edit`.
- VD: chuyển PhotoController trên về API resource route

```php
Route::apiResource('photos', PhotoController::class);
```
