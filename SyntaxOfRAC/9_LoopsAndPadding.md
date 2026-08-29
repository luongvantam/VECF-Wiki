# Vòng Lặp & Đệm Dữ Liệu (Loops & Memory Padding)

Khi cần lặp lại một đoạn mã nhiều lần hoặc cần đệm thêm các byte trống để cấu trúc bộ nhớ chuẩn xác, bạn sử dụng các lệnh vòng lặp và hàm đệm dữ liệu của RAC Compiler.

---

## 1. Vòng Lặp Biên Dịch (`loop` & `repeat`)

Vòng lặp trong RAC hoạt động ngay trong quá trình biên dịch. Compiler sẽ tự động nhân bản đoạn code bên trong dấu ngoặc `{...}` đúng số lần bạn yêu cầu.

### Cú pháp:
- `loop <số_lần> { <khối_lệnh> }`
- `repeat <số_lần> { <khối_lệnh> }`

> Hai từ khóa `loop` và `repeat` có chức năng hoàn toàn như nhau.

### Ví dụ:

```rsc
# Lặp lại byte 0x90 đúng 4 lần:
loop 4 {
    0x90
}

# Lặp lại một chuỗi lệnh 3 lần:
repeat 3 {
    r1 = 0x01
    call trigger_action
}
```

---

## 2. Các Hàm Đệm & Căn Chỉnh Bộ Nhớ

Trong lập trình máy tính Casio, nhiều cấu trúc dữ liệu yêu cầu độ dài cố định hoặc vị trí bắt đầu phải là bội số của 2 hoặc 4 byte. RAC Compiler cung cấp 4 hàm tiện ích sau:

### 2.1. `fill(<số_lượng>, [<giá_trị>])`
Điền thêm một số lượng byte cố định với giá trị mong muốn (nếu không ghi giá trị, mặc định là `0x00`):

```rsc
fill(16)            # Điền 16 byte có giá trị 0x00
fill(8, 0xFF)       # Điền 8 byte có giá trị 0xFF
```

### 2.2. `align(<kích_thước>, [<giá_trị>])`
Tự động đệm thêm các byte cho đến khi vị trí hiện tại chia hết cho `<kích_thước>`:

```rsc
align(4)            # Đệm thêm byte 0x00 đến khi vị trí chia hết cho 4
align(4, 0x90)      # Đệm bằng byte 0x90 thay vì 0x00
```

### 2.3. `pad(<độ_dài>, [<giá_trị>])`
Tự động đệm thêm byte cho đến khi **kích thước của section hiện tại** đạt đúng `<độ_dài>` byte:

```rsc
# Đảm bảo section hiện tại có đúng 256 (0x100) byte:
pad(0x100, 0x00)
```

### 2.4. `pad_abs(<địa_chỉ>, [<giá_trị>])`
Tự động đệm thêm byte cho đến khi **địa chỉ thực tế trong RAM** đạt tới con số `<địa_chỉ>`:

```rsc
# Đệm dữ liệu đến đúng địa chỉ RAM 0xDDD4:
pad_abs(0xDDD4, 0x00)
```

---

[<- Quay lại: 8/ Macros Động & Hàm](8_MacrosAndFunctions.md) | [Tiếp theo: 10/ Ghi Chú, Ghép Lệnh & Xuống Dòng ->](10_CommentsAndFormatting.md)
