# Nhãn & Lệnh Nhảy (Labels & Jumps)

Label dùng để đánh dấu một vị trí trong chương trình để các lệnh khác có thể tham chiếu tới, chẳng hạn như lấy địa chỉ hoặc nhảy tới vị trí đó.

## 1. Khai báo Label

Bạn có thể khai báo một label tại vị trí hiện tại của con trỏ biên dịch hoặc gắn trực tiếp với một địa chỉ tuyệt đối.

- `lbl <tên_label>`
- `lbl <tên_label> at <địa_chỉ>`

Ví dụ:

```rsc
lbl buffer_screen at 0xDDD4
lbl start
```

## 2. Lấy địa chỉ của Label

RAC Compiler cung cấp hàm `adr()` để lấy địa chỉ của một label.

- `adr(<label>)` — Trả về địa chỉ tuyệt đối của label.
- `adr(<label>, <offset>)` — Trả về địa chỉ của label cộng thêm `offset`.
- `adr($)` — Trả về địa chỉ của vị trí hiện tại.

Ví dụ:

```rsc
var addr1 = adr(start)
var addr2 = adr(start, -2)
```

## 3. Lệnh nhảy (goto)

Sử dụng `goto` để chuyển luồng thực thi đến một label.

- Cú pháp:

```rsc
goto <label>
```

Ví dụ:

```rsc
goto start
```

> `goto` là cú pháp của RAC Compiler, không phải lệnh của CPU. Trong quá trình biên dịch, compiler sẽ tự động chuyển lệnh này thành chuỗi ROP tương ứng.
>
> Ví dụ:
>
> ```rsc
> goto start
> ```
>
> Có thể được biên dịch thành:
>
> ```rsc
> er14 = adr(start, -2)
> sp = er14, pop er14
> ```

---

[<- Quay lại](2p3_KhaiBaoGadget.md) | [Tiếp theo ->](4_TinhToan.md)
