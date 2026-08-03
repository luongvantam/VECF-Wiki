# Installation

## 1. Cài đặt

Trước tiên, hãy tải RAC Compiler về máy bằng lệnh sau:

```bash
git clone https://github.com/luongvantam/RAC-Compiler
```

Hoặc bạn có thể truy cập repository GitHub, tải mã nguồn dưới dạng `.zip` rồi giải nén.

---

## 2. Hướng dẫn sử dụng

Sau khi cài đặt, tạo hoặc chỉnh sửa file `.rsc` trong thư mục `rsc_ropchain/`, sau đó mở Terminal (hoặc Command Prompt) và chạy:

```bash
# Windows
python rac.py <model> "<path_file>"

# macOS / Linux
python3 rac.py <model> "<path_file>"
```

### Tham số

`<model>`

Tên model cần sử dụng. Hiện tại compiler hỗ trợ:

- `580vnx`
- `880btg`

Mỗi model tương ứng với một thư mục (`580vnx/`, `880btg/`) chứa các file cấu hình như:

- labels
- gadgets
- extensions
- ...

Bạn có thể chỉnh sửa các file này để phù hợp với nhu cầu của mình.

---

`<path_file>`

Đường dẫn tới file `.rsc` cần biên dịch.

Ví dụ:

```bash
python rac.py 580vnx "./rsc_ropchain/example.rsc"
```

---

Nếu không muốn nhập lệnh thủ công, bạn có thể sử dụng launcher có sẵn:

```bash
# Windows
.\run.bat

# macOS / Linux
sh run.sh
```

---

## 3. Output

Sau khi biên dịch thành công, compiler sẽ xuất ra kết quả theo định dạng:

```text
<log>...

=== <start_dest> -> <end_dest> ===
<hex>
===
```

Hoặc:

```text
=== <start_dest> -> <end_dest> (<start_src> -> <end_src>) ===
<hex>
===
```

Trong đó:

- `<start_dest>` và `<end_dest>` là phạm vi địa chỉ của ROP chain sau khi biên dịch.
- `<start_src>` và `<end_src>` (nếu có) là phạm vi địa chỉ của mã nguồn tương ứng.
- `<hex>` là chuỗi ROP chain đã được biên dịch.

Chỉ cần sao chép phần `<hex>` và dán vào emulator hoặc thiết bị mục tiêu để sử dụng.

---

[Tiếp theo ->](1_PrintHelloWorld.md)
