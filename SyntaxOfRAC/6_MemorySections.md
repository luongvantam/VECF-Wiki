# Phân Vùng Bộ Nhớ & Định Vị (`org`, `backup`, `@section` & `@set`)

Một chương trình thường được chia thành nhiều khối độc lập, ví dụ: khối khởi chạy (Launcher), khối mã chính (Main) hoặc khối dữ liệu. RAC Compiler cung cấp hệ thống **Section (Phân vùng)** và các chỉ thị định vị địa chỉ giúp quản lý chương trình một cách rõ ràng.

---

## 1. Chỉ Thị Định Vị (`org` & `backup`)

Hai lệnh này dùng để thiết lập địa chỉ nạp trong bộ nhớ RAM:

### 1.1. `org` (Origin)
- **Cú pháp:** `org <địa_chỉ>`
- Xác định **địa chỉ thực thi** của chương trình. Mọi nhãn nằm trong section sẽ được tính toán vị trí tuyệt đối dựa theo địa chỉ này.

### 1.2. `backup`
- **Cú pháp:** `backup <địa_chỉ>`
- Xác định **địa chỉ lưu trữ ban đầu** của dữ liệu. Thông tin này giúp compiler tự động tính khoảng cách di chuyển dữ liệu (`dist`) giữa nơi lưu trữ và nơi thực thi.

### Ví dụ:
```rsc
org 0xD730        # Nơi chương trình sẽ thực thi
backup 0xE9E0     # Nơi chương trình được lưu trữ ban đầu
```

---

## 2. Khai Báo Phân Vùng (`@section` & `@set`)

Để tạo một phân vùng mới, bạn dùng từ khóa `@section` (hoặc `@set`):

### Cú pháp:
```rsc
@section.<tên_section> [at <địa_chỉ_gốc> [backup <địa_chỉ_sao_lưu>]]
```

- Tham số `at`: Thiết lập địa chỉ thực thi (`org`).
- Tham số `backup`: Thiết lập địa chỉ lưu trữ (`backup`).

### Các cách viết phổ biến:

```rsc
# Khai báo section thông thường
@section.main

# Khai báo kèm địa chỉ thực thi (tương đương org 0xE9E0)
@section.main at 0xE9E0

# Khai báo đầy đủ cả địa chỉ thực thi và sao lưu
@section.main at 0xD730 backup 0xE9E0

# Dùng từ khóa @set (tác dụng tương đương @section)
@set.launcher at 0xD180
```

> **Lưu ý:** Khi bạn đã khai báo `at` hoặc `backup` trực tiếp trên dòng `@section`, bạn **không cần** phải viết thêm lệnh `org` hay `backup` bên trong section đó nữa.

---

## 3. Cấu Trúc File Mẫu Phổ Biến

Một file mã nguồn thông thường gồm hai section: `launcher` (khởi chạy) và `main` (logic chính):

```rsc
# 1. Phân vùng Launcher: Nạp tại 0xD180
@section.launcher at 0xD180

hex fd 24 30 30
er14 = calc(adr(main_code) - 0x2)
sp = er14, pop er14

# 2. Phân vùng Main: Thực thi tại 0xD730, sao lưu tại 0xE9E0
@section.main at 0xD730 backup 0xE9E0

lbl main_code
    call 0x1234
    call 0x5678
    brk
```

---

[<- Quay lại: 5/ Nhãn & Lệnh Nhảy](5_LabelsAndJumps.md) | [Tiếp theo: 7/ Phép Toán & Biểu Thức Thời Gian Biên Dịch ->](7_CompileTimeEvaluation.md)
