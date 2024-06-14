# 1. Kết hợp Query builder và Eloquent ORM trong Laravel

Kết hợp Query Builder và Eloquent ORM trong Laravel có thể là một cách tiếp cận linh hoạt để tối ưu hóa và tùy chỉnh các truy vấn cơ sở dữ liệu của bạn. Dưới đây là một số cách mà bạn có thể kết hợp cả hai để đạt được hiệu suất và tính dễ dàng trong việc quản lý cơ sở dữ liệu:

### 1. Sử dụng Query Builder trong Eloquent

Bạn có thể sử dụng Query Builder trực tiếp trong các truy vấn Eloquent để tận dụng các chức năng của cả hai.

#### Ví dụ 1: Sử dụng Query Builder để thêm điều kiện vào Eloquent Query

```php
use App\Models\User;
use Illuminate\Support\Facades\DB;

$users = User::where('status', 'active')
             ->where(function ($query) {
                 $query->where('age', '>', 25)
                       ->orWhere('location', 'New York');
             })
             ->get();
```

#### Ví dụ 2: Sử dụng Query Builder để tạo join phức tạp trong Eloquent

```php
use App\Models\Post;
use Illuminate\Support\Facades\DB;

$posts = Post::select('posts.*', 'users.name')
             ->join('users', 'posts.user_id', '=', 'users.id')
             ->where('users.status', 'active')
             ->get();
```

### 2. Sử dụng Raw Expressions trong Eloquent

Raw expressions có thể được sử dụng để chèn các đoạn SQL tùy chỉnh vào các truy vấn Eloquent.

#### Ví dụ: Sử dụng raw expressions để thực hiện các phép tính phức tạp

```php
use App\Models\Order;
use Illuminate\Support\Facades\DB;

$orders = Order::select('orders.*', DB::raw('SUM(order_items.quantity * order_items.price) as total_amount'))
               ->join('order_items', 'orders.id', '=', 'order_items.order_id')
               ->groupBy('orders.id')
               ->havingRaw('total_amount > ?', [100])
               ->get();
```

### 3. Tạo các truy vấn phức tạp bằng cách kết hợp nhiều phương thức

Bạn có thể sử dụng các phương thức của Eloquent và Query Builder để tạo ra các truy vấn phức tạp hơn.

#### Ví dụ: Kết hợp nhiều phương thức để tạo truy vấn phức tạp

```php
use App\Models\Product;

$products = Product::where('category_id', 1)
                   ->where(function($query) {
                       $query->where('price', '>', 50)
                             ->orWhere('name', 'like', '%Laptop%');
                   })
                   ->orderBy('price', 'desc')
                   ->get();
```

### 4. Sử dụng Query Builder để viết các câu lệnh tùy chỉnh trong Eloquent

Đôi khi bạn cần thực hiện các truy vấn mà Eloquent không hỗ trợ trực tiếp. Trong trường hợp này, bạn có thể sử dụng Query Builder để viết các câu lệnh tùy chỉnh và kết hợp chúng với Eloquent.

#### Ví dụ: Sử dụng Query Builder để viết câu lệnh tùy chỉnh

```php
use App\Models\User;
use Illuminate\Support\Facades\DB;

$users = User::whereExists(function($query) {
    $query->select(DB::raw(1))
          ->from('orders')
          ->whereRaw('orders.user_id = users.id')
          ->where('orders.status', 'completed');
})
->get();
```

### 5. Eloquent Scopes với Query Builder

Bạn có thể định nghĩa các scope trong model Eloquent và sử dụng Query Builder trong các scope này để tái sử dụng các truy vấn phức tạp.

#### Ví dụ: Định nghĩa scope và sử dụng Query Builder trong đó

```php
// Trong model User
public function scopeActive($query)
{
    return $query->where('status', 'active');
}

public function scopeOlderThan($query, $age)
{
    return $query->where('age', '>', $age);
}

// Sử dụng scope trong controller hoặc nơi khác
$users = User::active()->olderThan(25)->get();
```

Kết hợp Query Builder và Eloquent ORM trong Laravel cho phép bạn tận dụng sức mạnh của cả hai, giúp tối ưu hóa và linh hoạt hơn trong việc xử lý các truy vấn cơ sở dữ liệu phức tạp.

# 2. So sánh Query builder và Eloquent ORM

Laravel cung cấp hai cách chính để tương tác với cơ sở dữ liệu: Query Builder và Eloquent ORM. Cả hai đều có những ưu điểm và nhược điểm riêng. Dưới đây là so sánh chi tiết giữa Query Builder và Eloquent ORM trong Laravel:

### 1. Eloquent ORM

#### Ưu điểm:

- **ORM (Object-Relational Mapping):** Eloquent cung cấp một cách tiếp cận hướng đối tượng để làm việc với cơ sở dữ liệu. Mỗi model đại diện cho một bảng trong cơ sở dữ liệu và các đối tượng model đại diện cho các bản ghi.
- **Dễ sử dụng và thân thiện với người dùng:** Cú pháp của Eloquent rất dễ đọc và viết, giống như làm việc với các đối tượng PHP thông thường.
- **Quan hệ:** Eloquent làm việc rất tốt với các mối quan hệ giữa các bảng (một-một, một-nhiều, nhiều-nhiều). Việc truy xuất các dữ liệu liên quan trở nên đơn giản và dễ hiểu.
- **Tích hợp tốt với các tính năng của Laravel:** Eloquent tích hợp tốt với các tính năng khác của Laravel như sự kiện (events), observer, mutators, và accessors.

#### Nhược điểm:

- **Hiệu suất:** Eloquent có thể chậm hơn so với Query Builder trong một số trường hợp, đặc biệt là khi làm việc với lượng dữ liệu lớn hoặc truy vấn phức tạp.
- **Cần hiểu biết về ORM:** Để tận dụng tối đa Eloquent, bạn cần có hiểu biết về cách hoạt động của ORM và các quy tắc thiết kế cơ sở dữ liệu hướng đối tượng.

### 2. Query Builder

#### Ưu điểm:

- **Hiệu suất:** Query Builder thường nhanh hơn Eloquent vì nó làm việc trực tiếp với SQL và không cần chuyển đổi dữ liệu giữa các đối tượng và các bản ghi cơ sở dữ liệu.
- **Linh hoạt:** Query Builder cho phép bạn viết các truy vấn SQL phức tạp một cách tự do mà không bị hạn chế bởi các quy tắc của ORM.
- **Tối ưu hóa:** Bạn có thể tối ưu hóa các truy vấn của mình một cách chi tiết và chính xác hơn với Query Builder.

#### Nhược điểm:

- **Không hướng đối tượng:** Query Builder không cung cấp một cách tiếp cận hướng đối tượng. Bạn phải làm việc trực tiếp với các bảng và các bản ghi, điều này có thể làm cho mã của bạn khó đọc và khó bảo trì hơn.
- **Thiếu các tính năng cao cấp:** Query Builder không hỗ trợ các tính năng như các mối quan hệ, mutators, accessors, và sự kiện mà Eloquent cung cấp.

### So sánh tổng quan

| Tiêu chí           | Eloquent ORM                               | Query Builder                                       |
| ------------------ | ------------------------------------------ | --------------------------------------------------- |
| **Cách tiếp cận**  | Hướng đối tượng                            | Hướng thủ tục                                       |
| **Dễ sử dụng**     | Dễ đọc, dễ viết với cú pháp thân thiện     | Cần viết nhiều mã SQL hơn                           |
| **Hiệu suất**      | Chậm hơn trong một số trường hợp           | Nhanh hơn, tối ưu cho các truy vấn phức tạp         |
| **Tính linh hoạt** | Hỗ trợ các mối quan hệ, tính năng cao cấp  | Linh hoạt trong việc viết truy vấn SQL phức tạp     |
| **Tích hợp**       | Tích hợp tốt với các tính năng của Laravel | Thiếu tính năng cao cấp, làm việc trực tiếp với SQL |

### Khi nào nên sử dụng Eloquent ORM?

- Khi bạn cần làm việc với các mối quan hệ phức tạp giữa các bảng.
- Khi bạn muốn tận dụng các tính năng cao cấp như sự kiện, mutators, và accessors.
- Khi bạn muốn một cú pháp dễ đọc, dễ bảo trì và hướng đối tượng.

### Khi nào nên sử dụng Query Builder?

- Khi bạn cần hiệu suất tối đa và truy vấn SQL phức tạp.
- Khi bạn không cần các tính năng cao cấp của ORM hoặc không muốn sử dụng chúng.
- Khi bạn cần viết các truy vấn SQL trực tiếp và linh hoạt.

### Kết hợp cả hai

Trong nhiều trường hợp, bạn có thể kết hợp cả Eloquent và Query Builder để tận dụng ưu điểm của cả hai. Ví dụ, bạn có thể sử dụng Eloquent cho phần lớn các truy vấn của mình và chuyển sang Query Builder khi cần tối ưu hóa hoặc viết các truy vấn phức tạp.

```php
use App\Models\User;
use Illuminate\Support\Facades\DB;

// Eloquent Query
$activeUsers = User::where('status', 'active')->get();

// Query Builder Query
$totalSales = DB::table('orders')
                ->select(DB::raw('SUM(total_amount) as total_sales'))
                ->where('status', 'completed')
                ->first();
```

Tóm lại, việc lựa chọn giữa Eloquent ORM và Query Builder phụ thuộc vào yêu cầu cụ thể của dự án của bạn và sự cân nhắc giữa hiệu suất và tính dễ dàng trong việc bảo trì mã nguồn.
