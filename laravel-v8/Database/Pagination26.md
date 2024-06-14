# TỔNG QUAN

Bao gồm các nội dung sau:

1. Khái niệm
2. Sử dụng Pagination:
   - Đối với Query builder.
   - Đối với Eloquent ORM.
   - Tạo phân trang thủ công.
   - Custom Pagination URL
3. Hiển thị pagination data

# 1. Khái niệm:

- Pagination là một class trong Laravel core, nó hỗ trợ phân trang dữ liệu trả về trong DB (Query builder, Eloquent ORM) một cách đơn giản và linh hoạt.
- Mặc định Pagination trong Laravel sử dụng Taiwind CSS framework & ta có thể thay đổi nó cũng dễ dàng.

# 2. Sử dụng Pagination:

## 2.1 Đối với Query builder:

- Để phân trang KQ trả về trong Query builder, ta sử dụng method `paginate` với cú pháp sau:

```php
paginate($perPage, $columns, $pageName, $page);
```

- Trong đó:
  - $perPage: là số lượng item sẽ lấy ra và hiển thị trên mỗi trang. Mặc định là 15 item/page.
  - $columns: là những cột sẽ lấy ra trong DB, mặc định là lấy hết.
  - $pageName: là tên của query string sẽ chứa tham số page number. Mặc định `pageName = 'page'`.
  - $page: là item cần lấy ra thuộc trang số mấy, nếu 'page = null' thì Laravel sẽ xử lý theo data của page query string. Mặc định $page=null;

* VD: phân trang 5 User trong bảng 'users' / page

```php
class UserController extends Controller
{
    public function index()
    {
        return view('user.index', [
            'users' => DB::table('users')->paginate(5)
        ]);
    }
}
```

- Trong TH ta muốn sử dụng 'pagination đơn giản', chỉ có 2 button là 'previous & next' thì có thể sử dụng phương thức 'simplePaginate' với cú pháp tương tự method 'paginate'. VD:

```php
$users = DB::table('users')>simplePaginate(15);
```

## 2.2 Đối với Eloquent ORM:

- Đối với TH sử dụng Eloquent thì cú pháp và phương thức cũng tương tự như đối với Query Builder. VD:

```php
use App\Models\User;

$users = User::paginate(15);
//or
$users = User::where('votes', '>', 100)->paginate(15);
```

## 2.3 Tự tạo ra phân trang thủ công:

- Trong TH, data của ta là một dữ liệu không phải được trả về từ Query Builder hoặc Eloquent ORM, ta có thể sử dụng một trong 2 class `Illuminate\Pagination\Paginator | Illuminate\Pagination\LengthAwarePagination` để tạo ra pagination với cú pháp:

```php
use Illuminate\Pagination\Paginator;
$paginate = new Paginator($items, $perPage, $currentPage, $options);
//or

use Illuminate\Pagination\LengthAwarePaginator;
$paginate = new LengthAwarePaginator($items, $total, $perPage, $currentPage, $options);
```

\*\* Trong đó:

- $item: là mảng data ta muốn phân trang.
- $total: là tổng số bản ghi có trong item.
- $perPage: là số lượng item sẽ lấy ra và hiển thị trên mỗi trang.
- $currentPage: là trang dữ liệu ta muốn lấy ra hiện tại. Mặc định $currentPage = null;
- $options: là các tham số cấu hình thêm cho pagination. Mặc định $options = [];

- So sánh, ta thấy ở Paginator không tồn tại tham số '$total' vì lớp 'Paginator' này tương tự như 'simplePaginate' nên nó không cần phải lấy ra tổng số lượng item trong mảng, còn 'LengthAwarePaginator' thì tương tự như paginate nên nó cần lấy ra tổng số lượng item để tính toán và hiển thị. VD:

```php
$students = [
    ['name' => 'nva'],
    ['name' => 'nvB'],
    ['name' => 'nvC'],
    ['name' => 'nvD'],
];

$paginate = new \Illuminate\Pagination\Paginator($students, 2);
```

## 2.4 Custome Pagination URL:

- Mặc định, Laravel sẽ sử dụng link hiện tại để làm URL paginate, nhưng trong TH ta muốn thay đổi nó, ta có thể sử dụng method 'withPath' để thay đổi URL theo ý ta.
- VD: đổi paginate URL từ /users thành admin/users

```php
use App\Models\User;

Route::get('/users', function () {
    $users = User::paginate(15);

    $users->withPath('/admin/users');
});
```

- Nếu phần logic pagination của ta cần add thêm một vài tham số vào trong URL, ta có thể sử dụng phương thức 'appends' để add thêm query string vào URL.
- VD: thêm tham số 'sort' vào trong URL paginate

```php
use App\Models\User;

Route::get('/users', function () {
    $users = User::paginate(15);

    $users->appends(['sort' => 'votes']);
});
```

- Hoặc ta có thể sử dụng phương thức `withQueryString` để add thêm tất cả các query string trên URL hiện tại vào trong paginate. VD:

```php
$users = User::paginate(15)->withQueryString();
```

- Ta cũng có thể thêm các 'hash fragment' vào cuối URL trong pagination bằng cách sử dụng method `fragment`. VD: thêm '#users' vào cuối URL

```php
$users = User::paginate(15)->fragment('users');
```

# 3. Hiển thị Pagination data:

-
