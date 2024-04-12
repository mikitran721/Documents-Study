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
