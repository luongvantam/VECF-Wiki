# Register

## Thanh ghi là gì?

Bên cạnh bộ nhớ (Memory), bên trong CPU còn có một vùng lưu trữ đặc biệt gọi là **thanh ghi (Register)**.

Thanh ghi là nơi CPU lưu trữ tạm thời dữ liệu và địa chỉ trong quá trình thực thi chương trình. So với bộ nhớ thông thường, thanh ghi có tốc độ truy cập rất nhanh vì chúng nằm ngay bên trong CPU.

Trong suốt quá trình chương trình hoạt động, CPU sẽ liên tục đọc và ghi dữ liệu vào các thanh ghi để thực hiện các phép tính, truyền dữ liệu và điều khiển luồng thực thi.

Vi xử lý **nX-U8/100** có nhiều loại thanh ghi khác nhau như:

* `Rn`
* `ERn`
* `XRn`
* `QRn`
* `EA`
* `SP`
* `PC`
* `LR`
* ...

Mỗi thanh ghi đều có một chức năng riêng. Tuy nhiên, để hiểu cách Return-Oriented Programming hoạt động, trước tiên chúng ta chỉ cần quan tâm đến hai thanh ghi quan trọng nhất là **PC** và **LR**.

---

## Program Counter (PC)

**PC (Program Counter)** là thanh ghi chứa **địa chỉ của lệnh sẽ được CPU thực thi tiếp theo**.

CPU luôn hoạt động theo một chu kỳ rất đơn giản:

1. Đọc địa chỉ từ thanh ghi **PC**.
2. Lấy lệnh tại địa chỉ đó từ bộ nhớ.
3. Thực thi lệnh.
4. Cập nhật giá trị của **PC**.
5. Lặp lại quá trình trên.

Có thể hình dung **PC** giống như một con trỏ đang chỉ vào dòng lệnh hiện tại của chương trình.

Ví dụ:

```text
0x0000: MOV R0, #1
0x0002: ADD R0, #1
0x0004: SUB R0, #1
0x0006: ...
```

Ban đầu:

```text
PC = 0x0000
```

CPU sẽ thực hiện lệnh đầu tiên.

Sau khi hoàn thành, PC sẽ được cập nhật:

```text
PC = 0x0002
```

CPU tiếp tục thực hiện lệnh kế tiếp.

Quá trình này diễn ra liên tục cho đến khi chương trình kết thúc.

Nếu một lệnh thay đổi giá trị của **PC**, CPU sẽ không tiếp tục thực thi tuần tự nữa mà sẽ **nhảy đến địa chỉ mới**.

Ví dụ:

```asm
JMP 0x1000
```

Sau khi lệnh này được thực thi:

```text
PC = 0x1000
```

CPU sẽ bắt đầu thực thi từ địa chỉ `0x1000` thay vì dòng lệnh tiếp theo.

---

## Link Register (LR)

**LR (Link Register)** là thanh ghi dùng để lưu **địa chỉ trả về (Return Address)** khi một hàm được gọi.

Giả sử chương trình có đoạn mã:

```text
P1:
    ...
    CALL P2
    ...
```

Khi thực hiện `CALL P2`, CPU cần ghi nhớ vị trí để sau khi `P2` kết thúc có thể quay trở lại và tiếp tục thực thi trong `P1`.

Địa chỉ này được gọi là **Return Address** và được lưu vào thanh ghi **LR**.

Ví dụ:

```text
P1:

00: ...
04: CALL P2
08: ...
0C: ...
```

Khi CPU thực hiện:

```text
CALL P2
```

địa chỉ của lệnh kế tiếp (`08`) sẽ được lưu vào **LR**.

```text
LR = 0x0008
```

Sau đó CPU chuyển sang thực thi hàm `P2`.

Khi `P2` kết thúc, CPU sẽ sử dụng địa chỉ đã lưu để tiếp tục thực thi từ vị trí `0x0008`.

---

## Khi chỉ có một lời gọi hàm

Nếu chương trình chỉ gọi một hàm duy nhất, việc sử dụng thanh ghi **LR** là hoàn toàn đủ.

Ví dụ:

```text
P1
 └── CALL P2
```

Quá trình diễn ra như sau:

1. CPU lưu địa chỉ trả về của `P1` vào `LR`.
2. CPU chuyển sang thực thi `P2`.
3. `P2` kết thúc.
4. CPU quay trở lại `P1` bằng địa chỉ đã lưu trong `LR`.

Mọi thứ đều hoạt động bình thường.

---

## Khi các hàm gọi lồng nhau

Vấn đề xuất hiện khi một hàm đang thực thi lại tiếp tục gọi một hàm khác.

Ví dụ:

```text
P1
 └── CALL P2
         └── CALL P3
```

Ban đầu:

```text
P1 gọi P2

LR = địa chỉ trả về của P1
```

Sau đó `P2` tiếp tục gọi `P3`:

```text
P2 gọi P3

LR = địa chỉ trả về của P2
```

Lúc này, địa chỉ trả về của `P1` đã bị ghi đè.

Khi `P3` kết thúc, CPU vẫn có thể quay trở lại `P2` vì `LR` đang chứa địa chỉ trả về của `P2`.

Tuy nhiên, sau khi `P2` kết thúc, CPU **không còn biết phải quay trở về đâu trong `P1`**, vì địa chỉ trả về ban đầu đã bị mất.

Nói cách khác, **LR chỉ có thể lưu được một địa chỉ trả về tại một thời điểm**.

Nếu không có cơ chế lưu trữ bổ sung, chương trình sẽ không thể thực hiện các lời gọi hàm lồng nhau một cách chính xác.

---

## Vậy làm sao để giải quyết điều này ?

Để tránh việc địa chỉ trả về bị ghi đè, chương trình cần một nơi để lưu tạm các giá trị cũ của thanh ghi **LR** trước khi gọi một hàm khác.

Nơi lưu trữ này chính là **Stack (Ngăn xếp)**.

Trong chương tiếp theo, chúng ta sẽ tìm hiểu Stack là gì, cách Stack hoạt động và vì sao nó lại đóng vai trò quan trọng trong Return-Oriented Programming.


---

[<- Quay lại](1_ROPLaGi.md) | [Tiếp theo ->](3_Stack.md)
