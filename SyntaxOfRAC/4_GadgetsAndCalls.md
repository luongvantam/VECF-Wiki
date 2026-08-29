# Khai Báo Gadget & Lệnh Gọi (Gadgets & Calls)

Trong máy tính Casio, bộ nhớ ROM có sẵn rất nhiều đoạn mã xử lý chức năng (như in chữ, vẽ màn hình, sao chép bộ nhớ,...). Những đoạn mã này được gọi là **Gadget**. RAC Compiler cho phép bạn đặt tên và gọi thực thi các gadget này rất dễ dàng.

---

## 1. Đặt tên cho Gadget (`def`)

Để không phải ghi nhớ những địa chỉ hex phức tạp, bạn dùng lệnh `def` để đặt một cái tên dễ hiểu cho địa chỉ đó.

### Cú pháp:
```rsc
def <tên_gadget> : <địa_chỉ>
```

### Ví dụ:
```rsc
def set_screen_buf : 0x17B34
def render_display : 0xDDD4
```

Sau khi định nghĩa, bạn có thể dùng `set_screen_buf` thay cho địa chỉ `0x17B34` ở bất kỳ đâu trong file mã nguồn.

---

## 2. Gắn Thẻ Chức Năng Cho Gadget (Tagged Gadgets)

Bạn có thể gắn thêm thẻ `{<tag>}` ở trước tên gadget để compiler nhận biết chức năng đặc biệt của gadget đó:

### Cú pháp:
```rsc
def {<tag>} <tên_gadget>: <địa_chỉ>
```

### Các thẻ thông dụng:
- `{memcpy}`: Đánh dấu gadget dùng để sao chép một vùng nhớ.
- `{memset}`: Đánh dấu gadget dùng để điền dữ liệu (xóa/đặt giá trị) vùng nhớ.
- `{strcpy}`: Đánh dấu gadget dùng để sao chép chuỗi ký tự.

### Ví dụ:
```rsc
def {memcpy} copy_memory_fast : 0x12345
def {memset} clear_screen_ram  : 0x18F20
```

---

## 3. Lệnh Gọi Gadget (`call`)

Để yêu cầu chương trình chạy một gadget tại một thời điểm, bạn sử dụng lệnh `call`.

### 3.1. Cú pháp cơ bản:
```rsc
call <tên_gadget | địa_chỉ>
```

### Ví dụ:
```rsc
call set_screen_buf     # Gọi theo tên đã đặt
call 0x1234             # Hoặc gọi trực tiếp bằng địa chỉ
```

### 3.2. Lệnh gọi rút gọn:
Nếu một lệnh hoặc gadget đã có sẵn trong danh sách của model máy (ví dụ `580vnx`), bạn chỉ cần viết tên lệnh đứng riêng một dòng mà không cần chữ `call`:

```rsc
# Hai cách viết sau là như nhau:
call line_print
line_print
```

---

[<- Quay lại: 3/ Biến Ảo, Thanh Ghi & Bí Danh](3_VariablesAndRegisters.md) | [Tiếp theo: 5/ Nhãn & Lệnh Nhảy ->](5_LabelsAndJumps.md)
