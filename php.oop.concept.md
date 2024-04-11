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
