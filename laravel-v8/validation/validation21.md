# Nội dung

Trình bày về cách hiển thị: error, thêm rule trong validation

1. Hiển thị error message.
2. Sửa nội dung error message
3. Các rule có sẵn
4. Thêm mới rule

# 1. Hiển thị error message

- Khi thực hiện validate data nếu như ko pass thì Laravel sẽ return về một `MessageBag` (Illuminate\Support\MessageBag) chứa các error message. Và Laravel cũng tự động gán MessageBag vào trong $errors của views.
- Để lấy ra error message đầu tiên của một field, ta sử dụng method `first`
- VD: lấy ra error message lỗi đầu tiên của 'email'

```php
$errors = $validator->errors();
echo $errors->first('email');
//or
{{ $errors->first('email') }}
```

- Để lấy ra tất cả message của một field nào đó, ta có thể sử dụng method `get`; hàm này sẽ trả về một mảng các message lỗi của field đó.
- VD: in ra tất cả các error message của trường email.

```php
foreach ($errors->get('email') as $msg) {
    //
}
```

- Nếu input là một mảng, ta có thể lấy ra tất cả error message trong mảng input key đó bằng cách sử dụng ký tự `*`.
- VD: Lấy ra error message của các attachments

```php
foreach ($errors->get('attachments.*') as $message) {
    //code
}
```

- Để lấy ra tất cả các error message của validator, ta có thể sử dụng method `all`. VD:

```php
foreach ($errors->all() as $message) {
    //code
}
```

- Trong TH, ta cần kiểm tra xem field nào có tồn tại error message hay ko, ta sử dụng method `has`. Method này sẽ trả về true nếu field đó có error message.
- VD: kiểm tra xem validate input email có sai hay không

```php
if ($errors->has('email')) {
    //code
}
```

# 2. Sửa nội dung error message

- Mặc định các message trong Laravel được đặt trong `lang/en/validation.php`. Nếu nó chưa có thì có thể sử dụng command `php artisan lang:publish`.
- Nếu muốn thay đổi nội dung message cho field nào đó, ta có thể thêm vào key `custom`.
- VD: thay đổi error message cho trường email với rule require và max.

```php
'custom' => [
    'email' => [
        'required' => 'We need to know your email address.',
        'max' => 'Your email address is too long.'
    ],
],
```

- Ta có thể custome attribute trong file language bằng cách thiết lập trong key `attribute`
- VD: thay đổi attribute name của field `email`

```php
'attributes' => [
    'email' => 'email address',
],
```

# 3. Các rule có sẵn trong Laravel

- Tham khảo trên Laravel page với key `Availabel Validation Rules`. Có một số rule thông dụng như sau:

* accepted: chỉ chấp nhận các giá trị: yes, on, 1, true; thường áp dụng cho input là radio | checkbox.
* active_url: kiểm tra xem URL có đang thực sự tồn tại bằng vc sử dụng hàm dns_get_record của PHP.
* after: kiểm tra ngày tháng có đang lớn hơn giá trị cần so sánh không.
* before: ngược lại after.
* alpha: kiểm tra xem data gửi lên có phải các ký tự alphabetic.
* alpha_num: kiểm tra xem data gửi lên có phải các ký tự alphabetic và số.
* array: kiểm tra data có phải là mảng ko.
* bail: sẽ dừng validate nếu như có một rule nào đó fail.
* between:min,max -> kiểm tra giá trị có nằm trong khoảng min-max không.
* confirmed: kiểm tra xem dữ liệu có giống với dữ liệu của field_confirmation kô. VD xác thực nhập lại password.
* date: kiểm tra xem data gửi lên có là date không.
* dimensions: kiểm tra kích thước của ảnh upload. VD: `avatar => 'dimensions:min_width=100,min_height-200`
* email: kiểm tra data có phải là email không.
* image: Kiểm tra data có phải là image không.
* in_array:anotherfield -> kiểm tra data gửi lên có nằm trong mảng anotherfield.
* integer: kiểm tra xem data có phải là số nguyên không.
* ip:
* ipv4:
* ipv6:
* json:
* max: value;
* min:value;
* not_in:foo,bar; kiểm tra data gửi lên không nằm trong mảng.
* nullable:
* numberic:
* regex:pattern; kiểm tra data phải matching với pattern.
* required:
* string:
* unique:table,column,except,idColumn: kiểm tra data bắt buộc phải chưa xuất hiện với điều kiện ở DB.
* url:

# 3. Thêm mới rule

- Ta có thể tự tạo Rule riêng với sử dụng command: `make:rule`. Command này sẽ tạo một rule trong `app/Rules` để ta thêm logic code.
- VD: tạo thêm rule Uppercase để kiểm tra xem data ta có đang in hoa ko

```php
php artisan make:rule Uppercase
```

- Khi này sẽ có file Rule mới như sau:

```php
//file: app/Rules/Uppercase.php
public function passes($attribute, $value)
{
    //
}

public function message()
{
    //
}
```

- Trong đó:
  - method passes nếu trả về true thì rule sẽ được pass và ngược lại.
  - method message sẽ trả về message string nếu như rule không pass.
- Khi này, khi muốn áp dụng rule vào validator ta sử dụng như sau:

```php
use App\Rules\Uppercase;

$request->validate([
    'name' => ['required', 'string', new Uppercase],
]);
```

- Ngoài cách tạo thêm rule mới, ta có thể đưa logic vào trong một Closure với cú pháp:

```php
function ($attribute, $value, $fail) {
    if ($value == 'foo') {
        $fail('The '.$attribute.' is invalid.');
    }
}
```

- Trong đó:
  - $attribute là tên của field cần validate
  - $value là giá trị của trường cần validate.
  - $fail là một callback function sẽ được gọi khi valiate
- VD:

```php
use Illuminate\Support\Facades\Validator;

$validator = Validator::make($request->all(), [
    'title' => [
        'required',
        'max:255',
        function ($attribute, $value, $fail) {
            if ($value === 'foo') {
                $fail('The '.$attribute.' is invalid.');
            }
        },
    ],
]);
```
