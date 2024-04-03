# BẢO MẬT TRONG LARAVEL

## CSRF

## MASS ASSIGNMENT

- một lỗ hổng bảo mật xảy ra nếu một người truy cập cố tính gửi dữ liệu role = 'admin' trong bảng users chẳng hạn để người đó có quyền là admin. Để xử lý lỗ hổng trong Mass Assignment, Laravel đưa ra thêm hai thuộc tính cho Model là $fillable và $guarded.
- Chú ý rằng chúng ta không khai báo cả hai thuộc tính nãy đồng thời lần nhau. Và một vấn đề là $fillable và guarded chỉ có tác dụng với các phương thức của Eloquent Model, với các phương thức của Query Buider thì nó không có tác dụng.

```php
    protected $fillable = [
        'name',
        'price',
    ];
```
