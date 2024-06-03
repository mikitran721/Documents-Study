# Tổng quan

Gồm các nội dung

1. Giới thiệu
2. Cấu hình
3. Read-Write DB
4. Thực thi query đến DB
5. Listen Query event
6. Thực thi DB transactions
7. Kết nối đến DB thông qua CLI

# 1. Giới thiệu:

- Trong Laravel, DB được hỗ trợ rất mạnh qua lớp Query Builder và Eloquent ORM. Đồng thời,Laravel cũng hỗ trợ sử dụng được nhiều DB, connection trên cùng một ứng dụng một cách đơn giản.
- Mặc định Laravel hỗ trợ các loại DB sau:
  - MySQL
  - PostgreSQL
  - SQLite
  - SQL Serve
- Nếu cần sử dụng các loại DB khác có thể tham khảo thêm package bên thứ 3 cung cấp.

# 2. Cấu hình

- Tất cả các thông tin cấu hình liên quan DB trong Laravel đều được đặt trong config/database.php. Trong file này chứa thông tin kết nối đến rất nhiều DB và ta có thể cấu hình connection nào là default.
- Hầu hết các thông tin cấu hình được lấy từ file .env
- Đối với cấu hình SQLite thì DB_DATABASE trong env chính là đường dẫn đến sqlite của ta. VD:

```php
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/database.sqlite
```

- Đối với SQL server ta cần đảm bảo PHP config phải có các extension `sqlsrv & pdo_sqlsrv`.
- Thông thường, các config tới DB sẽ có các thông tin như: host, database, username, password,... Nhưng nếu cần config qua URL connection thì Laravel cũng hỗ trợ, bằng cách ta có thể đưa chúng vào trong DATABASE_URL trong env.
- URL này sẽ có dạng: `driver://username:password@host:port/database?options`. Trong đó:
  - driver là loại DB ta muốn kết nối.
  - username là thông tin user đăng nhập vào DB.
  - password là password đăng nhập vào DB.
  - host là thông tin host của DB.
  - port là cổng của DB.
  - database là tên DB cần kết nối
  - options là các option ta muốn config kèm theo.
- VD: `mysql:://root::password@127.0.0.1/forge?charset=UTF-8`

# 3. Read-Write Database:

- Trong một số TH, ta muốn sử dụng các kết nối riêng biệt cho câu lệnh SELECT dữ liệu, các câu lệnh INSERT, UPDATE & DELETE lại sử dụng kết nối khác. Laravel cũng hỗ trợ ta làm điều này (rất tiện cho các hệ thống sử dụng Master-Slave).
- Mẫu config cho kết nối read-write:

```php
'mysql' => [
    'read' => [
        'host' => [
            '192.168.1.1',
            '192.168.1.2',
        ],
    ],
    'write' => [
        'host' => [
            '196.168.1.3',
        ],
    ],
    'sticky' => true,
    'driver' => 'mysql',
    'database' => 'database',
    'username' => 'root',
    'password' => '',
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
],
```

- Trong đó: `sticky` là key không bắt buộc, sticky quyết định các truy vấn sau câu lệnh write sẽ được thực thi vào connection nào trong request đó. Nếu sticky thiết lập là 'true' thì sau câu lệnh write data, các câu lệnh read phía sau sẽ sử dung connection đến host của DB write luôn (miễn là cùng 1 request).

# 4. Thực thi query đến DB:

- Sau khi đã hoàn tất việc kết nối đến DB, giờ ta có thể query đến DB thông qua class DB (Illuminate\Support\Facades\DB).

## Thực thi SELECT query

- Để sử dụng câu lệnh SELECT ta có thể sử dụng method `select`. VD:

```php
use Illuminate\Support\Facades\DB;

$users = DB::select("SELECT * FROM users where active = 1");
```

- Trong TH, ta muốn sử dụng prepare query ta có thể sử dụng tham số thứ 2 là các giá trị binding vào query. VD:

```php
use Illuminate\Support\Facades\DB;

$users = DB::select("SELECT * FROM users where active = ?", [1]);

foreach ($users as $user) {
    echo $user->name;
}
```

- Ngoài ra, ta có thể binding data vào câu query bằng cách gán tên cho nó. VD:

```php
users = DB::select("SELECT * FROM users where id = :id", ['id' => 1]);
```

## Thực thi INSERT query:

- Để run câu lệnh INSERT trong DB, ta sử dụng phương thức 'insert'. VD:

```php
use Illuminate\Support\Facades\DB;

DB::insert("INSERT INTO users (id, name) VALUES (1, 'Miki')");
```

- Ta có thể sử dụng binding query vào trong phương thức `insert`. VD:

```php
DB::insert("INSERT INTO users (id, name) VALUES (?, ?)", [1, 'Miki2']);
```

## Thực thi UPDATE query:

- Để chạy câu lệnh UPDATE trong DB, ta sử dụng method `update`. VD:

```php
use Illuminate\Support\Facades\DB;
$affected = DB::update("UPDATE users set votes = 100 WHERE name = 'Anita'");
```

- Hoặc ta có thể binding query (sử dụng khi có biến) vào trong phương thức 'update'. VD:

```php
use Illuminate\Support\Facades\DB;

$affected = DB::update("UPDATE users SET votes = ? WHERE name = ?", [100, 'Anita']);
```

## Thực thi DELETE query:

- Để chạy câu lệnh DELETE trong DB, ta sử dụng method `delete`. VD:

```php
$deleted = DB::delete("DELETE * FROM users");
```

## Thực thi các câu lệnh chung:

- Ngoài các câu lệnh trên, nếu trong TH ta không muốn nhận giá trị trả về từ DB khi thực hiện câu lệnh, ta có thể sử dụng method `statement` để thực thi. VD:

```php
DB::statement("DROP TABLE users");
```

## Chỉ định connection

- Trong TH, ta cần chỉ định connection thực thi query đến DB, ta có thể sử dụng method `connection` để chỉ định connection trước khi thực thi query.

* Lưu ý: Connetion name chính là array key của array connection trong file 'config/database.php'.

- VD: Chỉ định connection đến kết nối 'sqlite'

```php
use Illuminate\Support\Facades\DB;

$users = DB::connection('sqlite')->select(...);
```

# 5. Listen query Event

- Nếu như ta muốn lắng nghe các câu query trên Laravel, ta có thể sử dụng phương thức `listen`. VD:

```php
DB::listen(function ($query) {
    //$query->sql
    //$query->bindings
    //$query->time
});
```

- Tuy nhiên, để cho listen hoạt động tốt, ta nên đặt chúng vào một 'service provider' nào đó. VD:

```php
namespace App\Providers;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    //...
    public function boot()
    {
        DB::listen(function ($query) {
            //$query->sql
            //$query->bindings
            //$query->time
        });
    }
}
```

# 6. Thực thi DB transactions:

- Nếu ta cần thực thi nhiều câu query theo một transaction của DB, ta có thể sử dụng method `transaction`. VD:

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    DB::update('update users set votes = 1');
    DB::delete('delete * from posts');
});
```

- Trong TH ta muốn xác định số lần thử lại transaction, ta có thể truyền vào tham số thứ 2 tương ứng với số lần thực thi lại transaction. Nếu hết số lần thực thi lại mà transaction vẫn chưa hoàn thành thì Laravel sẽ raise ra một exception.
- VD: Retry lại transaction 5 lần

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    DB::update('update users set votes = 1');
    DB::delete('delete * from posts');
}, 5);
```

- Ngoài cách như trên thì ta cũng có thể thực thi transaction một cách thủ công qua các methods như:
  - beginTransaction: bắt đầu transaction
  - rollBack: thực thi rollback query
  - commit: thực thi commit query
- VD:

```php
try {
    DB::beginTransaction();
    DB::update('update users set votes =1');
    DB::delete('delete from posts');
    DB::commit();
} catch(Exception $exception) {
    DB::commit();
}
```

# 7. Kết nối đến DB thông qua CLI:

- Để kết nối đến DB qua CLI, ta có thể sử dụng câu lệnh: `php artisan db`.
- Đối với TH cần chỉ định connection, ta có thể truyền thêm argument vào command. VD: kết nối connection mysql: `php artisan db mysql`
