# Print "Hello World"

Đây là chương trình đầu tiên khi làm quen với RAC Compiler. Chương trình sẽ hiển thị dòng chữ **"Hello World"** trên màn hình của máy tính Casio.

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

Lưu đoạn mã trên vào một tệp có phần mở rộng `.rsc`, sau đó biên dịch bằng RAC Compiler và chạy kết quả trên emulator.

Nếu mọi thứ hoạt động đúng, màn hình sẽ hiển thị:

```text
Hello World
```

Chương trình trên có vai trò tương tự như:

```python
print("Hello World")
```

trong Python hoặc:

```c
printf("Hello World");
```

trong C.

---

> Ở phần này, bạn **không cần hiểu** ý nghĩa của từng lệnh hoặc cách ROP chain hoạt động. Mục tiêu chỉ là làm quen với cấu trúc của một chương trình RAC và biết cách biên dịch, chạy chương trình đầu tiên.
>
> Toàn bộ các lệnh được sử dụng trong ví dụ này sẽ được giải thích chi tiết ở **Lập Trình ROP**.

---

[<- Quay lại](0_Installation.md) | [Tiếp theo ->](2p0_KieuDuLieu.md)
