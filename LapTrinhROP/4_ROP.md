# Gadget và Return-Oriented Programming

## Gadget là gì?

Trong chương trước, chúng ta đã biết rằng sau mỗi lệnh:

```asm
POP PC
```

CPU sẽ lấy một giá trị từ Stack và nạp trực tiếp vào thanh ghi **PC**.

```text
PC = Stack.top()
```

Điều này có nghĩa là nếu trên Stack chứa địa chỉ của một đoạn mã trong firmware, CPU sẽ bắt đầu thực thi từ chính địa chỉ đó.

Thông thường, địa chỉ này không trỏ đến một hàm hoàn chỉnh mà chỉ trỏ tới một đoạn mã rất ngắn, ví dụ:

```asm
POP ER0
POP PC
```

hoặc

```asm
ADD R0, R1
POP PC
```

Những đoạn mã ngắn như vậy được gọi là **gadget**.

Có thể hiểu đơn giản:

> **Gadget là một đoạn mã đã tồn tại sẵn trong firmware, thực hiện một thao tác nhỏ rồi kết thúc bằng `POP PC` để chuyển quyền điều khiển sang gadget tiếp theo.**

---

## Gadget hoạt động như thế nào?

Giả sử firmware có gadget sau:

```asm
POP ER0
POP PC
```

và Stack được chuẩn bị như sau:

```text
<TOP>

0x1234
<Gadget B>

<BOTTOM>
```

CPU bắt đầu thực thi gadget.

Lệnh đầu tiên:

```asm
POP ER0
```

lấy giá trị đầu tiên trên Stack:

```text
ER0 = 0x1234
```

Stack lúc này còn:

```text
<TOP>

<Gadget B>

<BOTTOM>
```

Tiếp theo:

```asm
POP PC
```

CPU lấy giá trị tiếp theo:

```text
PC = <Gadget B>
```

Ngay lập tức CPU chuyển sang thực thi **Gadget B**.

Có thể thấy, gadget vừa thực hiện công việc của mình vừa quyết định gadget nào sẽ được thực thi tiếp theo.

---

## Gadget không chỉ có `POP`

Không phải mọi gadget đều chỉ dùng các lệnh `POP`.

Ví dụ:

```asm
ADD R0, R1
POP PC
```

```asm
MOV EA, ER12
POP PC
```

```asm
INC R0
POP PC
```

Mỗi gadget chỉ đảm nhận một thao tác đơn giản như:

* Nạp dữ liệu vào thanh ghi.
* Sao chép dữ liệu giữa các thanh ghi.
* Thực hiện phép cộng hoặc phép trừ.
* Đọc hoặc ghi dữ liệu trong bộ nhớ.

Miễn là đoạn mã kết thúc bằng `POP PC` (hoặc cơ chế trả về tương đương), nó đều có thể được sử dụng như một gadget.

---

## Gadget được lấy ở đâu?

Điểm đặc biệt của ROP là **chúng ta không tự viết gadget**.

Các gadget đã tồn tại sẵn trong firmware.

Ví dụ một hàm bình thường:

```asm
...
ADD R0, R1
MOV EA, ER0
POP PC
```

Nếu CPU bắt đầu thực thi từ lệnh:

```asm
MOV EA, ER0
POP PC
```

thì phần mã này cũng có thể được sử dụng như một gadget mới.

Vì vậy, **gadget không nhất thiết phải bắt đầu từ đầu một hàm**.

Chỉ cần CPU có thể bắt đầu thực thi từ địa chỉ đó và đoạn mã kết thúc bằng `POP PC`, chúng ta đều có thể tận dụng nó.

Nhờ đó, một firmware thường chứa rất nhiều gadget khác nhau.

---

## Từ Gadget đến ROP Chain

Một gadget riêng lẻ chỉ thực hiện được một thao tác.

Ví dụ firmware có ba gadget:

```asm
; Gadget A
POP ER0
POP PC
```

```asm
; Gadget B
ADD ER0, ER2
POP PC
```

```asm
; Gadget C
MOV EA, ER0
POP PC
```

Nếu Stack được chuẩn bị như sau:

```text
<TOP>

0x1000
<Gadget B>
<Gadget C>

<BOTTOM>
```

CPU sẽ thực thi theo thứ tự:

```text
Gadget A
    │
    ▼
ER0 = 0x1000
    │
    ▼
POP PC
    │
    ▼
Gadget B
    │
    ▼
ER0 = ER0 + ER2
    │
    ▼
POP PC
    │
    ▼
Gadget C
    │
    ▼
EA = ER0
```

Chuỗi các gadget được thực thi liên tiếp theo cách này được gọi là **ROP Chain (Return-Oriented Programming Chain)**.

---

## Return-Oriented Programming

Đến đây có thể thấy nguyên lý của **Return-Oriented Programming (ROP)** khá đơn giản.

CPU không hề biết địa chỉ được nạp vào `PC` có phải là địa chỉ trả về của một hàm hay không.

Đối với CPU, lệnh:

```asm
POP PC
```

chỉ đơn giản là:

```text
PC = Stack.top()
```

Nếu Stack chứa:

```text
<TOP>

<Gadget A>
<Gadget B>
<Gadget C>

<BOTTOM>
```

CPU sẽ thực thi:

```text
POP PC
    │
    ▼
Gadget A
    │
    ▼
POP PC
    │
    ▼
Gadget B
    │
    ▼
POP PC
    │
    ▼
Gadget C
```

Mỗi gadget sau khi hoàn thành công việc của mình sẽ kết thúc bằng `POP PC`, nhờ đó CPU tiếp tục lấy địa chỉ gadget kế tiếp từ Stack.

Toàn bộ luồng thực thi của chương trình lúc này được quyết định bởi dữ liệu đã được sắp xếp trên Stack.

Đây chính là nguyên lý cốt lõi của **Return-Oriented Programming**: **không cần chèn thêm mã máy mới**, mà chỉ tận dụng các đoạn mã đã tồn tại sẵn trong firmware và điều khiển thứ tự thực thi của chúng thông qua Stack.

---

## Tổng kết

Qua chương này, chúng ta đã biết:

* **Gadget** là những đoạn mã ngắn đã tồn tại sẵn trong firmware.
* Mỗi gadget thường thực hiện một thao tác nhỏ rồi kết thúc bằng `POP PC`.
* `POP PC` giúp CPU chuyển sang gadget tiếp theo được lưu trên Stack.
* Nhiều gadget có thể được ghép lại thành một **ROP Chain**.
* **Return-Oriented Programming (ROP)** là kỹ thuật xây dựng chương trình bằng cách sắp xếp các gadget trên Stack thay vì chèn thêm mã máy mới.

---

[<- Quay lại](3_Stack.md) | [Tiếp theo ->](5_VietChuongTrinhDonGian.md)
