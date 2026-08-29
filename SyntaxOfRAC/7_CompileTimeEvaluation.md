# Phép Toán & Biểu Thức Thời Gian Biên Dịch (Compile-time Expressions & Evaluation)

Thay vì phải tự tính toán thủ công kích thước dữ liệu, độ lệch địa chỉ hay thực hiện các phép cộng trừ phức tạp, bạn có thể để RAC Compiler tự động tính toán giúp bạn tại thời điểm biên dịch.

---

## 1. Đo Lường Kích Thước & Phân Vùng (Section Metrics)

Các hàm sau giúp bạn tự động lấy thông tin về kích thước và địa chỉ của các section:

- `sizeof()` hoặc `pr_length`: Lấy kích thước (tính bằng byte) của section hiện tại.
- `sizeof(<tên_section>)`: Lấy kích thước (byte) của một section cụ thể.
- `dist.<tên_section>`: Lấy khoảng cách giữa địa chỉ `backup` và `org` của section: `abs(backup - org)`.
- `pr_org()` / `pr_org(<tên_section>)`: Lấy địa chỉ thực thi gốc của section.
- `pr_backup()` / `pr_backup(<tên_section>)`: Lấy địa chỉ sao lưu của section.

### Ví dụ:

```rsc
@section.main at 0xD730 backup 0xE9E0

sizeof()          # Compiler tự điền kích thước byte của section main
dist.main         # Compiler tự tính khoảng cách giữa 0xD730 và 0xE9E0 (0x12B0)

var my_org = pr_org(main)       # Gán giá trị 0xD730 cho biến my_org
var my_bk  = pr_backup(main)    # Gán giá trị 0xE9E0 cho biến my_bk
```

---

## 2. Tính Toán Biểu Thức với `eval()` và `calc()`

Hàm `eval()` và `calc()` giúp bạn thực hiện các phép toán số học và thao tác bit. Compiler sẽ tính ra kết quả cuối cùng và thay thế trực tiếp vào mã nguồn.

### Cú pháp:
- `eval(<biểu_thức>)`
- `calc(<biểu_thức>)`

> `eval()` và `calc()` có tác dụng hoàn toàn giống nhau, bạn có thể dùng tùy ý.

### Các phép toán được hỗ trợ:
- **Phép toán số học:** `+`, `-`, `*`, `/`, `%` (chia lấy dư)
- **Phép toán bit:** `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (dịch trái), `>>` (dịch phải)
- **Kết hợp linh hoạt:** Bạn có thể lồng biến, `adr()`, `sizeof()`, `dist`, `pr_org()` vào trong biểu thức.

### Ví dụ:

```rsc
# Phép tính số học đơn giản:
eval(10 + 20 * 2)                 # Output: 50 (0x32)

# Tính kích thước bộ đệm gấp đôi:
var buffer_len = eval(sizeof(main) * 2)

# Tính khoảng cách giữa hai nhãn:
var delta = calc(
    adr(end_point) - adr(start_point)
)

# Tính địa chỉ thực tế khi payload nằm trong vùng sao lưu:
var target_addr = eval(adr(my_label) + dist.main)
```

---

## 3. Lệnh Tính Khoảng Cách Nhãn `adr_arith`

Lệnh `adr_arith` giúp bạn viết nhanh phép tính khoảng cách giữa hai hoặc nhiều nhãn:

- `adr_arith <nhãn_1> <+/-> adr_arith <nhãn_2>`
- `adr_arith [<độ_lệch_1>] <nhãn_1> <+/-> adr_arith [<độ_lệch_2>] <nhãn_2>`

### Ví dụ:

```rsc
adr_arith start - adr_arith end
adr_arith [+4] start - adr_arith [-2] end
```

---

## 4. Cơ Chế Tự Động Xử Lý Tham Chiếu Xuôi (Forward Reference)

Compiler hoạt động theo 2 lượt quét (2-pass):
1. **Lượt 1:** Thu thập thông tin cấu trúc, kích thước và vị trí các nhãn trong toàn bộ file.
2. **Lượt 2:** Tính toán chính xác toàn bộ các biểu thức `eval()`, `sizeof()`, `dist`, `adr()`.

> **Ưu điểm lớn:** Bạn hoàn toàn có thể gọi `sizeof(main)` hoặc `adr(target)` ở ngay đầu file trước cả khi section `main` hoặc nhãn `target` được viết ở phía dưới mà không hề bị báo lỗi.

---

[<- Quay lại: 6/ Phân Vùng Bộ Nhớ & Định Vị](6_MemorySections.md) | [Tiếp theo: 8/ Macros Động & Hàm ->](8_MacrosAndFunctions.md)
