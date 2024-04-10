# 17. Hàm __autoload()
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
    if (file_exist($className . '.php')) {
        include_once $className . '.php';
    }
}

ConNguoi::getName();
//output: ConNguoi
```

# 19. Magic methods SET & GET
- Cả phương thức __set() và __get() trong PHP đều cho phép ta truy cập vào các thuộc tính mà phạm vi của nó không cho phép ta tác động từ bên ngoài như `private`;
## __set()
- Phương thức __set() sẽ tự động được gọi khi ta thiết lập giá trị cho một prop không được phép truy cập từ bên ngoài, hoặc ko tồn tại.
- cú pháp:
```php
public function __set($key, $value)
{
    //$key: tên của prop cần xét
    //$value: giá trị của prop cần xét
}
```
- VD:
```php
class ConNguoi
{
    private $name;

    public function __set($key, $value)
    {
        //kiem tra co ton tai prop ko
        if (property_exists($this, $key)) {
            //gan gia tri
            $this->$key = $value;
        } else {
            die('Khong ton tai thuoc tinh');
        }

    }

    public function getName()
    {
        echo $this->name;
    }
}

$cn = new ConNguoi();
$cn->name = 'Poo Phoong';
$cn->getName(); //output: Poo Phoong

$cn->age = 100; //die: Khong ton tai thuoc tinh
```
## __set()
- phương thức __get() sẽ auto được gọi khi ta lấy ra giá trị của các prop trong object mà ko được phép truy cập từ bên ngoài hoặc prop không tồn tại.
- syntax:
```php
    public function __get($key)
    {
        //$key: tên của prop ta muốn lấy ra
    }
```
- VD
```php
class ConNguoi
{
    private $name = "Poo Phoong";

    public function __get($key)
    {
        //kiem tra co ton tai prop ko
        if (property_exist($this, $key)) {
            return $this->$key;
        } else {
            die('Khong ton tai prop');
        }
    }

    public function getName() 
    {
        echo $this->name;
    }
}

$cn = new ConNguoi();
echo $cn->name; //Poo Phoong

$cn->age(); //khong ton tai prop
```
- Ưu và nhược điểm của __set() và __get():
  - Ưu: 1. Giúp ta truy xuất các prop không nhìn thấy được của obj (nhằm đảm bảo tính đóng gói); 2. Hạn chế cũng như custom lại thông báo lỗi khi ta truy cập đến các prop nội bộ hoặc ko tồn tại;
  - Nhược: làm CT chậm hơn chút ít.

# 20. Magic methods ISSET & UNSET
## __ISSET()
- Phương thức __isset() sẽ được gọi khi ta thực hiện kiểm tra một prop không được phép truy cập | ko tồn tại của một obj. (khi su dung: isset() và empty() ).
- __isset() không sử dụng được với static prop.
- syntax:
```php
public function __isset($name)
{
    //$name: là prop cần kiểm tra
}
```
- VD không sử dụng __isset()
```php
class ConNguoi
{
    private $name;
}

$cn = new ConNguoi();

isset($cn->name); //false

empty($cn->name); //true
```
- VD sử dụng __isset()
```php
class ConNguoi
{
    private $name;

    public function __isset($name)
    {
        echo "Check: " . $name;
    }
}

$cn = new ConNguoi();

isset($cn->name); //Check: name
empty($cn->name); //Check: name
isset($cn->age); //Check: age
```
## __UNSET()
- Phương thức __unset() sẽ được gọi khi ta thực hiện hủy (unset) một prop không được phép truy cập hay ko tồn tại của obj cụ thể. Cụ thể là dùng hàm unset(). Phương thức __unset() không áp dụng với static prop;
- VD không sử dụng __unset()
```php
class ConNguoi
{
    private $name;
}

$cn = new ConNguoi();

unset($cn->name); //output: cannot access private property ConNguoi::$name
```
- VD khi sử dụng __unset()
```php
class ConNguoi
{
    private $name;

    public function __unset($name)
    {
        echo "Remove: " . $name;
    }
}

$cn = new ConNguoi();

unset($cn->name); //Remove: name

unset($cn->age); //Remove: age
```