# Khai Báo Biến Ảo (Virtual Variables)

RAC Compiler cho phép bạn khai báo các **Virtual Variable** để lưu trữ và tái sử dụng giá trị trong quá trình viết mã. Biến có thể chứa số, chuỗi hoặc kết quả của các biểu thức tính toán.

> **Lưu ý:** Virtual Variable chỉ tồn tại trong quá trình biên dịch. Compiler sẽ thay thế mọi tham chiếu đến biến bằng giá trị tương ứng, vì vậy **tên biến sẽ không xuất hiện trong hex output**.

## 1. Cú pháp khai báo

Bạn có thể khai báo biến bằng từ khóa `var` hoặc gán trực tiếp:

- `var <tên_biến> = <giá_trị>`
- `<tên_biến> = <giá_trị>`

Ví dụ:

```rsc
var count = 10         # Khai báo biến count có giá trị 10
count = 20             # Gán lại giá trị của count thành 20
var name = "World"     # Khai báo biến chuỗi
```

> **Lưu ý:** Từ khóa `var` là tùy chọn nếu tên biến không trùng với từ khóa hoặc tên thanh ghi (ví dụ: `r`, `er`, `xr`, `qr`, `lr`, ...). Nếu có khả năng gây nhầm lẫn, hãy luôn sử dụng `var`.

## 2. Cách sử dụng biến

Để sử dụng biến, chỉ cần gọi trực tiếp tên biến.

```rsc
count                  # Output: 20

var msg = "Hello"
msg                    # Output: "Hello"
```

---

[<- Quay lại](2p0_KieuDuLieu.md) | [Tiếp theo ->](2p2_KhaiBaoThanhGhi.md)
