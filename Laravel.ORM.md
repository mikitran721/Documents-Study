# Tên bảng

- khi cần đặt tên bảng khác với Model cần config trong model:
  - `protected $table='my_posts'`

# Khóa chính

- Muốn thay đổi tên của khóa chính (không phải là `id` theo default), cần:
  - `protected $primary_key = 'id_post'`
- Dữ liệu PK là int, nếu muốn thay đổi cần set `$keyType = 'string'`
- Để bỏ tự tăng của `id` cần set `$incrementing = false`

# TIMESTAMPS

- để Eloquent ko tự cập nhật cho timestamp mỗi khi thêm record cần set `public $timestamps = false`
- thay đổi định dạng data của timestamp cần set `protected $dateFormat = 'd-m-Y'`

# KẾT NỐI DB

- mặc định DB tại .env, nếu muốn model này connect tới 1 table trong csdl khác cần:
  - `protected $connection = 'connection-name'`

# TRUY VẤN CSDL

## all

- lấy tất cả data của table về (nặng)
- `Post::all()`

## thêm ràng buộc truy vấn

```php
$post = Post::where('published',true)
->orderBy('title',DESC)
-> take(5)
->get();
```

## Collections

- Các hàm của Eloquent như all() và get() đề trả về 1 instance từ Collection.
- Class collection cũng cung cấp các hàm hữu ích để làm việc với các KQ trả về
- search với `Eloquent collection` để tìm hiểu

## CHUNK

- Nếu bạn cần xử lý hàng ngàn kết quả từ Eloquent, sử dụng chunk sẽ tiết kiệm được memory khi thao tác với tập dữ liệu kết quả lớn. hàm này sẽ lấy từng chunk của Eloquent models, cung cấp chúng thông qua Closure để xử lý.

```php
Post::chunk(200,function($posts){
    foreach($posts as $post){
        //logic code
    }
});
```

## Cursors

- Hàm cursor cho phép bạn duyệt qua records bằng cách sử dụng một cursor(con trỏ), nó chỉ thực thi cho một truy vấn. Khi dữ liệu lớn, hàm cursor có thể được sử dụng để giảm memory sử dụng.

```php
foreach(Post::where('publish', true)->cursor() as $post) {
    //code
};
```

## Lấy dữ liệu một Model/Aggregate

- Ngoài việc lấy tất cả các records của table bằng hàm all(), chúng ta có thể lấy model instance bằng hàm find hoặc first

```php
$post = Post::find(1); // trả về một model instance
// hoặc lấy về một bản ghi đầu tiên trong list model instance trả về
$post = Post::where('publish', true)->first();
```

- có thể sử dụng các aggregate method như count, sum, max.

```php
$count = Post::where('publish',true)->count();
```

## NotFound Exception

- sử dụng `findOrFail` và `firstOrFail` trong try-catch

```php
try{
    $post = Post::findOrFail(1);
    //hoac
    $posts = Post::where('publish',true)->firstOrFail();
}catch(ModelNotFoundException $e){
    echo $e->getMessage();
}
```

## firstOrCreate và firstOrNew

- firstOrCreate: sẽ tìm các bản ghi sử dụng cặp cột và giá trị, nếu không tìm thấy, một bản ghi sẽ được tạo ra với các thuộc tính này.
- firstOrNew: nó không ghi vào CSDL mà trả về một instance của model, chỉ ghi dữ liệu vào CSDL sau khi gọi phương thức save().

```php
// Tìm sản phẩm trong CSDl nếu không có thì thêm bản ghi mới vào bảng.
$product = Product::firstOrCreate($request->all());
// Tìm sản phẩm trong CSDL nếu không có thì trả về một instance
$product = Product::firstOrNew($request->all());
$product->save();// Lúc này sản phẩm mới được lưu trong CSDL.
```

## updateOrCreate

```php
$product = Product::updateOrCreate([
    'name' => 'Sản phẩm 1',
    'price' => 150000,
]);
//Dùng để update hoặc tạo bản ghi mới.
```

## Deleting

- Xóa vĩnh viễn 1 record

```php
$post = Post::find(1);
$post->delete();
//hoặc
$post = Post::where('id', 1)->delete();
```

## Soft Deleting

- Cho phép xóa tạm thời, truy vấn, phục hồi, xóa vĩnh viễn bản ghi
- Để cho phép một Model có thể thực hiện được đánh dấu bản ghi đã xóa, chúng ta sử dụng trait `Illuminate\Database\Eloquent\SoftDeletes` và thêm `deleted_at` vào thuộc tính `$dates` của nó.

```php
use Illuminate\Database\Eloquent\SoftDeletes;
class Post extends Model
{
    use SoftDeletes;
    protected $fillable = [
        'title',
        'content',
        'publish'
    ];
    protected $dates = ['deleted_at'];
}
```

- Trong file migration tạo bảng posts bạn nhớ thêm `$table->softDeletes()`. Khi đó, nếu bạn thực hiện phương thức delete() thay vì nó sẽ xóa triệt để trong CSDL thì nó sẽ cập nhật thời gian hiện tại vào trường deleted_at, và như vậy bản ghi này coi như là đã xóa khỏi table. Để xem xem bản ghi này đã được đánh dấu là xóa tạm thời hay chưa các bạn sử dụng phương thức `trashed()`.

```php
if ($post->trashed()) {
    echo "bản ghi này đã dánh dấu là xóa mềm";
}
```

- Để truy vấn những bản ghi bao gồm cả đã xóa thì chúng ta sử dụng withTrashed()

```php
$post = Post::withTrashed()
            ->where('id', 1)
            ->get();
// Trả về kết quả bài Post người dùng có id = 1 đã bị xóa mềm.
//or
public function index()
{
    $model = new Item();
    $data = $model::withTrashed()->get()->toArray();
    dd($data);
}
```

- onlyTrashed(): Hàm này sẽ chỉ lấy các dữ liệu đã bị xóa từ bảng items.

```php
    public function index()
    {
    	$model = new Item();
		$data = $model::onlyTrashed()->get()->toArray();
		dd($data);
    }
```

- Chú ý, để xóa vĩnh viễn bản ghi dùng phương thức `forceDelete()`
