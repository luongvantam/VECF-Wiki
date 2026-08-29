# Ghi Chú, Ghép Lệnh & Xuống Dòng (Comments, Compound Statements & Line Continuation)

Để mã nguồn luôn rõ ràng, dễ đọc và dễ bảo trì, bạn có thể sử dụng các quy tắc ghi chú, viết gộp nhiều lệnh trên một dòng và quy tắc xuống dòng sau đây.

---

## 1. Ghi Chú (Comments)

Ghi chú giúp bạn giải thích ý nghĩa của các dòng mã nguồn. Compiler sẽ bỏ qua toàn bộ phần ghi chú khi biên dịch.

### 1.1. Ghi chú một dòng (`#`)
Bất kỳ nội dung nào nằm phía sau dấu thăng `#` trên cùng một dòng sẽ được xem là ghi chú:

```rsc
var a = 10         # Đây là biến khởi tạo
er0 = 0xD730       # Nạp địa chỉ bộ đệm
```

### 1.2. Ghi chú nhiều dòng (`/* ... */`)
Sử dụng cặp ký hiệu khối `/*` và `*/` để viết ghi chú dài trên nhiều dòng:

```rsc
/*
    Đoạn mã này thực hiện:
    - Bật màn hình LCD máy tính
    - Khởi tạo bảng vector ngắt
*/
call init_system
```

---

## 2. Ghép Nhiều Câu Lệnh Trên Một Dòng (Dấu `;`)

Bạn có thể viết nhiều câu lệnh trên cùng một dòng bằng cách ngăn cách chúng bằng dấu chấm phẩy (`;`). Các lệnh sẽ được compiler xử lý tuần tự từ trái sang phải:

```rsc
call 0x1234 ; goto end
r1 = 0x01 ; r2 = 0x02 ; call add_numbers
```

---

## 3. Ghép Nối Dòng & Xuống Dòng (Line Continuation)

### 3.1. Dùng dấu gạch chéo ngược `\` ở cuối dòng:
Khi một câu lệnh hoặc danh sách byte hex quá dài, bạn có thể ngắt dòng bằng cách đặt dấu `\` ở cuối dòng:

```rsc
hex 30 31 32 33 \
    34 35 36 37
```

### 3.2. Tự động xuống dòng trong các khối ngoặc:
Các biểu thức tính toán `(...)`, mảng `[...]`, hoặc khối lệnh `{...}` tự động hỗ trợ xuống dòng mà bạn **không cần** phải thêm ký tự `\`:

```rsc
# Biểu thức tính toán nhiều dòng:
eval(
    0x01 +
    0x02 * 0x03
)

# Mảng dữ liệu nhiều dòng:
[
    0x10
    0x20
]

# Khối hàm nhiều dòng:
func setup() {
    r1 = 0x05
    r2 = 0x10
}
```

---

[<- Quay lại: 9/ Vòng Lặp & Đệm Dữ Liệu](9_LoopsAndPadding.md) | [Tiếp theo: 11/ Cấu Hình Build & Nhúng Python ->](11_BuildConfigAndPython.md)
