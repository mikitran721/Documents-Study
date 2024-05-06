# 3.1 Nhúng view khác vào trong Blade

## @include

- Blade template cho phép ta có thể nhúng các view khác vào trong view hiện tại một cách đơn giản bằng vc sử dụng `@include directive`. Lúc này ở các view được nhúng cũng có thể sử dụng được tất cả các biến có trong view hiện tại.
- VD:

```php
//file: resources/views/shared/notify.blade.php
<div class='alert'> {{ $alertMessage }} </div>

// file: resources/views/home.blade.php
<h1> {{ $title }} </h1>
@include('shared.notify')

// file: routes/web.php
Route::get('/', function() {
    return view('home', ['title' => 'Laravel', 'alertMessage' => "This is a message"]);
})->name('home');
```

- Trong trường hợp trên view notify.blade.php đã sử dụng biến $alertMessage được truyền vào trong view home.blade.php từ routes. Trong trường hợp muốn đưa thêm data vào view được include từ view gọi thì có thể sử dụng cú pháp: `@include('view.name', [$variableName => $data])`. Trong đó:
  - $variableName là tên biến chứa data ta muốn truyền vào subviews.
  - $data là giá trị của $variableName.
- VD:

```php
// file: resources/views/shared/notify.blade.php
<div class="alert {{ $alertType }} "> {{ $alertMessage }} </div>

// file: rescources/views/home.blade.php
<h1> {{ $title }} </h1>
@include('shared.notify', ['alertType' => 'alert-warning']);

// file: routes/web.php
Route::get('/', function () {
    return view('home', ['title' => 'PHP', 'alertMessage' => 'A message'])
})->name('home');
```

## @includeIf

- Trong blade nếu ta @include một view không tồn tại Laravel sẽ throw ra một error. Nếu muốn bỏ qua exception đó thì có thể dùng `@includeIf`. Directive này sẽ check nếu như view tồn tại thì mới thực thi việc include view. Cách sử dụng thì tương tự `@include`. VD

```php
// file: resources/views/home.blade.php
<h1> {{ $title }} </h1>
@includeIf('shared.notify', ['alertType' => 'alert-warning']);
```

## @includeWhen

- Trong một số trường hợp ta cần kiểm tra điều kiện trước khi nhúng view thì có thể sử dụng `@includeWhen` với cú pháp:

```php
@includeWhen($boolean, 'view.name', $data);
```

- Trong đó: $boolean trả về `true` thì sẽ include được view và false thì ngược lại.

## @includeUnless

- Đây là directive phủ định của `@includeWhen`. Nghĩa là $boolean trả về true thì view sẽ không được include và ngược lại `false` thì sẽ được include. Cú pháp:

```php
@includeUnless($boolean, 'view.name', $data);
```

## includeFirst

- Directive này cho phép ta truyền vào một list views. Và nó sẽ kiểm tra xem nếu view nào tồn tại đầu tiên trong danh sách thì nó sẽ nhúng vào view gọi nhúng, khi đó các view phía sau sẽ không được nhúng nữa. Cú pháp:

```php
@includeFirst(['view.name', 'view.name1', 'view.name2'], $data)
```

## Nhúng view trong vòng lặp

- Ta có thể nhúng view qua mỗi lần lặp một mảng hay một collection trong Blade template với directive `@each` với cú pháp như dưới đây: <áp dụng tương tự component với thành phần gọi <li> trong React>

```php
@each('view.name', $array, item, 'view.empty')
```

- Trong đó:
  - view.name: là view ta muốn nhúng vào view hiện tại
  - $array là mảng, collection data ta muốn lặp
  - item: là giá trị sẽ được assign qua mỗi lần lặp.
  - view.emptyL là view sẽ được nhúng khi $array rỗng - nó có bể bỏ trống.
- VD:

```php
// file: resources/views/shared/post.blade.php
<div class="post-item">
  <h3> {{ $post['name'] }} </h3>
</div>

// file: resources/views/home.blade.php
<h1>List posts</h1>
<div class="list-post">
  @each('shared.post', $posts, 'post')
</div>

// file: routes/web.php
Route::get('/', function () {
  $posts = [
    ['name' => 'Post 1'],
    ['name' => 'Post 2'],
    ['name' => 'Post 3'],
    ['name' => 'Post 4'],
  ];

  return view('home', ['posts' => $posts]);
})->name('home');
```

## @once

- Directive này cho phép chúng ta thực thi hành động bên trong nó một lần duy nhất khi render view. VD có 2 chỗ ta cùng nhúng một đoạn code nếu như sử dụng directive `@once` thì các đoạn code phía sau @once đầu tiên sẽ không được thực thi nữa. VD:

```php
@once
  @push('scripts')
    <script>
      //code js
    </script>
  @endpush
@endonce
```

# 4. Xây dựng layout sử dụng component trong Blade

- Hầu hết các UD web hiện nay đều phân chia bố cục theo các layout riêng rồi ghép chúng lại với nhau khi cần sử dụng đến. Làm như vậy code phần view sẽ dễ đọc hơn, dễ maintaince hơn.
- Laravel cũng hỗ trợ ta xây các component riêng rồi nhúng nó vào khi cần dùng.

## 4.1 Định nghĩa component trong Blade template

- Để khai báo một component cơ bản, ta chỉ cần khai báo chúng ở trong path `resources/views/components`.VD:

```php
// file: resources/views/components/todo.blade.php
<title> {{ $title ?? 'Todo manager' }} </title>
<hr/>
{{ $slot }}
```

- Trong đó: `$slot` là nơi sẽ hiển thị nội dung truyền vào component khi chúng được gọi ở view khác.

## 4.2 Gọi component trong Blade template

- Sau khi đã khai báo được component, nếu muốn gọi chúng ở trong view thì có thể sử dụng cú pháp:

```php
<x-componentname>...</x-componentname>
// hoac
<x-componentname />
```

- VD: gọi và render component todo.blade.php

```php
// file: resources/views/home.blade.php
<x-todo>
//đống này sẽ hiện thị tại $slot trong component
  @foreach($tasks as $task)
    <h3> {{ $task['name'] }} </h3>
  @endforeach
</x-todo>

// file: routes/web.php
Route::get('/', function() {
  $tasks = [
    ['name' => 'Task 1'],
    ['name' => 'Task 2'],
    ['name' => 'Task 3'],
    ['name' => 'Task 4'],
  ];

  return view('home',['tasks' => $tasks]);
})->name('home');
```

- Trong một số trường hợp, ta muốn truyền data vào $title trong todo.blade.php (hay là truyền biến từ view vào component) thì ta sử dụng cú pháp sau tại view:

```php
<x-slot name="VariableName">
  Data
</x-slot>
```

- Trong đó:
  - VariableName là tên biến trong component ta muốn truyền vào data.
  - Data là dữ liệu ta muốn truyền vào.
- VD: bind title vào component todo.blade.php

```php
<x-todo>
  // dong nay là gán giá trị cho $title trong component của todo.blade.php
  <x-slot name="title">
    The title hehe.
  </x-slot>
  @foreach ($tasks as $task)
    <h3> {{ $task['name'] }} </h3>
  @endforeach
</x-todo>
```
