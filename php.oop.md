# Hàm __autoload()
- được gọi khi ta khởi tạo một obj ko xác định được
- VD:
```php
//Khai báo
function __autoload($classname)
{
    echo 'abc';
}

//Khoi tao class ConNguoi
$cn = new ConNguoi();
//output: abc

```
## Ung dung
- Do __autoload() tự động được gọi khi khởi tạo 1 đối tượng kxđ, nên nó đc ứng dụng vào vc tạo ra các ứng dụng autoloading classes.
- VD:
- file NguoiLon.php
```php
class NguoiLon
{
    public function __construct()
    {
        echo 'Class Nguoi lon';
    }
}
```
- file TreCon.php
```php
class TreCon
{
    public function __construct()
    {
        echo 'Class Tre con';
    }
}
```
- Ứng dụng __autoload() trong file index.php
```php
//khai báo __autoload()
public function __autoload($className)
{
    //kiem tra file ton tai
    if(file_exists($className . '.php')){
        include_once $className . '.php';
    }
}
//khoi tao 2 class
$nl = new NguoiLon();
$tc = new TreCon();
```
## ứng dụng __autoload() với static method
- Hàm __autoload() cũng được gọi khi ta gọi phương thức tĩnh không xác định.
- VD:
```php
//1. File ConNguoi.php
class ConNguoi
{
    private static $name = 'ConNguoi';

    public static function getName()
    {
        echo static::$name;
    }
}

//file index.php
//khai bao __autoload()
function __autoload($className)
{
    //kiem tra file ton tai khong
    if(file_exist($className . '.php')){
        include_once $className . '.php';
    }
}

ConNguoi::getName();
//output: ConNguoi
```