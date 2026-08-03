# Kiểu dữ liệu cơ bản (Data Types)

RAC Compiler hỗ trợ nhiều định dạng dữ liệu và chuỗi linh hoạt để phục vụ cho việc tạo ROP chain và shellcode.

## 1. Dữ liệu số (Integers)

Khai báo số Hex (thập lục phân):

- Ghi trực tiếp: `0x02`, `0xeeff`
- Dùng từ khóa `hex`: `hex 30`, `hex ff ee`

> `hex ff ee` tương đương với `0xeeff`.

Ngoài số Hex, RAC Compiler còn hỗ trợ **giá trị biên dịch (Compile-time Value)**.

Các giá trị này chỉ được phép xuất hiện trong biểu thức tính toán như `eval()`, `calc()` hoặc các hàm hỗ trợ tính toán khác. Chúng **không được sinh trực tiếp vào ROP chain hoặc shellcode**.

Ví dụ:

```rsc
calc(10 + 20)
eval(sizeof(main) * 2)
```

---

## 2. Chuỗi ký tự (Strings)

RAC Compiler hỗ trợ hai loại chuỗi:

### Chuỗi chuẩn

```rsc
"<chuỗi>"
```

Ví dụ:

```rsc
"Xin chào"
```

### Chuỗi Token

Sử dụng các token có sẵn trên máy tính Casio ClassWiz.

```rsc
'<chuỗi_token>'
```

Ví dụ:

```rsc
'sin(90)'
```

---

## 3. Giá trị biên dịch (Compile-time Expressions)

Các biểu thức dưới đây được compiler tính toán trong quá trình biên dịch và **thay thế bằng giá trị tương ứng trong output**.

- `sizeof()` — Trả về kích thước (byte) của section hiện tại.
- `sizeof(<section>)` — Trả về kích thước (byte) của section được chỉ định.
- `dist.<section>` — Trả về khoảng cách (byte) giữa `org` và `backup` của section.

### Ví dụ

```rsc
sizeof()      # Nếu section hiện tại có kích thước 10 byte thì output sẽ là 0x0A.

dist.main     # Giả sử section "main" có org = 0xD730 và backup = 0xE9E0, thì output sẽ là 0x12B0 (4784).
```

---

[<- Quay lại](1_PrintHelloWorld.md) | [Tiếp theo ->](2p1_KhaiBaoBienAo.md)
