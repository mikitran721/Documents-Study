# Tổng hợp

- Phần này là phần IV nói về Blade template, gồm các nội dung
  1 Xây dựng layout sử dụng kế thừa trong Blade
  2 Form trong Blade
  3 Hiển thị validate error message trong Blade template
  4 Raw PHP

# 1. Xây dựng layout sử dụng kế thừa trong Blade

## 1.1 Định nghĩa layout

- Để định nghĩa một layout trong Blade ta khai báo view như bình thường (hay gom vào views/layout/...blade.php). VD

```php
// file: resources/views/layout/app.blade.php
<title>App name - @yield('title')</title>
<body>
    @section('sidebar')
        // dữ liệu ngắn, đơn giản.
        This is the master sidebar.
    @show

    <div class="container">
        @yield('content')
    </div>
</body>
```

- Trong đó:
  - @yield directive dùng để chỉ định section có name trong yield sẽ được hiển thị.
  - @section directive dùng để định nghĩa nội dung của section.

## 1.2 Kế thừa layout

- Sau khi đã tạo được layout rồi, để kế thừa một layout trong Blade template ta sử dụng cú pháp `@extends('view.name')`. Trong đó `view.name` là view ta muốn kế thừa.
- `@section` sẽ chỉ định nội dung được hiển thị `@yield` tương ứng trong layout. Trong TH nội dung truyền vào section là dữ liệu ngắn đơn giản thì có thể sử dụng cú pháp dưới đây. Trong đó:
  - name là tên của section ta muốn truyền vào layout cha.
  - Data body là dữ liệu ta muốn truyền vào section.

```php
@section('name')
'Data body'
@endsection
```

- VD

```php
@extends('layouts.app')

//dinh nghia cho @yield('title')
@section('title','Page title')

// them vao section 'sidebar' o layout
@section('sidebar')
    @parent

    <p>This is appeneded to the master sidebar.</p>
@endsection

// dinh nghia cho @yield('content')
@section('content')
    <p>This is the body content.</p>
@section
```

- Trong một vài trường hợp, ta muốn xác định giá trị mặc định sẽ hiển thị khi không có data truyền vào trong @yield thì có thể sử dụng cú pháp `@yield('name', 'Default content')`

# 2. Form trong Blade template

- Laravel cung cấp sẵn một số directive hỗ trợ việc tạo các method, csrf token. Để thêm một input field hidden chứa csrf_token trong form ta có thể sử dụng directive `@csrf`. VD

```php
<form method="POST" action="/profile">
    @csrf
    @method('PUT')
</form>
```

- Ngoài ra ta có thể sử dụng directive `@method` để thêm một input hidden chứa method của form (vì HTML form không thể tạo được method PUT, PATCH, DELETE)

# 3. Sử dụng validate error message trong Blade template

- Ta có thể sử dụng `@error` directive để thực hiện việc hiển thị message lỗi vủa validation trả về. VD:

```php
<label for="title">Post title</label>
<input id="title" type="text" class="@error('title') is-invalid @enderror"

@error('title')
    <div class="alert alert-danger"> {{ $message }} </div>
@enderror
```

# 4. Raw PHP

- Trong một số trường hợp, ta cần sử dụng code PHP chứa logic code trong blade template, ta có thể dụng sử dụng directive `@php` với cú pháp:

```php
@php('code php')
//hoac

@php
// code php
@endphp
```

- VD:

```php
@php
    $msg = "Love PHP";
@endphp

{{ $msg }}
```
