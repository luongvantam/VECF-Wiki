# Biến Ảo, Thanh Ghi & Bí Danh (Variables, Registers & Aliases)

Khi viết mã trong RAC, bạn sẽ sử dụng **Biến Ảo** để lưu tạm giá trị khi biên dịch, hoặc nạp giá trị trực tiếp vào **Thanh Ghi** của máy tính. Ngoài ra, bạn có thể đặt **Bí Danh (`as`)** để code dễ đọc hơn.

---

## 1. Biến Ảo (Virtual Variables)

Biến ảo là các biến do bạn tự đặt tên để lưu trữ các con số hoặc chuỗi ký tự trong lúc viết code.

> **Đặc điểm:** Biến ảo chỉ tồn tại trong quá trình biên dịch. Compiler sẽ tự động thay thế tên biến bằng giá trị tương ứng của nó. Tên biến sẽ không xuất hiện trong file kết quả sau khi biên dịch.

### Cú pháp khai báo:
- `var <tên_biến> = <giá_trị>`
- `<tên_biến> = <giá_trị>`

### Cách sử dụng:
Gọi tên biến ở bất kỳ dòng lệnh nào bạn muốn chèn giá trị của nó.

```rsc
var count = 10         # Tạo biến count có giá trị 10
count = 20             # Gán lại giá trị cho biến count thành 20
var base_addr = 0xD730 # Tạo biến lưu địa chỉ

count                  # Xuất giá trị của biến count ra output (0x14)
```

> **Lời khuyên:** Nên luôn viết từ khóa `var` ở trước khi tạo biến mới để tránh đặt trùng tên với các thanh ghi của máy tính.

---

## 2. Thanh Ghi Phần Cứng (Registers)

Thanh ghi là các ô nhớ tốc độ cao nằm bên trong vi xử lý của máy tính Casio. Khác với biến ảo, khi bạn gán giá trị cho một thanh ghi, compiler sẽ sinh ra lệnh thực sự để đưa giá trị đó vào CPU của máy tính.

### Cú pháp gán thanh ghi:
- `reg <tên_thanh_ghi> = <giá_trị>`
- `<tên_thanh_ghi> = <giá_trị>`

### Các kích thước thanh ghi thông dụng:
- **8-bit (chứa 1 byte):** `r0`, `r1`, `r2`, ..., `r15`
- **16-bit (chứa 2 byte):** `er0`, `er2`, `er4`, ..., `er14`
- **32-bit (chứa 4 byte):** `xr0`, `xr4`, `xr8`, `xr12`
- **64-bit (chứa 8 byte):** `qr0`, `qr8`
- **Thanh ghi điều khiển:** `lr` (thanh ghi liên kết)

### Ví dụ:

```rsc
reg r0 = 0x05          # Gán giá trị 8-bit vào thanh ghi r0
er0 = 0x1234           # Gán giá trị 16-bit vào thanh ghi er0
xr0 = hex 30 30 30 30  # Gán giá trị 32-bit vào thanh ghi xr0
```

### Cách compiler xử lý:
Khi bạn viết lệnh:
```rsc
xr0 = hex 30 30 30 30
```
Compiler sẽ tự động tạo chuỗi lệnh nạp dữ liệu từ ngăn xếp:
```rsc
pop xr0
hex 30 30 30 30
```

---

## 3. Bí Danh (Aliases - Từ khóa `as`)

Từ khóa `as` giúp bạn đặt một cái tên mới dễ nhớ, dễ hiểu hơn cho một thanh ghi, biến hoặc phân vùng.

### 3.1. Đặt bí danh cho thanh ghi / biến:
Cú pháp: `<đối_tượng> as <tên_mới>`

```rsc
# Đặt tên gợi nhớ cho thanh ghi er0
er0 as buffer_ptr

# Sử dụng tên mới như bình thường:
buffer_ptr = 0xD800    # Tương đương: er0 = 0xD800

var max_items = 100
max_items as MAX_LIMIT # Đặt bí danh cho biến
```

### 3.2. Đặt bí danh cho Section:
Cú pháp: `@section.<tên_cũ> [at <gốc> backup <sao_lưu>] as <tên_mới>`

```rsc
@section.init at 0x1000 backup 0x2000 as start_phase

# Có thể gọi theo tên mới:
sizeof(start_phase)
```

---

[<- Quay lại: 2/ Kiểu Dữ Liệu, Chuỗi & Mảng](2_DataTypesAndStrings.md) | [Tiếp theo: 4/ Khai Báo Gadget & Lệnh Gọi ->](4_GadgetsAndCalls.md)
