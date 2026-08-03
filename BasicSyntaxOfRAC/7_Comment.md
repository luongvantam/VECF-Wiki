# Ghi chú và Ghép lệnh (Comments & Compound Statements)

## 1. Ghi chú (Comments)

Comments giúp bạn giải thích hoặc ghi chú mã nguồn. Chúng chỉ phục vụ mục đích đọc hiểu và sẽ được compiler bỏ qua hoàn toàn.

### Ghi chú một dòng

Sử dụng ký tự `#`. Mọi nội dung phía sau `#` trên cùng một dòng sẽ được xem là comment.

```rsc
var a = 10     # Đây là biến khởi tạo
```

### Ghi chú nhiều dòng

Sử dụng cặp ký hiệu `/*` và `*/`.

```rsc
/*
Đây là ví dụ về comment nhiều dòng.
Mọi nội dung bên trong sẽ được compiler bỏ qua.
*/
```

## 2. Ghép lệnh (Compound Statements)

Bạn có thể viết nhiều câu lệnh trên cùng một dòng bằng cách sử dụng dấu chấm phẩy (`;`).

```rsc
call 0x1234 ; goto end
```

> Việc ghép nhiều câu lệnh trên một dòng chỉ giúp mã nguồn ngắn gọn hơn, không làm thay đổi thứ tự thực thi của chương trình.

---

[<- Quay lại](6_Section.md)
