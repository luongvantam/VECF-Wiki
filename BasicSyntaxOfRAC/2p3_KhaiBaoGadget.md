# Khai báo Gadget (Gadgets)

Gadget là các đoạn mã ngắn có sẵn trong ROM, thường kết thúc bằng lệnh `RT` hoặc `POP PC`. Trong RAC Compiler, bạn có thể đặt tên cho một gadget để dễ tái sử dụng trong chương trình.

## 1. Định nghĩa Gadget (Def)

Sử dụng `def` để ánh xạ một địa chỉ ROM với một tên định danh.

- Cú pháp:

```rsc
def <tên_gadget> : <địa_chỉ>
```

Ví dụ:

```rsc
def my_gadget : 0x17B34
```

Sau khi được định nghĩa, `my_gadget` có thể được sử dụng ở bất kỳ đâu thay cho địa chỉ `0x17B34`.

## 2. Gọi Gadget (Call)

Sử dụng `call` để thêm một gadget vào ROP chain. Bạn có thể gọi bằng tên hoặc bằng địa chỉ.

- Cú pháp:

```rsc
call <tên_gadget | địa_chỉ>
```

Ví dụ:

```rsc
call my_gadget       # Gọi gadget đã định nghĩa
call 0x12345         # Gọi trực tiếp địa chỉ
```

> `call` không sinh lệnh `CALL` như trong Assembly truyền thống. Thay vào đó, compiler sẽ chèn địa chỉ gadget vào ROP chain để CPU thực thi gadget đó khi đến lượt.

---

[<- Quay lại](2p2_KhaiBaoThanhGhi.md) | [Tiếp theo ->](3_Label.md)
