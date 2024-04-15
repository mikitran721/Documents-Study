# 15. Hàm xử lý chuỗi
## addcslashes($str, $char_list)
- Hàm này chèn `\` vào trước các ký tự trong chuỗi `$str` với các ký tự được liệt kê bởi `$char_list`
- VD
```php
echo addcslashes("Poo Phoong", "o");
//output: P\o\o Ph\o\ong

echo addcslashes("Poo Phoong", "a..zA..Z")
//output: \P\o\o \P\h\o\o\n\g
```
## addslashes($str)
- Hàm này sẽ thêm ký tự `\` vào trước các ký tự nháy đơn `'` trong chuỗi $str nếu có
- VD
```php
echo addslashes("Poo Ph'o'ong");
//output: Poo Ph\o\ong
```
## bin2hex($str)
- Hàm này chuyển đổi chuỗi về dạng ASCII HEX của từng ký tự trong chuỗi $str.
## chop($string, $charList)
- Hàm này xóa ký tự, hoặc từ cuối của chuối nếu nó = $charList
- VD
```php
echo chop("Poo Phoong ngao", "ngao");
//output: Poo Phoong
```
## crc32($string)
- Hàm này chuyển chuỗi $string thành một số nguyên
## explode($separator, $string, $limit)
- Hàm này tách chuỗi $string thành một array với phần tử là các chuỗi khác nhau với điều kiện $separator, và giới hạn $limit.
- VD:
```php
$data = explode('o', "poo phoong");
print_r($data);

$data = explode('o', 'poo phoong', 2)
print_r($data)
//output: Array ( [0] => p [1] => o phoong)
```
## implode($separater, $array)
- Hàm này có tác dụng nối tất cả các phần tử của $array thành chuỗi với khoảng phân biệt $separator
- vd
```php
$data = [
    'Poo',
    'Phoong',
    'Ben',
    'Tom'
];
echo implode('-', $data);
echo implode($data);
```
## strlen($string)
- Hàm này đếm xem chuỗi $string có bao nhiêu ký tự
## str_word_count($string)
- Hàm này đếm xem chuỗi $string có bao nhiêu từ
## str_repeat($string, $repeat)
- Hàm này có tác dụng lặp chuỗi $string $repeat lần
## str_replace($find, $replace, $string)
- Hàm này có tác dụng tìm kiếm chuỗi $find và thay thế chuỗi đó bằng $replace trong chuỗi $string.
- vd
```php
$data = 'poo phoong ngao';
echo str_replace('o', 'e', $data);
//output: pee pheeng ngao
```
## md5($string)
- Hàm này chuyển chuỗi $string sang mã hóa md5.
## sha1($string)
- Hàm này có tác dụng mã hóa chuỗi $string sang sha1.
## htmlentities($string), htmlspecialchars($string)
- Hàm này có tác dụng chuyển đổi dữ liệu sang dạng thô.
## htmlspecialchars_decode($tring), html_entity_decode($string)
- Hàm này có tác dụng chuyển đổi dữ liệu về dạng thực của nó.
## strip_tags($string, $allow)
- Hàm này có tác dụng loại bỏ các thẻ html trong chuỗi $string, trừ các thẻ được cho phép $allow
- vd
```php
$data = "<b>poo phoong</b>";
echo strip_tags($data);
echo strip_tags($data, '<b>');
```
## substr($string, $start, $length)
- Hàm này cắt chuỗi $string, bắt đầu ở $start và có giới hạn $length;
```php
$data = "Hello world";
echo substr($data, 10); //d
echo substr($data, -1); //d
echo substr($data, 0, 10); //Hello worl
echo substr($data, 0, -1); //Hello worl
```
## strtolower($string)
- Chuyển chuỗi $string sang in thường.
## strtoupper($string)
- Chuyển chuỗi $string sang in hoa.
## ucword($string)
- chuyển đổi chữ cái đầu tiên của $string sang in hoa.
## trim($string, $charList)
- Loại bỏ khoảng trắng ở 2 đầu chuỗi $string hoặc loại các ký tự $charList trong chuỗi $string.
```php
echo trim(' poo ngao ');
echo trim('eepoo ngaoee', 'e');
```
- tương tự có `ltrim($string, $charList) & rtrim($string, $charList)` với cắt bên trái, bên phải.

# 16. ARRAY
## count($array)
- Hàm này có tác dụng đếm xem trong $array có bao nhiêu phần tử

## array_values($array)
- Đưa $array về dạng mảng tuần tự
```php
$arr = [
    'domain' => 'mikitran.php',
    'desc' => 'Lap trinh PHP'
];
print_r(array_values($arr));
//output: Array ( [0] => mikitran.php [1] => Lap trinh PHP )
```
## array_keys($array)
- Trả về một mảng tuần tự với phần tử là key của mảng ban đầu
```php
$arr = [
    'domain' => 'mikitran.php',
    'desc' => 'Lap trinh PHP'
];
print_r(array_keys($arr));
//output: Array ( [0] => domain [1] => description )
```
## array_pop($array)
- Hàm này trả về phần tử cuối cùng của array

## array_push($array, $var, $var,...)
- Hàm này thêm một hoặc nhiều $var vào cuối $array và trả về số lượng phần tử của $array sau khi thêm.

## array_shift($array)
- Hàm này xóa phần tử đầu tiên của $array và trả về phần từ vừa xóa.

## array_unshift($array, $var, $var,...)
- Hàm này thêm một hoặc nhiều phần tử vào đầu mảng, và trả về số lượng phần tử của mảng sau khi thêm.

## array_flip($array)
- Hàm này chuyển đổi key của mảng thành value và ngược lại.

## sort($array)
- Hàm này sắp xếp lại mảng theo ASC và trả về TRUE nếu thành công và ngược lại.

## array_reverse($array)
- Hàm này có tác dụng đảo ngược lại vị trí của các phần tử trong mảng.
```php
$arr = [5, 4, 3, 2, 1];
print_r(array_reverse($arr));
//output: Array ( [0] => 1 [1] => 2 [3] => 3 [4] => 4 [5] => 5 )
```

## array_merger($array1, $array2)
- Hàm có tác dụng gộp hai hay nhiều mảng thành một mảng.

## array_rand($array, $number)
- Lấy ra key ngẫu nhiên của $array với $number phần tử muốn lấy.
```php
$arr = [
    'domain' => 'miki.php',
    'desc' => 'love PHP'
];

print_r(array_rand($arr));
//output: domain
print_r(array_rand($arr, 2));
//output: Array ( [0] => domain [1] => desc )
```

## array_search($keyword, $array)
- Tìm kiếm giá trị của mảng thỏa mãn $keyword và trả về key của phần tử nếu có.

## array_slice($array, $begin, $length)
- Lấy ra $length phần tử bắt đầu từ $begin trong $array

## array_unique($array)
- Loại bỏ các phần tử trùng nhau trong $array và trả về một mảng mới sau khi đã loại bỏ.

## serialize($input)
- Chuyển đổi một chuỗi, số, mảng, object thành một chuỗi được mã hóa.

## unsirialize($input)
- Hàm có tác dụng decode chuỗi được mã hóa bởi `serialize`
```php
$arr = [
    'domain' => 'miki.php',
    'desc' => ' love PHP'
];
print_r($a = serialize($arr));
print_r(unsirialize($a));
```

## array_key_exists($key, $array)
- Kiểm tra xem mảng $array có tồn tại khóa $key không, nếu có trả về TRUE và ngược lại.
```php
$arr = [
    'domain' => 'miki.php',
    'desc' => 'love PHP'
];
print_r(array_key_exists('domain',$arr));
```

## in_array($value, $array)
- Kiểm tra xem mảng $array có tồn tại giá trị $value không? và trả về TRUE nếu có và ngược lại.

## array_diff($array1, $array2, $array3,...)
- Trả về mảng chứa các phần tử (value) có trong mảng $array1 nhưng không có trong mảng $array2, $array3, ...
```php
$arr1 = [
    'domain' => 'miki.php',
    'desc' => 'love PHP'
];
$arr2 = [
    'domain' => 'miki.php',
    'desc' => 'love PHP 2'
];
print_r(array_diff($arr1, $arr2));
//output: Array ( [desc] => love PHP )
```

## array_diff_assoc($array1, $array2, ...)
- Hàm này trả về một mảng có các `key` mà trong $array1 có nhưng các $array2, ... ko có.
```php
$arr1 = [
    'domain' => 'miki.php',
    'desc' => 'love PHP'
];
$arr2 = [
    'domain' => 'miki.php',
    'desc2' => 'love PHP 2'
];
print_r(array_diff($arr1, $arr2));
//output: Array ( [desc] => love PHP )
```

## array_intersect($array1, $array2, ...)
- Hàm này trả về mảng các phần tử giống nhau về `value` giữa các mảng $array

## array_intersect_assoc($array1, $array2, ...)
- Hàm này có trả về mảng chứa các phần tử giống nhau cả `key` và `value` trong các mảng $array...

## is_array($array)
- Kiểm tra $array có phải là mảng ko, nếu đúng trả về TRUE ngược lại là FALSE.

# 17. CÁC HÀM XỬ LÝ FILE
## 17.1 Mở file
- dùng cú pháp
```php
@fopen(command, mode)
//command: đường dẫn
//mode: quyền truy cập vào file
/*
    r: read only
    r+: Read + write
    w: write only
    w+: write + read. Nếu file tồn tại thì nội dung cũ bị xóa đi và ghi lại nội dung mới, còn nếu file chưa tồn tại sẽ tạo file mới
    a: write; nếu file tồn tại sẽ ghi tiếp nội dung phía dưới, nếu file ko tồn tại sẽ tạo file mới
    a+: read + write; nếu file tồn tại sẽ ghi tiếp nội dung phía dưới, nếu file ko tồn tại sẽ tạo file mới
    b: mở dưới dạng nhị phân binary
*/
```
- VD:
```php
$file = @open('data.txt', 'r');
if(!$file)
    echo 'mo file no ok';
else
    echo 'mo file ok';
```
## 17.2 Đọc file
- Có 3 cách đọc file
### Đọc từng ký tự
- Sử dụng hàm `feof` để kiểm tra xem đã ở vị trí cuối của file chưa và hàm `fgetc` để đọc từng ký tự của file.
- VD
```php
$file = @popen('data.txt', 'r');
if(!$file)
    echo 'mo file no ok';
else {
    while (!feof($file)) {
        echo fgetc($file);
    }
}
```
### Đọc từng dòng
- C1: Ở cách này ta vẫn dùng `feof` để kiểm tra vị trí đang duyệt đã ở cuối cùng của file chưa và dùng `fgets` để in ra từng hàng của file.
- VD
```php
$file = @popen('data.txt', 'r');
if(!$file)
    echo 'mo file no ok';
else {
    while (!feof($file)) {
        echo fgets($file);
    }
}
```
- C2: Hoặc ta có thể dùng hàm `file` để lấy file theo dạng mảng với mỗi dòng của file là một phần tử của mảng.
- VD
```php
$data = @file('data.txt', 'r');
if(!$file)
    echo 'mo file no ok';
else {
    foreach ($data as $value) {
        echo $valule;
    }
}
```
### Đọc hết file 1 lần
- Ta dùng hàm `fread($file, $filesize)`; Nó sẽ load nặng và lâu nếu file lớn.
- VD
```php
$file = @popen('data.txt', 'r');
if(!$file)
    echo 'mo file no ok';
else {
    echo fread($file, filesize('data.txt'));
}
```
## 17.3 Ghi file
- Để ghi file bắt buộc file phải được mở ở mode cho phép ghi và tiếp đó dùng hàm `fwrite` để ghi dữ liệu.
- VD
```php
$file = @popen('data.txt', 'w');
if(!$file)
    echo 'mo file no ok';
else {
    $data = "new content";
    fwrite($file, $data);
    fclose($file);
}
```

## 17.4 Đóng file
- Khi mở luôn luôn phải đóng file lại. Ta dùng hàm `fclose` để đóng file.
- cú pháp `fclose($file)`

## 17.5 Các hàm khác
### Kiểm tra file tồn tại
- Sử dụng hàm `file_exists()` để kiểm tra sự tồn tại của file, true nếu có và false nếu sai.
### Kiểm tra quyền ghi
- Dùng hàm `is_writeable()` để xem, nếu có thì true, sai là false.
### Lấy nội dung của file
- Để lấy nội dung của một file, ta có thể dùng `file_get_contents()`.
### Tạo thư mục
- Ta có thể dùng hàm `mkdir(path, mode, recursive, content)`
### Kiểm tra thư mục
- Để xem thư mục có tồn tại ko, sử dụng hàm `is_dir(path)`
### Đổi tên file
- Để đổi tên 1 file, ta dùng hàm `rename(old, new)`;
### copy file
- Ta sử dụng hàm `copy(file1, file2)` để copy file
### Xóa file
- Ta sử dụng hàm `unlink(file)` để xóa file.