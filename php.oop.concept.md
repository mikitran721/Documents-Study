# 5. Tính chất đặc thù của OOP
## 5.1 Tính kế thừa
- Một obj có thể có được các đặc tính (prop, med) mà obj khác đã có thông qua kế thừa (extends). Điều này cho phép các obj chia sẻ hay mở rộng các đặc tính sẵn có mà không phải tiến hành định nghĩa lại.
## 5.2 Tính đa hình
- Thể hiện rõ nhất qua các method của obj khi thừa kế từ daddy, cùng tên 1 method nhưng việc xử lý luôn có thể khác nhau.
## 5.3 Tính đóng gói
- Tính chất này ko co phép user sử dụng các obj thay đổi trạng thái nội tại của một obj. Chỉ có các local method của obj cho phép thay đổi trạng thái của nó.
- Việc cho phép môi trường bên ngoài tác động lên các dữ liệu nội tại của obj là hoàn toàn tùy thuộc vào coder.
## 5.4 Tính trừu tượng
- Thể hiện rõ nhất ở việc, một obj ban đầu có thể có một số đặc điểm chung cho nhiều obj # như là sự mở rộng của nó nhưng bản thân obj ban đầu có thể không có biện pháp thi hành (vd: việc định nghĩa hàm mà không mô tả nó)
# 7. Lớp trừu tượng Abstract
- Một lớp được khai báo là `abstract` thì đó là lớp trừu tượng. Syntax:
```php
abstract class ClassName
{
    //code
}
```
- Cấu trúc `abstract + visibility`.
- Các đặc điểm của abstract class:
  - các method khi được khai báo là `abstract` thì chỉ được định nghĩa chứ không được viết code xử lý trong nó.
  - Nếu ko là phương thức `abstract` thì vẫn khai báo và viết code như bình thường.
  - phương thức `abstract` chỉ có thể khai báo trong class abstract.
  - Không thể khởi tạo một abstract class. Do đó, các method khi khai báo chỉ được ở mức `public | protected`.
  - Thuộc tính không được khai báo với key `abstract`.
  - Phải định nghĩa lại các abstract method của abstract class đó khi kế thừa.
# 8. Interface trong OOP PHP
- Interface trong OOP là một khuôn mẫu, giúp ta tạo ra bộ khung cho một hoặc nhiều obj và nhìn vào Interface ta hoàn toàn có thể xác định được các methods và các prop cố định (const) trong đối tượng `implement` nó.
- Khai báo:
```php
interface InterfaceName
{
    //code
}
```
- Các tính chất của interface:
  - Interface không phải là obj.
  - Chỉ được khai báo method chứ ko được định nghĩa chúng.
  - Có thể khai báo được hằng (const) nhưng ko thể khai báo biến (var)
  - Không thể khởi tạo được.
  - các lớp `implement` interface thì phải khai báo và định nghĩa lại các method có trong interface đó.
  - Một class có thể implement nhiều interface.
  - Các interface có thể kế thừa lẫn nhau.
# 11. Namespace
- Khắc phục việc class trùng tên nhau.
- Khai báo với `namespace Name;`
- Khi khai báo namespace ta phải đặt nó phía trên cùng của file.
- Có thể đặt tên namespace theo các cấp được; VD `namespace App\Controller`
- Khi một class đã được một namespace định danh thì ta sẽ không thể gọi theo cách thông thường được nữa, mà cần gọi với cú pháp `new tenNamespace\tenClass();`
- VD:
```php
//ConNguoi.php
namespace ConNguoi;

class ConNguoi
{
  private $name = 'Poo Phoong';

  public function getName()
  {
    return $this->name;
  }
}

//index.php
include 'ConNguoi.php';

$cn = new ConNguoi\ConNguoi();

echo $cn->getName();
```
- Nạp namespace bằng `use`: nếu như không muốn viết dài có thể dùng từ khóa `use` để nạp theo cú pháp: `use tenNamespace\tenClass;` khi này khi tạo mới obj chỉ cần viết tên của Class mà thôi.
- VD:
```php
include 'ConNguoi.php';

use ConNguoi\ConNguoi;

$cn = new ConNguoi();

echo $cn->getName();
```
- Một file có namespace và use thì viết use ở dưới.
- Có thể dùng alias cho namespace, nhưng khi này ta vẫn cần viết đầy đủ `aliasNamespace\className` khi tạo mới obj.
- VD:
```php
include 'ConNguoi.php';

use ConNguoi as People;

$cn = new People\ConNguoi();

echo $cn->getName();
```
# 12. Traits trong PHP
## 12.1 Traits 01 - The concept
- PHP là ngôn ngữ chỉ hỗ trợ đơn kế thừa, nên việc muốn sử dụng lại source code một cách nhiều lần là khó khăn. Từ PHP 5.4 trở lên đã hỗ trợ Traits để khắc phục.
- Traits là 1 module giúp ta có thể sử dụng lại các phương thức được khai báo trong `trait` vào các class khác nhau thay vì kế thừa như trước.
- Các đặc điểm của Traits:
  - Có chức năng gom lại các method, prop mà ta muốn sử dụng lại nhiều lần.
  - Không thể khởi tạo được.
  - Các method trong Traits có thể bị override lại trong class sử dụng nó.
  - có thể sử dụng `namespace` trong Traits.
  - Có thể sử dụng `trait` lồng nhau
- Cú pháp:
```php
trait A
{
  //code
}

trait B
{
  use A;
  //code
}
```
- Sử dụng Trait trong class, sử dụng cú pháp:
```php
class ClassName
{
  use TraitName;
  
  //code
}
```
- Ưu tiên phương thức trong Traits:
  - khi cùng lúc 2 class có cùng 1 method giống tên nhau thì sẽ tạo ra lỗi khi sử dụng. Để khắc phục có thể thực hiện
  - 1. Override lại tên phương thức bị trùng tại class sử dụng traits
  - 2. Sử dụng cú pháp  vơi key `insteadof` sau khi khai báo sử dụng traits:
  ```php
  class ConNguoi
  {
    private $name;
    private $age;

    use SetGetName, SetGetAge {
      SetGetAge::getAll insteadof SetGetAge;
    }
  }
  ```
- VD:
```php
//file: SetGetName
trait SetGetName
{
  public function setName($name)
  {
    $this->name = $name;
  }

  public function getName()
  {
    return $this->name;
  }
}

//file: SetGetAge
namespace SetGetAge;

trait SetGetAge
{
  public function setAge($age)
  {
    $this->age = $age;
  }

  public function getAge()
  {
    return $this->age;
  }
}

//file: index.php
include 'SetGetName.php';
include 'SetGetAge.php';

//use namespace
use SetGetAge/SetGetAge;

class ConNguoi
{
  private $name;
  private $age;

  //goi Traits
  use SetGetName;
  use SetGetAge;
}

$cn = new ConNguoi();

$cn->setName('Poo Phoong');
echo $cn->getName();

$cn->setAge(100);
echo $cn->getAge();
```
## 12.2 Traits 02
- Trong Traits hỗ trợ ta tạo cả phương thức tĩnh và thuộc tính tĩnh như class bình thường. Nhưng thuộc tính tĩnh chỉ ảnh hưởng bên trong 1 class thôi, còn khi gọi nó ở một class khác thì giá trị lại bình thường.Tức là ở 2 class khác nhau gọi sử dụng Traits thì không ảnh hưởng giá trị sang nhau.
- Trong traits cũng hỗ trợ tạo phương thức `abstract` và khi class gọi sử dụng trait này cũng phải khai báo định nghĩa lại method đó.
- Có thể thay đổi `visibility` của phương thức trong traits với cú pháp
```php
use methodName as visibility newName; 
//visibility: public, protected, private
//newName la ten alias cho method
```
- VD đổi visibility
```php
trait Name
{
  private $name = 'poo phoong';

  private function getName()
  {
    return $this->name;
  }
}

class ConNguoi
{
  use Name {
    getName as public;
  }
}

$cn = new ConNguoi();
echo $cn->getName();
```
