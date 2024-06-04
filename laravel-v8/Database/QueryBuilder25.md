# TỔNG QUAN

- Gồm các nội dung:

1. Khái niệm query builder
2. Thực thi câu lệnh trong DB

# 1. Khái niệm Query Builder

- Query builder là một class trong Laravel, cung cấp các phương thức để làm việc với DB một cách thuận tiện hơn.
- Class này sử dụng `PDO parameter binding` để thực thi các câu lệnh, điều này sẽ giúp cho ứng dụng của ta tránh được các lỗi liên quan đến SQL injection. Nhờ đó, ta không cần thiết phải làm sạch dữ liệu khi sử dụng query builder.

# 2. Thực thi câu lệnh trong DB:

- Class Query builder cung cấp rất nhiều methods làm việc với DB. Hãy tham khảo trên laravel web.

## Lấy tất cả dữ liệu trong table

- Ta có thể sử dụng method `table` để chỉ định table muốn thực hiện truy vấn, sau đó dùng method `get` để lấy ra dữ liệu của truy vấn đó.
- VD: lấy hết user trong table users.

```php
use Illuminate\Support\Facades\DB;
$users = DB::table('users')->get();
foreach ($users as $user) {
    echo $user->name;
}
```

- Phương thức get sẽ trả về một 'collection' chứa kết quả trả về của câu query, với các dữ liệu của từng row là 'PHP stdClass'.

## Lấy ra một dòng dữ liệu trong table

- Nếu muốn lấy ra một dòng dữ liệu, ta có thể sử dụng method 'first'. Method này sẽ limit kết quả trả về của query, method này sẽ trả về một PHP stdClass nếu có dữ liệu và 'null' nếu không có.
- VD: lấy ra user đầu tiên trong bảng users có 'name = poo'

```php
$user = DB::table('users')->where('name', 'poo')->first();
```

- Trong TH, cần lấy ra một dòng data theo cột 'id', ta có thể sử dụng phương thức 'find'. VD: Lấy ra user có id=3.

```php
$user = DB::table('users')->find(3);
```

## Chunk giá trị trả về:

- Đôi khi ta cần lấy ra số lượng lớn bản ghi trong DB, nhưng lại sợ điều đó làm quá tải cho DB, khi này ta có thể sử dụng method 'chunk' để chia nhỏ data mỗi lần lấy.
- VD: chia nhỏ data, mỗi lần lấy 100 bản ghi trong mảng 'users'

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    foreach ($users as $user) {
        //code
        //return false if done
    }
})
```

- Nếu như cần ngừng việc 'chunk' data tại một thời điểm nào đó, ta có thể 'return false' để báo cho Laravel biết để stop. VD:

```php
DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    foreach ($users as $user) {
        if ($user->id) {
            return false; //stop chunking
        }
    }
})
```

## Đếm số lượng bản ghi trả về:

- Để đếm số lượng records của table ta sử dụng method 'count'. VD:

```php
use Illuminate\Support\Facades\DB;

$users = DB:table('users')->count();
```

- Tương tự, ta có thể sử dụng các method 'max, min, avg & sum' để lấy ra giá trị tương ứng của một cột nào đó. VD:

```php
$price = DB::table('orders')->max('price');
$price = DB::table('orders')->min('price');
$price = DB::table('orders')->avg('price');
$price = DB::table('orders')->sum('price');
```

## Kiểm tra sự tồn tại của dữ liệu:

- Ta có thể kiểm tra xem câu query kia có tồn tại giá trị trong DB không bằng phương thức 'exist'. Method này sẽ trả về 'true' nếu có dữ liệu matching với câu query. VD:

```php
if (DB::table('orders')->where('finalized', 1)->exist()) {
    //code
}
```

- Ngược lại, ta có thể sử dụng method 'doesntExist' để kiểm tra dữ liệu không tồn tại, nếu không tồn tại result matching với câu query nó sẽ trả về 'true'. VD:

```php
if (DB::table('orders')->where('finalized', 1)->doesntExist()) {
    //code
}
```

## Select data trong table

- Để xác định các columns sẽ lấy ra trong DB, ta sử dụng method 'select'. VD: select ra: name, email của table 'users'

```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')
            ->select('name', 'email')
            ->get();
//or
$users = DB::table('users')
            ->select(['name', 'email'])
            ->get();
```

- Ta cũng có thể đặt định danh cho column name trong method 'select'. VD:

```php
$users = DB::table('users')
            ->select('name', 'email as user_email')
            ->get();
```

- Trong TH, ta đã sử dụng method 'select' trước đó để thiết lập các column data trả về rồi & muốn add thêm column khác nữa, ta có thể sử dụng method 'addSelect'. VD:

```php
$query = DB::table('users')->select('name');

$users = $query->addSelect('age')->get();
```

- Ta cũng có thể 'query distinct' dữ liệu trong DB bằng cách sử dụng method 'distinct'. VD:

```php
$users = DB::table('users')->distinc()->get();
```

- Nếu như 'select' của ta chứa 'logic' thì ta có thể sử dụng method 'raw' để đưa logic đó vào trong câu query. VD:

```php
$users = DB::table('users')
            ->select(DB::raw('count(*) as user_count, status'))
            ->where('status', '<>', 1)
            ->groupBy('status')
            ->get();
```

- Lưu ý method 'DB::raw' sẽ đưa trực tiếp những gì ta truyền vào trong DB, chính vì thế cần cân nhắc kỹ nếu ko sẽ bị dĩnh lỗ hổng 'sql injection'.

## Join table trong DB:

- Ta có thể sử dụng method 'join' để join table trong DB. VD:

```php
$users = DB::table('users')
            ->join('contacts', 'users.id', '=', 'contacts.user_id')
            ->join('orders', 'users.id', '=', 'orders.user_id')
            ->select('users.*', 'contacts.phone', 'orders.price')
            ->get();
```

- Tương tự, ta có thể sử dụng methods: 'leftJoin, rightJoin & crossJoin' để thực hiện LEFT JOIN, RIGHT JOIN & CROSS JOIN trong DB. VD:

```php
$users = DB::table('users')
            ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
            ->get();

$users2 = DB::table('users')
            ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
            ->get();

$sizes = DB::table('sizes')
            ->crossJoin('colors')
            ->get();
```

- Nếu câu lệnh 'join' chứa nhiều điều kiện phức tạp, ta có thể truyền tham số thứ 2 là một 'closure' để thực thi logic. VD:

```php
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')
             ->where('contacts.user_id', '>', 5);
    })
    ->get();
```

- Tương tự ta có thể join với một 'sub query' khác. VD:

```php
$latestPosts = DB::table('posts')
                ->select('user_id', DB::raw('MAX(created_at) as last_post_created_at'))
                ->where('is_published', true)
                ->groupBy('user_id');

$users = DB::table('users')
            ->joinSub($latestPosts, 'latest_posts', function ($join) {
                $join->on('users.id', '=', 'lastest_posts.user_id');
            })
            ->get();
```

## Union query:

- Ta có thể thực thi câu lệnh 'UNION' trong DB bằng cách sử dụng phương thức 'union'. VD:

```php
$first = DB::table('users')
            ->whereNull('first_name');

$users = DB::table('users')
            ->whereNull('last_name')
            ->union($first)
            ->get();
```

## Where query:

- Ta có thể sử dụng method 'where' để thực hiện câu lệnh 'WHERE' trong DB. VD:

```php
$users = DB::table('users')
            ->where('votes', '=', 100)
            ->where('age', '>', 35)
            ->get();
```

- Khi ta sử dụng method 'where' nhiều lần thì Laravel sẽ tự convert các lần sau thành 'and where'. Nếu ta sử dụng method 'where' với 2 tham số truyền vào thì Laravel sẽ tự hiểu nó là '='. VD:

```php
DB::table('users')->where('votes', '=', 100);
//same
DB::table('users')->where('votes', 100);
```

- Ta cũng có thể truyền một array vào trong method 'where'. VD:

```php
 DB::table('users')->where('votes', 100)->where('age', 15);
 //same
 DB::table('users')->where(['votes' => 100, 'age' => 15]);
```

- Nếu ta muốn sử dụng 'or where' trong DB thì ta có thể sử dụng method 'orWhere'. VD:

```php
$users = DB::table('users')
            ->where('votes', '>', 100)
            ->orWhere('name', 'poo')
            ->get();
```

- Ta có thể truyền vào trong 'orWhere' một closure chứa các câu query khác. VD:

```php
$users = DB::table('users')
            ->where('votes', '>', '100')
            ->orWhere(function ($query) {
                $query->where('name', 'phoong')
                      ->where('votes', '>', 50)
            })
            ->get();
```

- Tương tự, ta có thể sử dụng 'whereNull, whereNotNull, orWhereNull & orWhereNotNull'. VD:

```php
$users = DB::table('users')
            ->whereNull('updated_at')
            get();
```

- Ta cũng có thể sử dụng các phương thức 'whereDate, whereMonth, whereDay, whereYear, whereTime' để truy vấn với các column dữ liệu. VD:

```php
$users = DB::table('users')
            ->whereDate('created_at', '2024-06-04')
            ->get();
```
