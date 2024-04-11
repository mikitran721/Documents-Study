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
# 21. Magic methods with function __call() & __callStatic()
## __call()
- Phương thức __call() sẽ đc gọi khi ta gọi một method ko được phép truy cập từ bên ngoài | ko tồn tại. Ko áp dung với static function.
- Syntax:
```php
public function __call($methodName, $arguments)
{
    //code
}
```
- VD:
```php
class ConNguoi
{
    private $name = 'Poo Phoong';
    private $age = 100;

    public function __call($methodName, $arguments)
    {
        echo 'Call method: ' . $methodName . ' & arguments: ' . implode('-' , $arguments);
    }

    private function getInfo()
    {
        echo $this->name + ' - ' + $this->age;
    }
}

$cn = new ConNguoi();

$cn->getInfo();
//output: Call method: getInfo & arguments:

$cn->getInfo('name','age');
//output: Call method: getInfo & arguments: name - age
```
## __callStatic()
- phương thức __callStatic() sẽ đc gọi khi ta gọi một function `tĩnh` ko được phép truy cập từ bên ngoài hoặc ko tồn tại
- syntax:
```php
public static function __callStatic($methodName, $arguments)
{
    //code
}
```
- VD: 
```php
class ConNguoi
{
    private $name = 'Poo Phoong';
    private $age = 150;

    public static function __callStatic($methodName, $arguments)
    {
        echo 'Call method: ' . $methodName . ' & arguments: ' . implode('-' , $arguments);
    }

    private static function getInfo()
    {
        echo $this->name . "+" . $this->age;
    }
}

ConNguoi::getInfo();
//output: Call method: getInfo & arguments:

ConNguoi::getInfo('name', 'age');
//output: Call method: getInfo & arguments: name-age
```
# 22. Magic methods sleep & wakeup
## __sleep()
- Phương thức __sleep() sẽ được gọi khi ta thực hiện `serialize()`.
- Thông thường khi ta `serialize()` một obj thì nó sẽ trả về all các prop trong obj đó. Nhưng nếu sd `__sleep()` thì ta có thể quy định được các prop có thể trả về. `__sleep()` luôn trả về giá trị là một array.
- syntax:
```php
public function __serialize()
{
    return ['prop1', 'prop2', ..., 'propn'];
}
```
- VD:
```php
class ConNguoi
{
    private $name = "poo phoong";
    private $age = 200;

    public function __sleep()
    {
        return array('name');
    }
}
```
## __wakeup()
- phương thức __wakeup() sẽ đc gọi khi ta unserialize() obj.
- Thường đc sử dụng để thực thi một hoặc nhiều hành động nào đó khi đối tượng được unsirialize().
- syntax:
```php
public function __wakeup()
{
    //code
}
```
- VD:
```php
class ConNguoi
{
    private $name = "poo phoong";
    private $age = 200;

    public function __sleep()
    {
        return array('name');
    }

    public function getName()
    {
        echo $this->name;
    }

    public function __wakeup()
    {
        $this->getName();
    }
}

unserialize(serialize(new ConNguoi));
```
# 23. Magic methods __toString() & __invoke()
## __toString()
- Bình thường ta không thể dùng `echo` với 1 instance của obj - Vì nó không phải là `string`. Nhưng với __toString() sẽ được gọi khi ta dùng obj như 1 string.
- syntax:
```php
public function __toString()
{
    //code
}
```
- VD:
```php
class ConNguoi
{
    private $name = 'Poo Phoong';
    private $age = 20;

    public function __toString()
    {
        return 'Phuong thuc __toString() duoc goi';
    }
}
echo new ConNguoi();
//output: Phuong thuc __toString() duoc goi
```
## __invoke()
- Phương thức __invoke() sẽ được gọi khi chúng ta sử dụng đối tượng như một hàm.
- syntax:
```php
public function __invoke(arguments)
{
    //code
}
```
- VD:
```php
class ConNguoi
{
    private $name = "poo phoong";
    private $age = 200;

    public function __invoke()
    {
        echo 'Phuong thuc __invoke() duoc goi';
    }
}

$cn = new ConNguoi();
$cn();
//output: Phuong thuc __invoke() duoc goi
```
# 24. Magic methods __set_state() & __clone() & __debugInfo()
## __set_state()
- Phương thức này sẽ được gọi khi ta var_export() object.
- syntax:
```php
public static function __set_state($arr)
{
    //code
}
```
- VD:
```php
class ConNguoi
{
    private $name = "Poo Phoong";
    private $ate = 200;

    public static __set_state(array $arr)
    {
        foreach ($arr as $key => $value) {
            echo $key . '->' . $value . '<br/>';
        }
    }
}

$cn = new ConNguoi();
eval(var_export($cn, true) . ';');
```
## __clone()
- Phương thức __clone() đươc gọi khi ta clone object.
- syntax:
```php
public function __clone()
{
    //code
}
```
- VD:
```php
class ConNguoi
{
    public $name = 'Poo Phoong';
    public $age - 200;

    public function __clone()
    {
        echo '__clone() duoc goi';
    }
}

$cn = new ConNguoi();

$cn2 = clone $cn;
//output: __clone() duoc goi

echo $cn2->name;
```
## __debugInfo()
- Phương thức __debugIno() được gọi khi ta var_dump() obj. Thường, khi ta var_dump() obj thì nó sẽ trả về tất cả các prop và giá trị của nó, nhưng khi ta sử dụng __debugInfo() thì ta có thể tùy chỉnh thông số trả về.
- VD:
```php
class ConNguoi
{
    public $name = "poo phoong";
    public $age = 300;

    public function __debugInfo()
    {
        return [
            'name' => $this->name,
        ];
    }
}

$cn = new ConNguoi();
var_dump($cn);
```