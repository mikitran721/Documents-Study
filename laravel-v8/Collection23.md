# Tổng quan

Nội dung gồm các phần sau

1. Khái niệm
2. Tạo collection
3. Các phương thức hỗ trợ trong Collection
4. Kế thừa collection
5. LazyCollection

# 1. Khái niệm Collection

- Collection là một class cung cấp các phương thức để làm việc với dữ liệu mảng một cách đơn giản, thuận tiện.
- VD: sử dụng collection thực hiện chuyển đổi all phần tử trong mảng thành in hoa và loại bỏ phần tử null trong mảng.

```php
$collection = collect(['taylor', 'abigail', null])->map(function ($name) {
    return strtoupper($name);
})->reject(function ($name) {
    return empty($name);
});
```

# 2. Tạo/Sử dụng Collection

- Có 3 cách để tạo Collection trong Laravel

## 2.1 Sử dụng helper collect

- Ta có thể sử dụng helper collect để tạo ra collection như ở VD phía trên.
- VD: tạo collection sử dụng helper collect

```php
$collection = collect([1, 2, 3]);
```

## 2.2 Sử dụng new Collection()

- Ta có thể tạo collection bằng cách khởi tạo luôn object từ class Collection. VD:

```php
use Illuminate\Support\Collection;
$collection = new Collection([1, 3, 4]);
```

## 2.3 Sử dụng Collection::make()

- Ta có thể khởi tạo object bằng cách sử dụng static method `make` trong Collection. VD:

```php
use Illuminate\Support\Collection;
$collection = Collection::make([1, 4, 5]);
```

- Trong tất cả các cách trên, dữ liệu đầu vào ta có thể bỏ trống, khi đó sẽ tạo ra một collection rỗng.

# 3. Các phương thức hỗ trợ trong Collection

- Tham khảo đầy đủ tại Laravel page, với key `Collection Available methods`.

## all

Phương thức này sẽ trả về tất cả các phần tử trong collection. VD:

```php
collect([1, 5, 6])->all();
```

## avg(), average()

Phương thức này sẽ trả về giá trị trung bình của các phần tử có trong mảng theo key. VD:

```php
$average = collect([3, 3, 4, 6])->avg(); //4

$avg2 = collect([
    ['foo' => 10],
    ['foo' => 10],
    ['foo' => 20],
    ['foo' => 40],
])->avg('foo');
//20
```

## chunk()

- Phương thức này sẽ phân khúc/break các giá trị bên trong collection ra thành các mảng con theo size chỉ định.
- VD:

```php
$collection = collect([1, 3, 6, 6, 6, 8, 9]);
$chunks = $collection->chunk(4);
$chunks->all();
//[[1, 3, 6, 6], [6, 8, 9]]
```

## collapse()

- Phương thức này sẽ làm phẳng các mảng con bên trong thành một mảng duy nhất.

```php
$collection = collect([
    [1, 3, 4],
    [4, 6, 6],
    [2, 4, 9],
]);
$collapsed = $collection->colapse();
$collapsed->all();
//[1, 3, 4, 4, 6, 6, 2, 4, 9]
```

## collect()

- Phương thức này sẽ trả về một collection mới với các giá trị trong collection hiện tại. (như copy collection). VD:

```php
$collectionA = collect([1, 3, 3]);
$collectionB = $collectionA->collect();
$collectionB->all();
```

## concat()

- Phương thức này sẽ nối các giá trị vào cuối của mảng trong collection. VD:

```php
$col = collect(['nva']);
$concatenated = $col->concat(['nvb'])->concat(['name' => 'nvc']);
$concatenated->all();
//['nva', 'nvb', 'nvc']
```

# contains()

- Phương thức này sẽ kiểm tra xem một value nào đó có tồn tại trong collection không. VD

```php
$collection = collect(['name' => 'Desk', 'price' => 100]);

$collection->contains('Desk'); //true

//Tham so truyen vao nhieu gia tri
$col = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);
$col->contains('product', 'Bookcase'); //false

//Tham so truyen vao la 1 closure
$col3 = collect([1, 3, 6, 7, 9]);
$col3->contains(function ($value, $key) {
    return $value > 5;
});
//true
```

## count()

- Phương thức này sẽ trả về số lượng phần tử có trong collection. VD:

```php
$col = collect([1, 5, 6]);
$col->count(); //3
```

## dd()

- Phương thức này sẽ dump ra các giá trị đang có trong mảng.

## diff()

- Phương thức này sẽ trả về các giá trị khác nhau giữa hai mảng data. VD:

```php
$col = collect([1, 2, 4, 6, 6]);
$diff = $col->diff([4, 6, 3]); //[3]
```

## each()

- Phương thức này sẽ lặp lại các giá trị trong collection và thực thi logic trong closure

```php
$col->each(function ($item, $key) {
    if (...) {
        return false;
    }
});
```

## filter()

- Phương thức này sẽ lặp qua các phần tử trong collection và thực thi logic trong closure, nếu closure trả về false thì phần tử này sẽ không được trả về trong kết quả. VD:

```php
$col = collect([1, 2, 5, 7]);
$filtered = $col->filter(function ($value, $key) {
    return $value > 2;
})

$filtered->all(); //[5, 7]
```

## first()

- Phương thức này sẽ trả về giá trị đầu tiên pass với điều kiện trong closure. VD:

```php
collect([1, 2, 4])->first(); //1

//voi tham so la closure
collect([1, 2, 5, 7])->first(function ($value, $key) {
    return $value > 2; //5
})
```

## flip()

- Phương thức này sẽ đảo ngược các giá trị key, value trong mảng. VD

```php
$collection = collect(['name' => 'nva', 'framework' => 'laravel']);
$flipped = $collection->flip();
$flipped->all();
//['nva' => 'name', 'laravel' => 'framework']
```

## get()

- Phương thức này sẽ trả về giá trị tương ứng với key truyền vào, nếu không tồn tại nó sẽ trả về null. VD

```php
$collection = collect(['name' => 'nva', 'framework' => 'laravel']);
$value = $collection->get('name');
//nva
```

## sum()

- Phương thức này trả về tổng giá trị các phần tử trong collection, sử dụng như avg();

## min(), max()

## toArray()

- Phương thức này sẽ convert collection về dạng array. VD:

```php
$collection = collect(['name' => 'book', 'price' => 200]);
$collection->toArray();
/*
[
    ['name' => 'book', 'price' => 200]
]
*/
```

## toJson()

- Phương thức này sẽ convert collection về dạng JSON string. VD:

```php
$collection = collect(['name' => 'book', 'price' => 200]);
$collection->toJson();
// '{"name":"book", "price":200}'
```

# 4. Kế thừa collection.

- Collection trong Laravel đã support nhiều phương thức. Tuy nhiên, nếu cần tạo thêm method cho collection, ta có thể làm được.
- Ta có thể sử dụng phương thức `macro` để tạo thêm phương thức với cú pháp

```php
Collection::macro($methodName, $closure);
```

- Trong đó:
  - $methodName là tên của method ta cần thêm.
  - $closure là một callback function chứa logic xử lý của method.

* VD:

```php
Collection::macro('toUpper', function () {
    return $this->map(function ($value) {
        return Str::upper($value);
    });
});
```

- Lúc này ở collection ta có thể gọi bình thường như VD:

```php
$collection = collect(['first', 'second']);
$upper = $collection->toUpper();
//['FIRST', 'SECOND']
```

## Macro với tham số truyền vào

```php
use Illuminate\Support\Collection;
use Illuminate\Support\Facades\Lang;
use Illuminate\Support\Str;

Collection::macro('toLocale', function ($locale) {
    return $this->map(function ($value) use ($locale) {
        return Lang::get($value, [], $locale);
    });
});
$collection = collect(['first', 'second']);
$translated = $collection->toLocale('es');
```

# 5. LazyCollection

- Nếu cần sử dụng Collection với dữ liệu lớn, ta có thể cân nhắc sử dụng LazyCollection. Class này dược base trên PHP generator nên sử dụng ít tài nguyên.
- Để tạo LazyCollection ta sử dụng cú pháp: `LazyCollection::make()`. VD:

```php
use Illuminate\Support\LazyCollection;

LazyCollection::make(function () {
    $handle = fopen('log.txt', 'r');

    while (($line = fgets($handle)) !== false) {
        yield $line;
    }
});
```
