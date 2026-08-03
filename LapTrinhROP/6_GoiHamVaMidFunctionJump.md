# Gọi hàm trong Firmware

Ở chương trước, chúng ta đã viết chương trình **Hello World** đầu tiên và sử dụng các lệnh như:

```rsc
line_print
render.ddd4
```

Thực chất, đây chỉ là các **macro** do RAC Compiler cung cấp để giúp việc lập trình trở nên đơn giản hơn.

Bên dưới các macro này, chương trình thực chất đang điều khiển CPU **nhảy đến các hàm đã tồn tại sẵn trong firmware** để thực hiện các chức năng như in văn bản, vẽ màn hình hay xử lý dữ liệu.

Đây cũng chính là sức mạnh của **Return-Oriented Programming (ROP)**. Thay vì tự viết lại mọi chức năng từ đầu, chúng ta có thể tận dụng những hàm đã được Casio xây dựng sẵn.

Tuy nhiên, việc gọi trực tiếp một hàm trong firmware cũng tiềm ẩn một vấn đề rất lớn có thể khiến toàn bộ ROP Chain bị hỏng. Trong chương này, chúng ta sẽ tìm hiểu nguyên nhân của vấn đề đó và cách giải quyết bằng kỹ thuật **Mid-function Jump**.

---

# Cấu trúc của một hàm trong Firmware

Hãy nhớ lại kiến thức ở chương **Stack**.

Khi một hàm được gọi bằng các lệnh như `CALL` hoặc `BL`, CPU cần lưu địa chỉ trả về để sau khi hàm kết thúc có thể tiếp tục thực thi chương trình.

Vì vậy, hầu hết các hàm trong firmware đều có cấu trúc tương tự như sau:

```asm
f_example_function:
    PUSH LR

    ; --- Function Body ---
    MOV R0, #1
    ADD R1, R2
    ...
    ; ---------------------

    POP PC
```

Ngay khi bắt đầu, hàm sẽ thực hiện:

```asm
PUSH LR
```

để lưu địa chỉ trả về xuống Stack.

Sau khi hoàn thành công việc, hàm sẽ thực hiện:

```asm
POP PC
```

để lấy địa chỉ đó từ Stack và quay trở về nơi đã gọi.

Trong lập trình thông thường, cơ chế này hoạt động hoàn toàn chính xác.

---

# Vấn đề khi gọi hàm bằng ROP

Trong ROP, Stack không còn được tạo bởi lời gọi hàm nữa mà đã được **chúng ta chuẩn bị trước**.

Ví dụ:

```text
Stack

<TOP>

<Địa chỉ f_example_function>
<Gadget B>
<Gadget C>

<BOTTOM>
```

Khi gadget trước thực hiện:

```asm
POP PC
```

CPU sẽ lấy địa chỉ của `f_example_function` và bắt đầu thực thi từ đầu hàm.

Lệnh đầu tiên của hàm là:

```asm
PUSH LR
```

Chính lệnh này sẽ làm phát sinh vấn đề.

`PUSH LR` sẽ lấy giá trị hiện có trong thanh ghi `LR` rồi đẩy thêm một phần tử mới lên Stack.

Lúc này Stack sẽ trở thành:

```text
Stack

<TOP>

<Giá trị trong LR>
<Gadget B>
<Gadget C>

<BOTTOM>
```

Như vậy, cấu trúc Stack mà chúng ta đã chuẩn bị trước đã bị thay đổi.

Sau khi phần thân của hàm thực thi xong, CPU gặp:

```asm
POP PC
```

Thay vì lấy địa chỉ của **Gadget B**, CPU lại lấy giá trị vừa được `PUSH LR` lưu xuống.

Nếu giá trị này không phải địa chỉ mà chúng ta mong muốn, chương trình sẽ nhảy sang một vị trí không xác định và ROP Chain sẽ bị đứt hoàn toàn.

---

# Mid-function Jump

Để tránh việc hàm làm thay đổi Stack, chúng ta sẽ **không nhảy vào đầu hàm**.

Ví dụ, một hàm trong firmware có dạng:

```asm
0x1000    PUSH LR
0x1002    MOV R0, #1
0x1004    ADD R1, R2
...
0x1040    POP PC
```

Thay vì sử dụng địa chỉ:

```text
0x1000
```

chúng ta sẽ sử dụng:

```text
0x1002
```

Điều này có nghĩa là CPU sẽ **bỏ qua lệnh `PUSH LR`** và bắt đầu thực thi ngay từ phần thân của hàm.

Ví dụ:

```text
Stack

<TOP>

0x1002
<Gadget B>
<Gadget C>

<BOTTOM>
```

Quá trình thực thi sẽ diễn ra như sau:

1. CPU lấy địa chỉ `0x1002` từ Stack.
2. Bỏ qua lệnh `PUSH LR`.
3. Thực thi toàn bộ phần thân của hàm.
4. Khi gặp `POP PC`, CPU lấy địa chỉ của **Gadget B** từ Stack.
5. ROP Chain tiếp tục hoạt động bình thường.

Kỹ thuật này được gọi là **Mid-function Jump** (Nhảy vào giữa hàm).

Vậy khi dùng RAC Compiler và bạn dùng trực tiếp các macro này có cần phải thêm 2 để tránh `PUSH LR` không?
> Câu trả lời là **không** vì RAC đã hỗ trợ cho bạn khi gọi các macro như line_print, char_print, calc_func, ... thì nếu nó bắt đầu bằng `PUSH LR` compiler sẽ tự động cộng 2 để tránh `PUSH LR` giúp bạn.

---

# Truyền tham số cho hàm

Các hàm trong firmware thường không tự hoạt động mà cần dữ liệu đầu vào.

Những dữ liệu này thường được truyền qua các thanh ghi như:

* `R0`, `R1`, `R2`
* `ER0`, `ER2`
* `XR0`

Vì vậy, trước khi gọi một hàm, chúng ta cần sử dụng các gadget để chuẩn bị sẵn các thanh ghi này.

Ví dụ, giả sử có một hàm tại địa chỉ `0x3000` dùng để vẽ hình chữ nhật và yêu cầu:

* `ER0` chứa tọa độ.
* `R2` chứa chiều rộng.

Ta có thể chuẩn bị Stack như sau:

```text
Stack

<TOP>

<Gadget: POP ER0; POP PC>
<Tọa độ>

<Gadget: POP R2; POP PC>
20

0x3002

<Gadget tiếp theo>

<BOTTOM>
```

Quá trình thực thi sẽ là:

1. Gadget đầu tiên nạp tọa độ vào `ER0`.
2. Gadget tiếp theo nạp giá trị `20` vào `R2`.
3. CPU nhảy tới `0x3002`, bỏ qua `PUSH LR`.
4. Hàm thực hiện công việc của mình.
5. Khi gặp `POP PC`, CPU tiếp tục thực thi gadget kế tiếp.

Nhờ đó, chúng ta có thể sử dụng gần như toàn bộ các hàm trong firmware mà vẫn giữ nguyên cấu trúc của ROP Chain.

Nhưng có 1 cách tối ưu khác thay vì phải gọi `<Gadget: POP ER0; POP PC>` và `<Gadget: POP R2; POP PC>` bạn có thể gọi trực tiếp `<Gadget: POP XR0; POP PC>` vì bản chất `XR0 = ER0 và ER2` hay `XR0 = R0, R1, R2 và R3`. Tuy nhiên cùng với đó bạn cần đảm bảo rằng tránh không có thanh ghi nào đang sử dụng đến mà bạn lại thay đổi nó thành byte rác.

---

Qua đó, nhờ kỹ thuật này, chúng ta có thể tận dụng những hàm mạnh mẽ đã tồn tại sẵn trong firmware mà không cần tự viết lại toàn bộ chức năng, đồng thời vẫn giữ cho ROP Chain hoạt động ổn định.


---

[<- Quay lại](5_VietChuongTrinhDonGian.md) | [Tiếp theo ->](7_JumpSP.md)
