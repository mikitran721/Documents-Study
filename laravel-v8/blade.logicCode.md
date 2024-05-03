# 2.1 If trong Blade template:

- Trong blade template, ta có thể triển khai câu lệnh `if trong PHP` với các `directive: @if, @else, @elseif & $endif`.
- VD

```php
@if (count($records) === 1)
    I have 1 record
@elseif (cout($records) > 1)
    I have multiple records.
@else
    No record.
@endif
```

- Ngoài ra Blade còn cung cấp thêm một số các directive rút gọn khác như:

## @unless

- Nếu logic trong unless trả về false thì code bên trong sẽ được thực thi. VD:

```php
@unless (Auth::check())
    You not signed in.
@enunless
```

## @isset

- Kiểm tra một biến có tồn tại hay không. Logic tương tự `if(isset())` trong PHP. VD

```php
@isset($records)
// $records is defined and not null
@endisset
```

## @empty

- Kiểm tra xem một biến có tồn tại hoặc = null hay kô. Logic tương tự `if (empty())` trong PHP. VD:

```php
@empty($records)
// $records is empty
@endempty
```

## @auth

- Kiểm tra xem user đã login hay chưa. Nếu user login rồi thì code bên trong sẽ thực thi. VD

```php
@auth
// The user is authenticated
@endauth
```

- Nếu cần xác thực theo `guard` khác thì có thể truyền guard name vào trong directive. VD: check theo guard admin.

```php
@auth('admin')
// The user is authenticated
@endauth
```

## @guest

- Kiểm tra user đã login hay chưa. Nếu user chưa login thì code bên trong sẽ được thực thi. VD:

```php
@guest('admin')
// The user is not login
@endguest
```

## @production

- Kiểm tra xem môi trường có phải production hay kô. Nếu là production thì code bên trong sẽ được thực thi. VD

```php
@production
// Production environment
@endproduction
```

## @env

- Ngoài ra, ta có thể check `env` theo tên môi trường truyền vào. Nếu đúng code bên trong cặp directive sẽ được thực thi. VD:

```php
@env('staging')
// the app is runing in 'staging'
@endevn

$env(['staging', 'production'])
// the app is running in 'staging' or 'production'
@endenv
```

## @hasSection

- Kiểm tra xem trong template cha có tồn tại section nào không. Nếu có code bên trong sẽ được thực thi. VD:

```php
@hasSection('navigation')
    <div class='pull-right'>
        @yield('navigation')
    </div>
@endif
```

## @sectionMissing

- Đây là trường hợp ngược lại @hasSection. (dùng khi section cần kiểm tra không tồn tại). VD

```php
@sectionMissing('navigation')
    <div class='pull-right'>
        @include('default-navigation')
    </div>
@endif
```

# 2.2 Switch trong Blade template:

- Đối với câu lệnh switch ta có thể sử dụng các directive `@switch, @case, @break, @default & @endswitch` với ý nghĩa tương tự switch, case, break, default và endswitch trong PHP. VD

```php
@switch($i)
    @case(1)
        First case
        @break
    @case(2)
        Second case
        @break
    ..
    @default
        Default case
@endswitch
```

# 2.3 Vòng lặp trong Blade template

- Để sử dụng các vòng lặp trong blade ta chỉ cần thêm @vào trước các câu lệnh, riêng đối với do-while thì Blade ko hỗ trợ. VD

```php
@for ($i = 0; $i < 10; $i++)
    The current value is {{ i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No user</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```

- Trong vòng lặp trong Blade ta cũng có thể sử dụng các directive `@continue, @break` tương ứng với `continue, break` trong PHP. VD:

```php
@foreach ($users as $user)
    @if ($user->type ==1)
        @continue
    @endif

    <li> {{ $user->name }} </li>

    @if ($user->number ==5)
        @break
    @endif
@endforeach
```

- Trong quá trình compile vòng lặp trong blade template, Laravel có thực hiện thêm một biến `$loop` vào để ta có thể truy xuất các thông tin vòng lặp, như: lần lặp hiện tại, kiểm tra lần lặp cuối, lần lặp đầu tiên. VD

```php
@foreach ($users as $user)
    @if ($loop->first)
        This is the first iteration.
    @endif

    @if ($loop->last)
        This is the last iteration.
    @endif

    <p>This is user {{ $user->id }} </p>
```

- Đối với trường hợp có nhiều vòng lặp lồng nhau ta có thể trỏ đến `parent` để lấy thông tin vòng lặp bên ngoài. VD:

```php
@foreach ($users as $user)
    @foreach ($user->posts as $post)
        @if ($loop->parent->first)
            This is first iteration of the parent loop.
        @endif
    @endforeach
@endforeach
```

- Danh sách các thuộc tính trong object `$loop`:
  - $loop->index: Lấy ra chỉ mục (index) của vòng lặp hiện tại, start=0
  - $loop->iteration: lấy ra lần lặp hiện tại là lần thứ mấy (start=1)
  - $loop->remaining: Vòng lặp còn phải lặp thêm bao nhiêu lần nữa
  - $loop->count: Lấy ra số lượng item của vòng lặp
  - $loop->first: Xem có phải lần lặp đầu tiên ko
  - $loop->last: Xem có phải lần lặp cuối ko
  - $loop->even: check xem lần lặp này có phải chẵn ko
  - $loop->odd: check xem lần lặp này có phải lẻ ko
  - $loop->depth: Mức độ lồng của vòng lặp hiện tại.
  - $loop->parent: Truy xuất đến thông tin của vòng lặp cha.
