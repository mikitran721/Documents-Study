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