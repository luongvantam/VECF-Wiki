# In chữ "Hello World" (First Program)

Đây là chương trình đầu tiên giúp bạn làm quen với cấu trúc của một file mã nguồn RAC (`.rsc`). Chương trình này sẽ in dòng chữ **"Hello World"** lên màn hình máy tính Casio fx-580VN X.

---

## 1. Mã nguồn mẫu (`hello.rsc`)

Tạo một file mới có tên `hello.rsc` và nhập nội dung sau:

```rsc
@section.main at 0xD730

lbl main
    setlr_pc
    setsfr
    di,rt
    xr0 = hex 01 01, adr(text)
    line_print
    render.ddd4
    brk

lbl text
    "Hello World"
    hex 00

@section.launcher at 0xD180

hex fd 24 30 30
er14 = calc(adr(main) - 0x2)
sp = er14, pop er14
```

---

## 2. Giải thích cấu trúc chương trình

File mã nguồn RAC cơ bản gồm các thành phần:

1. **Phân vùng (`@section`):** 
   - `@section.main at 0xD730`: Khai báo khối mã chính nạp tại địa chỉ `0xD730`.
   - `@section.launcher at 0xD180`: Khai báo khối mã khởi chạy (Launcher) để chuyển luồng thực thi sang phần chính.
2. **Nhãn (`lbl`):**
   - `lbl main`: Đánh dấu điểm bắt đầu chạy của chương trình.
   - `lbl text`: Đánh dấu vị trí chứa dữ liệu chuỗi ký tự.
3. **Thanh ghi và Gadget:**
   - `xr0 = ...`: Gán giá trị tham số (tọa độ in và con trỏ chuỗi) vào thanh ghi `xr0`.
   - `line_print`, `render.ddd4`: Gọi các lệnh hệ thống để vẽ chuỗi chữ lên màn hình.
4. **Dữ liệu chuỗi:**
   - `"Hello World"`: Chuỗi ký tự cần hiển thị.
   - `hex 00`: Byte số `0x00` báo hiệu kết thúc chuỗi.

> **Ghi chú cho người mới bắt đầu:** Ở bài học này, bạn chỉ cần nắm được diện mạo tổng quan của một file `.rsc`. Các bài học tiếp theo sẽ hướng dẫn chi tiết từng cú pháp từ kiểu dữ liệu, biến, nhãn cho đến cách viết hàm.

---

## 3. Biên dịch và Chạy thử

Chạy lệnh biên dịch trong Terminal:

```bash
python3 rac.py 580vnx "hello.rsc"
```

Sau khi nạp kết quả hex vào trình giả lập, màn hình sẽ hiển thị:

```text
Hello World
```

---

[<- Quay lại: 0/ Cài đặt](0_Installation.md) | [Tiếp theo: 2/ Kiểu Dữ Liệu, Chuỗi & Mảng ->](2_DataTypesAndStrings.md)
