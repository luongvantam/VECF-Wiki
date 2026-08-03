# Viết chương trình ROP đơn giản

Đến đây chúng ta đã hiểu những khái niệm cơ bản như **Stack**, **thanh ghi**, **gadget** và **ROP Chain**. Trong chương này, chúng ta sẽ bắt đầu viết chương trình ROP đầu tiên.

Thay vì tìm hiểu về **Mode an** hay **Character Converter (CC)** - Bộ Chuyển Đổi Ký Tự (bcđkt), tài liệu sẽ đi thẳng vào cách xây dựng một chương trình hoàn chỉnh bằng **RAC Compiler**. Nếu muốn tìm hiểu sâu hơn về các kỹ thuật này, bạn có thể tham khảo các tài liệu sau:

* [https://zhuanlan.zhihu.com/p/618704031](https://zhuanlan.zhihu.com/p/618704031)
* [https://gamingwithevets.github.io/thu-vien-ma-tran/](https://gamingwithevets.github.io/thu-vien-ma-tran/)

Từ chương này trở đi, toàn bộ ví dụ sẽ được viết bằng **RSC** rồi biên dịch bằng **RAC Compiler**.

Để dễ theo dõi, bạn nên đọc trước tài liệu **Cú pháp RAC cơ bản** và **Instruction Manual của NX-U8/100**.

> Mặc dù toàn bộ ví dụ sử dụng **fx-580VN X**, các nguyên lý trong chương này cũng có thể áp dụng cho nhiều dòng **Casio ClassWiz** khác.

---

# Chương trình Hello World

Đây là chương trình ROP đơn giản dùng để hiển thị dòng chữ **Hello World**.

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

Thoạt nhìn chương trình có vẻ khá dài, nhưng thực tế mỗi phần chỉ đảm nhiệm một nhiệm vụ riêng. Chúng ta sẽ lần lượt tìm hiểu từng phần.

---

# Section Main

Dòng đầu tiên:

```rsc
@section.main at 0xD730
```

khai báo **section main** được đặt tại địa chỉ `0xD730`.

Đây là nơi chứa toàn bộ chương trình chính sẽ được thực thi sau khi launcher chuyển quyền điều khiển đến.

---

## Khởi tạo môi trường

Ba dòng đầu tiên của chương trình là:

```rsc
setlr_pc
setsfr
di,rt
```

Đây đều là những đoạn mã khởi tạo trước khi chương trình bắt đầu hoạt động.

---

### setlr_pc

Macro này thực chất tương đương:

```asm
BL f_1fffe
POP PC

f_1fffe:
    POP PC
```

Có thể bạn sẽ thắc mắc:

> Tại sao lại phải chạy đoạn mã này ngay từ đầu?

Hãy nhớ lại lý thuyết ở chương trước.

Thông thường, một gadget sẽ kết thúc bằng:

```asm
POP PC
```

Tuy nhiên, trên firmware của ClassWiz cũng tồn tại rất nhiều đoạn mã kết thúc bằng:

```asm
RT
```

Lệnh `RT` sẽ không lấy địa chỉ từ Stack mà nhảy đến địa chỉ đang lưu trong thanh ghi **LR**.

Vì vậy, `setlr_pc` được dùng để thiết lập **LR** trỏ tới một gadget chỉ chứa:

```asm
POP PC
```

Kể từ thời điểm đó, mỗi khi gặp:

```asm
RT
```

CPU sẽ thực hiện:

```
RT
 ↓
LR
 ↓
POP PC
```

Nhờ vậy, các gadget kết thúc bằng `RT` cũng có thể hoạt động giống như những gadget kết thúc bằng `POP PC`, giúp chuỗi ROP tiếp tục thực thi bình thường.

---

### setsfr

Tiếp theo là:

```rsc
setsfr
```

Macro này khá dài:

```asm
mov er0, #04h
st r0, f030h
...
rt
```

Bạn chưa cần quan tâm từng lệnh bên trong.

Điều quan trọng cần nhớ là:

> **`setsfr` khôi phục các thanh ghi SFR (Special Function Register) về trạng thái phù hợp để các gadget phía sau hoạt động chính xác.**

Nếu bỏ qua bước này, một số gadget như `line_print` hoặc `render.ddd4` có thể hoạt động không đúng.

---

### di,rt

Cuối cùng:

```asm
DI
RT
```

`DI` dùng để tắt ngắt (Interrupt Disable), tránh việc chương trình bị ngắt giữa quá trình thực thi ROP.

Sau đó `RT` sẽ chuyển sang gadget tiếp theo thông qua **LR** mà `setlr_pc` đã chuẩn bị trước.

---

# Truyền tham số

Tiếp theo là dòng:

```rsc
xr0 = hex 01 01, adr(text)
```

Đây chỉ là cú pháp rút gọn của:

```rsc
pop xr0
hex 01 01
adr(text)
```

Trong firmware, gadget `POP XR0` có dạng:

```asm
POP XR0
POP PC
```

Theo nguyên lý của Stack:

```
Stack

<TOP>

hex 01 01
adr(text)
<Gadget tiếp theo>

<BOTTOM>
```

Khi gadget thực thi:

```
POP XR0
```

thì giá trị:

```
hex 01 01
adr(text)
```

được nạp vào thanh ghi `XR0`.

Sau đó:

```
POP PC
```

lấy địa chỉ gadget kế tiếp và CPU tiếp tục thực thi.

Như vậy, chỉ với một gadget, chúng ta vừa truyền được dữ liệu vào thanh ghi, vừa chuyển sang bước tiếp theo của chương trình.

---

# In chuỗi lên màn hình

Sau khi chuẩn bị xong tham số, chương trình gọi:

```rsc
line_print
```

Macro này sẽ đọc:

* tọa độ hiển thị (ER0)
* địa chỉ chuỗi `"Hello World"` (ER2)

rồi ghi dữ liệu vào bộ đệm màn hình.

Sau đó:

```rsc
render.ddd4
```

sao chép nội dung bộ đệm lên màn hình LCD, vì vậy dòng chữ **Hello World** sẽ xuất hiện.

---

# Kết thúc chương trình

Cuối cùng là:

```rsc
brk
```

Lệnh này dùng để kết thúc chương trình.

Nếu không dừng tại đây, CPU sẽ tiếp tục đọc những dữ liệu nằm phía sau chuỗi `"Hello World"` như thể chúng là địa chỉ gadget hoặc mã lệnh, rất dễ khiến chương trình chạy sai hoặc bị treo.

---

# Section Launcher

Bên dưới chương trình chính là:

```rsc
@section.launcher at 0xD180
```

Đây là phần đầu tiên được kích hoạt khi khai thác lỗ hổng.

```rsc
hex fd 24 30 30
```

là chuỗi ký tự đặc biệt dùng để kích hoạt lỗ hổng và làm thay đổi luồng thực thi của chương trình.

Ngay sau đó là chuỗi ROP:

```rsc
er14 = calc(adr(main) - 0x2)
sp = er14, pop er14
```

Nhiệm vụ của launcher rất đơn giản:

1. Điều khiển Stack Pointer (`SP`).
2. Chuyển Stack sang vùng chứa chương trình chính.
3. Bắt đầu thực thi tại `main`.

Có thể xem launcher như **cầu nối** giữa lỗ hổng và chương trình ROP mà chúng ta đã viết.

---

# Tại sao lại phải trừ `0x2`?

Có lẽ bạn sẽ chú ý tới dòng:

```rsc
er14 = calc(adr(main) - 0x2)
```

Tại sao không nhảy thẳng đến `main` mà lại phải trừ đi `2` byte?

Để hiểu điều này, hãy viết lại launcher dưới dạng Stack:

```text
Stack
<TOP>

POP ER14          ; Gadget 1
POP PC

adr(main) - 0x2

MOV SP, ER14      ; Gadget 2
POP ER14
POP PC

<BOTTOM>
```

Sau gadget đầu tiên:

```
ER14 = adr(main) - 0x2
PC   = Gadget 2
```

Đến gadget thứ hai:

```
MOV SP, ER14
```

`SP` được chuyển sang vị trí mới.

Tuy nhiên gadget vẫn còn hai lệnh:

```asm
POP ER14
POP PC
```

Điều đó có nghĩa là ngay sau khi đổi `SP`, CPU sẽ **đọc tiếp hai giá trị đầu tiên** trong Stack mới.

Vì vậy, địa chỉ được đặt tại launcher phải lùi lại `2` byte để sau khi thực hiện `POP ER14`, lệnh `POP PC` sẽ lấy đúng gadget đầu tiên của chương trình (`setlr_pc`) thay vì bỏ qua hoặc lấy nhầm dữ liệu.

Đây là một kỹ thuật thường gặp trong ROP nhằm căn chỉnh Stack sao cho chuỗi gadget bắt đầu đúng vị trí mong muốn.


---

[<- Quay lại](4_ROP.md) | [Tiếp theo ->](6_GoiHamVaMidFunctionJump.md)
