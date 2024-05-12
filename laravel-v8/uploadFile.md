# 1. Store Upload file

- Để lưu trữ file đã được upload lên trên request, ta sử dụng phương thức `store` với cú pháp: `$req->file('photo')->store($path, $diskType)`
- Trong đó:
  - $path: là path mà ta muốn lưu trữ file
  - $diskType là loại disk mà ta muốn lưu trữ. Các thông tin này có thể config ở trong `config/filesystems.php`. Mặc định laravel sẽ sử dụng disk là `local`, loại disk này sẽ lưu trữ thông tin vào trong thư mục `storage/app/`.
  - VD: lưu trữ ảnh vào thư mục `storage/app/images`

```php
$path = $req->file('photo')->store('images');
//hoặc
$path = $req->file('photo')->store('images','local');
```

- Khi ta sử dụng phương thức `store` để lưu trữ file, thì tên file khi được lưu trữ sẽ là một chuỗi ngẫu nhiên 40 ký tự được sinh ra bởi Str::random(40) trong Collection. Nếu muốn đặt tên theo cách của mình thì có thể sử dụng phương thức `storeAs` với cú pháp dưới đây. Trong đó: $path và $diskType tương tự như đối với `store`, $fileName là tên của file mà ta muốn lưu trữ.

```php
$req->file('photo')->storeAs($path, $fileName, $diskType);
// VD:
$req->file('image')->storeAs('images', 'avatar.jpg', 'local');
```

# 2. VD thực tế

- Tạo route để hiển thị và xử lý upload file

```php
// file: routes/web.php
Route::get('/', function () {
    return view('form');
});

Route::post('upload', function (Request $req) {
    if (!req->hasFile('image')) {
        return "Please choose a file";
    }
    $image = $req->file('image');
    $storedPath = $image->storeAs('images', $image->getClientOriginalName());

    return "Success upload";
})->name('upload.handle');
```

- Tạo view để upload file

```php
// file: resources/views/form.blade.php
<body>
    <form action="{{ route('upload.handle') }}" method="POST" enctype="multipart/form-data">
        @csrf
        <input type="file" name="image" />
        <input type="submit" value="Submit" />
    </form>
</body>
```
