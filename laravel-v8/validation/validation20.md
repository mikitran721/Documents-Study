# 1. Tổng quan về validation

- Laravel cung cấp cho ta nhiều cách để xác thực, kiểm tra dữ liệu. Trong đó, cách phổ biến nhất là xác thực các dữ liệu client gửi lên HTTP request.
- Trong Laravel, validation được support rất mạnh và sử dụng cũng như mở rộng tương đối dễ.
- Nội dung:
  1. Sử dụng method validate
  2. Sử dụng form request
  3. Sử dụng validator

# 2. Validation sử dụng method `validate`

- Để validate dữ liệu request gửi lên, ta có thể sử dụng method `validate` với cú pháp: `$request->validate($rules, $customMessage);`. Trong đó:
  - $rules: là một mảng dữ liệu với key là cac input name, value là các rule mà cần ràng buộc.
  - $customMessage: là một mảng dữ liệu chứa các custom message, có thể bỏ trống.
- VD: validate input id bắt buộc phải có và là kiểu số

```php
Route::get('store', function (Request $request) {
    $request->validate([
        'id' => 'required|integer'
    ]);
    //or
    $request->validate([
        'id' => ['required', 'integer']
    ]);
})->name('store');
```

- Trong TH validate mà không pass, Laravel sẽ trả về một response redirect về request trước đó, kèm theo các error message được flash xuống session.
- Nếu trong TH ta cần Laravel response về luôn JSON error code, ta cần truyền thêm tham số `accept header appliation/json` hoặc request của ta là một XHR request.

# 3. Validation sử dụng form request

- Laravel cung cấp cho ta thêm 1 cách để tạo ra các validate nằm ở trong file riêng biệt, chứa các logic phục vụ cho việc validate data và có thể tái sử dụng trong những TH tương tự.
- Để tạo form request, ta sử dụng command: `php artisan make:request FormRequestName`. VD: tạo ra StoreContactRequest: `php artisan make:request StoreContactRequest`.
- Lúc này, Laravel sẽ sỉnha một file `app/Http/Request/StoreContactRequest.php`. Có nội dung

```php
public function authorise()
{
    return flase;
}

public function rules()
{
    return [
        //
    ];
}
```

- Trong đó: method `authorize` sẽ quyết định client có được thực thi request này không. Nếu trả về true thì client được phép và ngược lại; Method `rules` sẽ chứa các rule mà ta cần validate. VD:

```php
//file: app/Http/Request/StoreContactRequest.php
public function authorize()
{
    return true;
}

public function rules()
{
    return [
        'id' => ['required', 'integer']
    ];
}

//file: routes/web.php
use App/Http/Request/StoreContactRequest;

Route::get('store', function (StoreContactRequest $request){
    return 'pass';
})->name('store');
```

- Đối với form request, ta có thể custom được error message bằng cách trả về message trong method `message`. VD:

```php
//file StoreContactRequest.php
public function message()
{
    return ['id.required' => 'ID là bắt buộc'];
}
```

- Ta có thể sửa attribute name trong form request bằng cách sử dụng method `attributes`.
- VD: sửa attribute 'id' thành 'contact id'

```php
//file: StoreContactRequest.php
public function attributes()
{
    return [
        'id' => 'Contact id'
    ];
}
```

- Trong một số TH, ta cần thực hiện thêm một số logic sau khi validate thực thi. Lúc này ta có thể sử dụng method `after`. VD:

```php
//file: StoreContactRequest.php
public function withValidator($validator)
{
    $validator->after(function ($validator) {
        //logic
    });
}
```

- Tương tự, ta có thể chỉnh sửa input data trước khi thực hiện validate bằng cách sử dụng method `prepareForValidation`.
- VD: Xử lý slug input về dạng đường dẫn trước khi validate

```php
protected function prepareForValidation()
{
    $this->merge([
        'slug' => Str::slug($this->slug)
    ]);
}
```

# 4. Validation sử dụng Validator

- Ta còn có thể sử dụng class `Validator` để validate với cú pháp

```php
Validator::make($inputs, $rules, $message, $attributes);
```

- Trong đó:
  - $inputs: là một mảng dữ liệu chứa các data cần validate.
  - $rules: là một mảng dữ liệu với key là các input name, value là các rule mà ta muốn ràng buộc.
  - $messages: là một mảng dữ liệu chứa các custom message, có thể bỏ trống.
  - $attributes: là một mảng dữ liệu chứa các custom attribute, có thể bỏ trống.
- VD:

```php
Route::get('store', function (Request $req) {
    $validator = Validator::make($req->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ]);
})->name('store');
```

- Khi này để thực hiện validate, ta sử dụng method `validate` tiếp theo đó.

```php
Route::get('store', function (Request $req) {
    Validation::make($req->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required'
    ])->validate();
})->name('store');
```

- Ta có thể vừa validate và kiểm tra xem validate có lỗi hay không bằng cách sử dụng method `fails`. VD:

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use Illuninate\Support\Facades\Validator;

Route::get('store', function (Request $req) {
    $validator = Validator::make($req->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required'
    ]);
    if ($validator->fails()) {
        //code
    }
})->name('store');
```
