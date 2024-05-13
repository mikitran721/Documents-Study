# Mục lục

- Tất cả các route hay controller trong Laravel đều phải trả về một response. Laravel có cung cấp sẵn một Response Class để hỗ trợ trả về các loại response data một cách đơn giản nhất.

1. Response Data
2. Redirect Response
3. File Response

# 1. Response Data

- Mặc định, khi một Route | Controller trả về giá trị là chuỗi thì Laravel sẽ tự động convert nó về dạng một HTTP response. [Content-Type: text/html; charset=UTF-8].
- Ngoài ra nếu ta trả về một mảng thì Laravel cũng sẽ tự convert nó về dạng một JSON response. [Content-Type: application/json].
- Tuy nhiên, trong thực tế ta lại cần trả về response với các header, status khác nhau. Lúc này ta có thể sử dụng Response Class (Illuminate\Http\Response) của Laravel. Đồng thời Laravel cũng có đưa Response Class vào Helpers. Nên ta hoàn toàn có thể sử dụng hàm `response()` trong Helpers thay cho Response Class để code được ngắn gọn hơn. VD:

```php
Route::get('/', function () {
    return response('Hello', 200)->header('Content-Type', 'text/plain');
    // 200: la http code tra ve
});

// Hoac
Route::get('/', function () {
    return response()->json([1, 3, 5]);
});
```

- Để đưa thêm header vào trong response, ta có thể sử dụng phương thức header với cú pháp: `header($headerName, $headerContent)`. VD:

```php
return response($content)
            ->header('Content-Type', $type)
            ->header('X-Header-One', 'Header value')
            ->header('X-Header-Two', 'Header value');
// or
return response($content)
            ->withHeaders([
                'Content-Type', $type,
                'X-Header-One', 'Header value',
                'X-Header-Two', 'Header value'
            ]);
```

- Nếu muốn thêm cookie vào response ta có thể sử dụng phương thức `cookie` với cú pháp truyền vào tương tự đối với hàm `setcookie`. VD:

```php
return response('Hello PHP')->cookie(
        'name', 'value', $minutes
);
```

- Nếu muốn xóa một cookie nào đó trong response có thể sử dụng phương thức `withoutCookie`. VD:

```php
return response('Hello PHP')->withoutCookie('name');
```

# 2. Redirect Response

- Đối với TH ta muốn trả về một redirect response, Laravel cũng có sẵn một class `Illuminate\Http\RedirectResponse` để làm diều đó. Đồng thời Laravel cũng đưa nó vào helper để tiện cho việc sử dụng. Chúng ta có thể sử dụng helper `redirect` thay cho class trên.
- VD: Redirect về URL `/home/dashboard` khi người dùng vào URL `/dashboard`

```php
Route::get('/dashboard', function () {
    return redirect('home/dashboard');
});
```

- Trong TH muốn redirect về URL user vừa truy cập trước đó, ta dùng hàm `back`. VD:

```php
Route::post('/user/profile', function () {
    // validate the request

    return back()->withInput();
})
```

- Ta có thể redirect đến một route nào đó bằng cách sử dụng phương thức `route`. VD:

```php
return redirect()->route('login');
```

- Nếu như cần redirect sang một domain khác, ta có thể sử dụng phương thức `away`. VD: redirect tới google

```php
return redirect()->away('https://www.google.com');
```

# 3. File response

- Nếu ta cần response về một file, ta có thể sử dụng phương thức `download` với cú pháp

```php
return response()->download($pathToFile);

// or
return response()->download($pathToFile, $name, $headers);
```

- Trong đó:
  - $pathToFile là path đến file muốn trả về.
  - $name là tên của file muốn trả về.
  - $headers là các header muốn trả về kèm theo. Cú pháp giống phương thức `header`.
- Ngoài ra, ta có thể response về một file hiển thị trên trình duyệt như PDF bằng cách sử dụng phương thức `file` với cú pháp:

```php
return response()->file($pathToFile);
// or
return response()->file($pathToFile, $name);
```

- Đôi khi ta muốn trả về một nội dung nào đó có thể dowload được, nhưng lại không muốn ghi chúng vào ổ trước khi download. Ta có thể sử dụng method `streamDownload` để trả về một stream data. VD:

```php
return response()->streamDownload(function () {
    echo GitHub::api('repo')
                    ->contents()
                    ->readme('Laravel', 'Laravel')['conents'];
}, 'laravel-readme.md');
```
