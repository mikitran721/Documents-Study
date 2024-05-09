# Tóm tắt:

- Đây là phần 2 về Request trong Laravel, nội dung về phần tương tác với dữ liệu, gồm:
  - Nhận dữ liệu input
  - Nhận dữ liệu query string.
  - Kiểm tra dữ liệu input
  - Old input.
  - Input trimming & Normalization
  - File

# 1. Nhận dữ liệu input:

- Để nhận tất cả dữ liệu input gửi lên request, ta sử dụng phương thức `all`. VD:

```php
$input = $req->all();
```

- Trong TH cần lấy ra dữ liệu của một input cụ thể (bao gồm payload và query string) ta có thể sử dụng phương thức `input`. VD: `$name = $req->input('name')`.
- Mặc định Laravel sẽ trả về null nếu input ta lấy ko tồn tại. Tác cũng có thể xác định giá trị mặc định cho input bằng cách truyền thêm tham số thứ 2 vào phương thức `input`. VD:

```php
$name = $req->input('name', 'poo phuung');
```

- Trong TH dữ liệu cần truy xuất là một mảng thì ta có thể sử dụng "." thay cho cách truy cập mảng. VD:

```php
$name = $req->input('products.0.name');
// ~ products[0]['name']

$name = $req->input('products.*.name');
// lấy ra tất cả name của products.
```

- Nếu ta không truyền tham số vào phương thức `input` thì Laravel sẽ trả về tất cả dữ liệu gửi lên request (tương tự như `req->all()`). VD:

```php
$input = $req->input();
// ~ same as
$input = $req->all();
```

- Trong một số TH, ta sử dụng các thẻ HTML như checkbox, radio,... Như vậy ta chỉ cần kiểm tra giá trị của nó là true | false thôi, khi đó ta có thể sử dụng phương thức `boolean`. Phương thức này nhận `1,'1', true, 'true', on, yes` là true, còn ngược lại là false. VD:

```php
$archived = $req->booleand('archived');
```

- Laravel cũng áp dụng `magic method` vào trong Request,nên ta hoàn toàn có thể access đến một input dưới dạng property trong object. Đối với cách này thì Laravel sẽ ưu tiên lấy ra giá trị trong `payload` trước. VD: lấy ra dữ liệu của input name `$name = $req->name;`.
- Đôi khi ta cần lấy ra một số input nhất định nào đó nhưng không phải tất cả, lúc này ta có thể sử dụng phương thức `only`. VD: chỉ lấy ra input -> username, password

```php
$input = $req->only(['username', 'password']);

//hoac

$input = $req->only('username', 'password');
```

- Nếu muốn bỏ qua input nào đó còn lại sẽ lấy hết thì ta có thể sử dụng phương thức `except`. VD: bỏ qua input credit_card còn lại lấy hết.

```php
$input = $req->except(['credit_card']);
// or
$input = $req->except('credit_card');
```

# 2. Nhận dữ liệu query string.

- Trong một số trường hợp ta chỉ muốn lấy query string của dữ liệu gửi lên request, ta có thể sử dụng phương thức `query`. Hàm này trả về một mảng dât chứa các query string gửi lên request. VD:

```php
$query = $req->query();
```

- Nếu cần lấy giá trị của một query string cụ thể, có thể truyền query string name vào phương thức query. VD:

```php
$name = $req->query('name');
```

- Mặc định, Laravel sẽ trả về null nếu query string ta cần lấy không tồn tại. Ta có thể xác định giá trị mặc định cho input bằng cách truyền thêm tham số thứ 2 vào query. VD:

```php
$name = $req->query('name', 'puu phuung');
```

# 3. Kiểm tra dữ liệu input

- Để kiểm tra dữ liệu gửi lên có input nào đó hay không, ta sử dụng phương thức `has`. Phương thức này trả về true nếu input xuất hiện trong request và ngược lại sẽ là false. VD:

```php
if ($req->has('name')) {
  // code
}
```

- Ta có thể kiểm tra nhiều input với phương thức has bằng cách truyền vào một mảng các input. Lúc này, phương thức sẽ trả về true nếu tất cả các input đều có trong request, ngược lại là false.

```php
if ($rea->has(['name', 'email'])) {
  // code
}

// same as
if ($req->has('name') && $req->has('email')) {
  // code
}
```

- Nếu cần kiểm tra một mảng các input và sẽ trả về true nếu có một hoặc nhiều input xuất hiện trong danh sách. Ta có thể sử dụng phương thức `hasAny`.

```php
if ($req->hasAny(['name', 'email'])) {
  // code
}
// same
if ($req->has('name' || $req->has('email'))) {
  // code
}
```

- Đôi khi cần xử lý một số hành động, logic khi input có xuất hiện trong request ta có thể sử dụng hàm `whenHas`.

```php
$rea->whenHas('name', function ($input) {
  // code
});
```

- Đôi khi ta cần kiểm tra thêm input nào đó có xuất hiện & nó phải bao gồm giá trị. Khi này ta sử dụng phương thức `filled`. Phương thức này sẽ trả về true nếu có input có xuất hiện trong input và có giá trị kèm theo. VD:

```php
if ($req->filled('name')) {
  // code
}
```

- Tương tự, ta có thể dùng hàm `whenFilled` để thực thi logic khi input nào đó có tồn tại và có giá trị. VD:

```php
$req->whenFilled('name', function ($input) {
  // code
});
```

- Nếu ta cần check xem một input nào đó không xuất hiện trong request, ta có thể sử dụng phương thức `missing`. Phương thức này sẽ trả về true nếu input không xuất hiện trong request.

```php
if ($req->missing('name')) {
  //code
}
```

# 4. Old input:

- Mặc định, Laravel sẽ lưu trữ lại giá trị các input của Request để phục vụ request tiếp theo như recover lại dữ liệu khi validate sai,.. Chúng đã được thực hiện tự động. Có một số phương thức cần thiết như dưới đây:
  - `flash` lưu hết dữ liệu input vào session & sẽ được xóa đi khi ta request lại lần tiếp theo.
  - `flashOnly` lưu hết các input được xác định vào session, còn lại sẽ bỏ qua.
  - `flashExcept` bỏ qua các input được xác định, còn lại sẽ lưu trữ.

```php
$request->flash();
$req->flashOnly(['username', 'email']);
$req->flaseExcept('password');
```

- Để lấy ra giá trị của input trước đó đã được store ta sử dụng phương thức `old`. Nếu dữ liệu không tồn tại sẽ return `null`. VD: `$username = $req->old('username')`. Hoặc có thể sử dụng hàm `old` trong helper: `old('username')`.
- Ta có thể xác định giá trị mặc định khi input không có giá trị bằng cách truyền vào tham số thứ 2. `$username = $req->old('username', 'puu phuung')`.

# 5. Input Trimming & Normalization

- Mặc định, Laravel sử dụng 2 middleware `App\Http\Middleware\TrimStrings & App\Http\Middleware\ConvertEmptyStringToNull` để làm nhiệm vụ xử lý, chuẩn hóa dữ liệu trước khi đưa và Request object.
- `TrimStrings` có tác dụng loại bỏ đi các khoảng trống ở các input gửi lên request.
- `ConvertEmptyStringToNull` chuyển đổi string rỗng thành null.
- Nếu không sử dụng có thể tắt đi tại `app\Http\Kernel.php`.

# 6. File:

- Ta có thể lấy ra được file upload lên trên request qua phương thức file. Phương thức này sẽ trả về một Object `UploadedFile` (Illuminate\Http\Uploadedfile) nếu file đó tồn tại và null nếu file ko tồn tại.
- Object `UploadedFile` được kế thừa từ `SplFileInfo` mặc định của PHP, nên ta có thể sử dụng được các phương thức trong SplFileInfo.
- VD: lấy thông tin file photo được upload lên request `$file = $req->file('photo')`. Ta có thể trỏ trực tiếp tới đến file name qua property: `$file = $req->photo`.
- Trong TH cần kiểm tra xem một file nào đó có xuất hiện trong request hay không, ta sử dụng phương thức `hasFile`. VD: kiểm tra xem request gửi lên có photo không?

```php
if ($req->hasFile('photo')) {
  // code
}
```

- Ngoài ra, ta cũng có thể kiểm tra xem file được upload lên có thành công hay không, = sử dụng phương thức `isValid`. VD:

```php
if ($req->file('photo')->isValid()) {
  // code
}
```
