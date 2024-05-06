# Tổng quát

- Đây là phần V (phần cuối) về Blade template. Trong phần này sẽ có các nội dung:
  1. Stack trong Blade
  2. Service Injection
  3. Tạo directive trong Blade
  4. Tạo mới câu lệnh if trong Blade
  5. Cache view trong Laravel

# 1. Stack trong Blade

- Blade cung cấp cho ta directive `@push`[sử dụng ở view] để thực thi việc đẩy các data vào `@stack` [sử dụng ở layout] trong view. Có thể hiểu nôm na là mỗi khi ta sử dụng @push thì data đó sẽ được đẩy thêm vào @stack. VD:

```php
// file: resources/views/layouts/app.blade.php
<title>App name - @yield('title','Default title') </title>
<body>
    <div class="container">
        @yield('content')
    </div>

    @stack('scripts')
</body>

// file: resources/views/home.blade.php
@extends('layouts.app')

@push('scripts', '<script> alert("Hello with stack") </script>')

@push('scripts')
    <script>
        alert("Hello again")
    </script>
@endpush

// file: routes/web.php
Route::get('/', function () {
    return view('home');
})->name('home');
```

- Mỗi lần sử dụng `@push` ở view thì data sẽ được đẩy xuống dưới @stack ở layout. Tuy nhiên trong một số trường hợp, ta muốn đẩy lên phía đầu của @stack thì ta có thể sử dụng directive `@prepend`. VD:

```php
// file: resources/views/home.blade.php
@extends('layouts.app')

@prepend('scripts','<script> alert("Hello 1") </script>')

@prepend('scripts')
    <script>
        alert("Hello 2")
    </script>
@endprepend
```

# 2. Service Injection trong Blade template

- Nếu như muốn inject một `service, object` nào đó trong Blade. Ta có thể sử dụng `@inject` directive với cú pháp: `@inject('variableName', 'ClassPath')`. Trong đó:
  - variableName là biến sẽ được assign khi inject thành công.
  - ClassPath là namespace của class ta muốn inject.
- VD:

```php
@inject('metrics', 'App\Services\MetricsService')

<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}
</div>

// code tương đương khi ko dùng @inject
@php($metrics = app(App\Services\MetricsService::class))
<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}
</div>
```

# 3. Tạo directive trong Blade

- Trong một số trường hợp, ta muốn tạo thêm directive của riêng mình thì Laravel cũng hỗ trợ ta tạo mới directive với cú pháp:

```php
// file app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\Blade;

Blade::directive($directiveName, function ($value) {
    // code
});
```

- Trong đó:
  - $directiveName là tên directive ta muốn tạo.
  - $value là giá trị truyền vào khi sử dụng directive.
- Lưu ý: Để việc tạo mới directive hoạt động tốt nhất có thể ta nên đưa code vào trong provider.
- VD: Tạo mới directive in ra date với format "d-m-y h:i"

```php
// file: app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\Blade;

//...
public function boot()
{
    Blade::directive('datetime', function ($expression) {
        return "<?php echo ($expression)->format('d-m-y h:i'); ?>"
    })
}

// file: resources/views/home.blade.php
@datetime(now())

// file: routes/web.php
Route::get('/', function() {
    return view('home');
})->name('home');
```

# 4. Tạo mới câu lệnh if trong Blade template

- Tương tự như đối với directive thông thường, ta có thể tạo mới `if directive` cho riêng mình bằng cách sử dụng `if mehtod` trong `Blade object`. VD:

```php
// file: app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\Blade;

public function boot()
{
    Blade::if('disk', function ($value) {
        return config('filesystem.default') === $value;
    });
}

// file: resources/views/home.blade.php
@disk('local')
//app is using the local disk
@elsedisk('s3')
// app is using s3 disk
@else
// app is using some other disk
@enddisk

@unlessdisk('local')
    //app is not using the local disk
@enddisk
```

# 5. Cache view trong Laravel

- Mặc định Laravel luôn cache lại các bản view được compile vào trong `storage/framework/views` sau đó mỗi request gọi đến laravel sẽ kiểm tra xem cache đó đã tồn tại hoặc hết hạn chưa? Nếu cache đã hết hạn (time đổi file blade lớn hơn thời gian thay đổi file cache) hoặc file cache chưa tồn tại thì Laravel sẽ thực hiện compile blade và cache lại.
- Quá trình compile này có thể làm cho ứng dụng chậm đi một chú. Chính vì điều này Laravel cung cấp cho ta 2 command để cache và clear cache view trong một số trường hợp cần thiết.

```php
Cache: php artisan view:cache

Clear Cache: php artisan view:clear
```
