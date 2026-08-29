# Cài đặt & Sử dụng (Installation & CLI)

RAC Compiler là trình biên dịch mã nguồn `.rsc` thành chuỗi bytecode / ROP chain cho máy tính Casio ClassWiz.

---

## 1. Cài đặt

Mở Terminal (hoặc Command Prompt) và clone repository về máy:

```bash
git clone https://github.com/luongvantam/RAC-Compiler
```

Hoặc tải file `.zip` từ GitHub và giải nén vào một thư mục trên máy tính của bạn.

> **Yêu cầu:** Máy tính cần cài sẵn Python 3 (phiên bản 3.8 trở lên).

---

## 2. Biên dịch file mã nguồn

Để biên dịch một file `.rsc`, sử dụng cú pháp dòng lệnh:

```bash
# Windows
python rac.py <model> "<đường_dẫn_file>"

# macOS / Linux
python3 rac.py <model> "<đường_dẫn_file>"
```

### Ý nghĩa tham số:

- `<model>`: Dòng máy tính mục tiêu cần biên dịch. Hiện tại hỗ trợ:
  - `580vnx`: Dành cho dòng máy Casio fx-580VN X.
  - `880btg`: Dành cho dòng máy Casio fx-880BTG.
- `<đường_dẫn_file>`: Đường dẫn tới file `.rsc` cần biên dịch.

### Ví dụ:

```bash
python3 rac.py 580vnx "./rsc_ropchain/example.rsc"
```

---

## 3. Sử dụng file chạy nhanh (Scripts)

Bạn cũng có thể chạy file script có sẵn trong thư mục dự án:

- **Windows:** Chạy `.\run.bat`
- **macOS / Linux:** Chạy `sh run.sh`

---

## 4. Kết quả đầu ra (Output)

Sau khi biên dịch thành công, compiler sẽ hiển thị kết quả trên màn hình:

```text
=== <start_dest> -> <end_dest> ===
<chuỗi_hex>
===
```

Nếu section có thiết lập vùng sao lưu (`backup`):

```text
=== <start_dest> -> <end_dest> (<start_src> -> <end_src>) ===
<chuỗi_hex>
===
```

- `<start_dest>` -> `<end_dest>`: Địa chỉ thực thi trong bộ nhớ RAM của máy tính.
- `<chuỗi_hex>`: Dữ liệu bytecode đã được biên dịch hoàn chỉnh. Bạn chỉ cần sao chép chuỗi hex này để nạp vào emulator (trình giả lập) hoặc thiết bị thật.

---

[Tiếp theo: 1/ In chữ Hello World ->](1_PrintHelloWorld.md)
