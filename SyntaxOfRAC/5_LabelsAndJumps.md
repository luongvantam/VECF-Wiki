# Nhãn & Lệnh Nhảy (Labels & Jumps)

**Nhãn (Label)** giống như một "dấu trang" dùng để đánh dấu một vị trí bất kỳ trong chương trình. Nhờ có nhãn, bạn có thể dễ dàng lấy địa chỉ của vị trí đó hoặc yêu cầu chương trình nhảy đến đó chạy tiếp.

---

## 1. Khai Báo Nhãn (Labels)

Bạn có thể tạo nhãn theo một trong các cách sau:

### Cách 1: Dùng từ khóa `lbl`
```rsc
lbl start
lbl my_loop
```

### Cách 2: Dùng dấu hai chấm `:` ở sau tên
```rsc
start:
my_loop:
```

### Cách 3: Gán nhãn tại một địa chỉ cố định
```rsc
lbl screen_ram at 0xDDD4
lbl memory_base at 0x8000
```

---

## 2. Lấy Địa Chỉ của Nhãn với `adr()`

Hàm `adr()` giúp bạn lấy địa chỉ số thực tế của nhãn mà không cần phải tự tính toán bằng tay:

- `adr(<tên_nhãn>)`: Lấy địa chỉ của nhãn.
- `adr(<tên_nhãn>, <độ_lệch>)`: Lấy địa chỉ của nhãn cộng hoặc trừ thêm số byte (offset).
- `adr($)`: Lấy địa chỉ của dòng lệnh hiện tại.
- `adr(<tên_nhãn>)[0]`: Lấy riêng byte thấp (Low byte).
- `adr(<tên_nhãn>)[1]`: Lấy riêng byte cao (High byte).

### Ví dụ:

```rsc
lbl start
var my_addr = adr(start)        # Lấy địa chỉ đầy đủ của nhãn start
var prev_addr = adr(start, -2)  # Lấy địa chỉ lùi lại 2 byte

# Tách byte thấp và byte cao để nạp vào 2 thanh ghi 8-bit riêng biệt:
r0 = adr(start)[0]              # Lấy byte thấp nạp vào r0
r1 = adr(start)[1]              # Lấy byte cao nạp vào r1
```

---

## 3. Cú pháp `adr_of`

Bạn cũng có thể dùng cú pháp `adr_of` để lấy địa chỉ:

- `adr_of <tên_nhãn>`: Lấy địa chỉ của nhãn.
- `adr_of [<độ_lệch>] <tên_nhãn>`: Lấy địa chỉ kèm độ lệch byte.

### Ví dụ:

```rsc
lbl finish

var addr1 = adr_of finish
var addr2 = adr_of [-2] finish
```

---

## 4. Lệnh Nhảy (`goto`)

Khi muốn chuyển luồng thực thi của chương trình tới một nhãn nào đó, bạn sử dụng lệnh `goto`.

### Cú pháp:
```rsc
goto <tên_nhãn>
```

### Ví dụ:
```rsc
lbl loop_start
    # ... các lệnh thực thi ...
    goto loop_start     # Nhảy ngược lại điểm loop_start để chạy tiếp
```

> **Cách compiler xử lý:** Compiler sẽ tự động tính toán và tạo chuỗi lệnh điều chỉnh con trỏ ngăn xếp `sp` để chuyển chương trình đến đúng nhãn mục tiêu mà bạn không cần phải tính toán offset thủ công.

---

[<- Quay lại: 4/ Khai Báo Gadget & Lệnh Gọi](4_GadgetsAndCalls.md) | [Tiếp theo: 6/ Phân Vùng Bộ Nhớ & Định Vị ->](6_MemorySections.md)
