# Tổng quan

- HTTP là một mô hình không trạng thái, session đóng vai trò lưu trữ lại thông tin các request để các request tiếp theo có thể truy xuất thông tin được.
- Trong Laravel hỗ trợ rất mạnh về session, có thể sử dụng session qua: database, file, MemCached, Redis,...
- Nội dung gồm:
  1. Cấu hình session
  2. Các yc cho từng session driver: DB, Redis
  3. Tương tác với session
  4. Khởi tại lại session ID.

# 1. Cấu hình session

- Ta có thể cấu hình session trong file `config/session.php`.
- Mặc định Laravel lưu trữ session bằng file, nếu như UD của ta sử dụng `load balance` thì cần config session qua Redis, hoặc DB để các worker có thể connect được đồng thời.
- Laravel hỗ trợ một số session như sau:
  - file: session sẽ được lưu trữ tại `storage/framework/sessions/` -> ta có thể thay đổi được thư mục.
  - cookie: session sẽ được lưu trữ vào cookie và sẽ được mã hóa an toàn trước khi lưu vào cookie.
  - DB: session sẽ lưu trữ vào database
  - memcached/ redis: session sẽ được lưu trữ vào trong bộ nhớ cache của các ứng dụng này.
  - dynamodb: session sẽ được lưu trữ vào trong AWS DynamoDB.
  - array: session sẽ được lưu trữ vào một array trong PHP. Driver này chỉ dùng riêng cho testing.
- Và một số thông số khác:
  - lifetime: time sống của session, tính = phút.
  - expire_on_close: session sẽ được xóa khi user đóng trình duyệt.
  - ecrypt: có mã hóa session trước khi lưu trữ không
  - files: path sẽ lưu trữ session nếu session driver là file.
  - cookie: cookie key name để lưu trữ identify session.

# 2. Các yêu cầu cho từng session driver

## 2.1 Yêu cầu với DB

- Khi ta sử dụng driver là DB, ta cần phải tạo 1 table để lưu trữ thông tin session. Dùng command sau: `php artisan session:table`.
- Khi này Laravel sẽ sinh ra một file migrate `database/migrators/xx_xx_xxxxxx_create_sessions_table.php`. Tiếp đó, ta cần migrate nó vào DB với command `php artisan migrate`.

## 2.2 Yêu cầu với Redis

- Trước khi sử dụng Redis làm driver trong Laravel, ta cần phải cài đặt redis extension trong PHP (sử dụng PECL), hoặc sử dụng `predis/predis` package qua composer. Khuyến khích sử dụng redis extension của PHP để cho performance tốt nhất.

# 3. Tương tác với session:

## 3.1 Lấy dữ liệu

- Trong Laravel, có 2 cách để lấy được session: qua session helper hoặc sử dụng Request instance.
- Để lấy dữ liệu trong session, ta sử dụng phương thức `get`. VD:

```php
//file: UserController.php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Session;

class UserController extends Controller
{
    public function show(Request $req, $id)
    {
        $value = $req->session()->get('key');
        //or
        $value = session('key');
        //or
        $value = session()->get('key');
        //or
        $value = Session::get('key');
    }
}
```

- Trong đó, nếu session key kô tồn tại thì hàm get sẽ trả về null. Nếu muốn thay giá trị mặc định khi key ko tồn tại thì có thể truyền tham số thứ 2 sẽ là giá trị mặc định khi key không tồn tại. VD:

```php
$value = $req->session()->get('key', 'default');

$value = $req->session()->get('key', function () {
  return 'default';
});
```

- Để lấy tất cả session data, ta sử dụng phương thức `all`. VD:

```php
$data = Session::all();
```

## 3.2 Kiểm tra session

- Để kiểm tra một session nào đó có tồn tại hay không, có thể sử dụng phương thức `has`. Phương thức này sẽ trả về true nếu session có tồn tại và khác null. VD:

```php
if ($request->session()->has('users')) {
  // code
}
```

- Nếu chỉ cần kiểm tra xem session có tồn tại hay kô mà không quan tâm giá trị của nó thì có thể sử dụng phương thức `exists`. Phương thức này sẽ trả về true nếu trong session tồn tại key đó. VD:

```php
if ($request->session()->exists('users')) {
  //code
}
```

## 3.3 Lưu trữ Session

- Để lưu trữ session ta sử dụng phương thức `put` với cú pháp `session()->put($key, $value)`. VD:

```php
$sessions = Session::put("cart.products",[['id' => 1, 'item' => 1]]);
```

- Trong TH session key có giá trị là array và ta muốn push thêm data vào session key đó, ta có thể sử dụng phương thức `push`. VD:

```php
use Illuminate\Support\Facades\Session;
$sessions = Session::push("cart.products", ['id' => 2, 'item' => 1]);
```

- Trong TH, ta muốn lấy ra session data xong xóa luôn key đó khỏi session. Có thể sử dụng method `pull`. Ta có thể set thêm giá trị default nếu như session key không tồn tại bằng thêm param thứ 2. VD:

```php
$sessions = Session::pull("cart.products", "default");
```

- Ta có thể tăng giảm giá trị của session bằng cách sử dụng phương thức `increment vs decrement`. VD:

```php
$req->session()->increment('count');
$req->session()->increment('count', $incrementBy = 2);
$req->session()->decrement('count');
$req->session()->decrement('count', $decrementBy = 2);
```

## 3.4 Flash session

- Flash session là một dạng session chỉ tồn tại được trong 1 request rồi sẽ bị xóa. Loại session này usually áp dụng các chức năng như validate, flash notify.
- Để sử dụng flash session, ta sử dụng method `flash` với cú pháp:

```php
$sessions = Session::flash($key, $value);
```

- Trong TH, ta muốn flash session tồn tại thêm 1 request nữa, ta có thể sử dụng phương thức `reflash`. Cú pháp: `$request->session()->reflash();`

## 3.5 Xóa dữ liệu

- Ta có thể sử dụng method `forget` để xóa Session trong Laravel. VD:

```php
$request->session()->forget('name');
//forget multiple keys
$request->session()->forget(['name', 'status']);
```

- Trong TH ta cần xóa hết Session, có thể sử dụng method `flush`. Cú pháp: `$request->session()->flush();`

# 4. Khởi tạo lại Session ID:

- Trong một vài TH nào đó, ta cần thay đổi lại session ID, ta có thể sử dụng method `regenerate`. Cú pháp: `$request->session()->regenerate();`.
- Nếu như cần thay đổi Session ID và xóa hết session data, ta có thể sử dụng method `invalidate`. Cú pháp: `$request->session()->invalidate();`.
