# Register

## Thanh ghi là gì?

Register (viết tắt là reg) nghĩ là thanh ghi, nó đóng vai trò như là 1 biến ở trong các ngôn ngữ lập trình bậc cao nhưng nó lại có 1 số khác biệt.

Bản thân thanh ghi có vai trò giúp mã máy tính có thể tương tác với Stack và bộ nhớ.

## Tính chất của Thanh ghi

1. **Tốc độ truy xuất rất nhanh**
   Thanh ghi nằm bên trong CPU nên CPU có thể truy xuất trực tiếp và rất nhanh. Vì vậy, thanh ghi thường được sử dụng để lưu trữ các giá trị mà CPU đang cần xử lý.

2. **Dung lượng nhỏ**
   Mỗi thanh ghi chỉ có thể lưu trữ một lượng dữ liệu giới hạn. Kích thước của thanh ghi phụ thuộc vào kiến trúc của CPU, chẳng hạn như 8-bit, 16-bit, 32-bit hoặc 64-bit..

3. **Số lượng có hạn**
   Một CPU chỉ có một số lượng thanh ghi nhất định. Không giống như bộ nhớ chính có thể lưu trữ rất nhiều dữ liệu, số lượng thanh ghi bị giới hạn bởi thiết kế của kiến trúc CPU.

4. **Có vai trò khác nhau tùy kiến trúc CPU**
   Không phải tất cả thanh ghi đều hoạt động giống nhau. Một số thanh ghi có thể dùng để lưu dữ liệu, một số dùng làm địa chỉ, quản lý Stack hoặc phục vụ những chức năng đặc biệt khác. Những đặc điểm này phụ thuộc vào từng kiến trúc CPU.

## Thanh ghi trong kiến trúc nx-u8/100

Trong nx-u8/100 thanh ghi sở hũu 1 tính chất khá đặc biệt đó là cây thanh ghi.

Bạn hãy tưởng tượng 1 cây đồ thị nhánh lớn nhất là QRn và bé nhất là Rn nó sẽ trông như sau:

```text
                   ┌────────────┐
                   │    QR0     │
                   └─────┬──────┘
                  ┌──────┴───────┐
                XR0             XR1
                 │               │
             ┌───┴───┐       ┌───┴───┐
            ER0     ER1     ER2     ER3
            │        │       │       │
          R0 R1    R2 R3   R4 R5   R6 R7
```

Bạn có thể hiểu nôm na nếu R0 = 0x01 và R1 = 0x02 thì ER0 = 0x0201

> Lý giải 1 chút vì sao nếu R0 = 0x01 và R1 = 0x02 thì ER0 = 0x0201. Có 1 điều khá thú vị liên quan đến Endianness (thứ tự bytes) cụ thể là **Byte Order**. Đa phần các kiến trúc máy tính hiện nay thường sử dụng **Little Endian** trong khi nx-u8/100 lại sử dụng **Big Endian**. Chính vì vậy mà trong ví dụ trên R1 lại được nằm bên trái và R0 nằm bên phải.

Bên cạnh đó nx-u8/100 còn sở hữu nhiều thanh ghi mở rộng khác như là LR, PC, EA, SP.

## PC (Program Counter)

**PC (Program Counter)** là thanh ghi chứa **địa chỉ của lệnh sẽ được CPU thực thi tiếp theo**.

Hiểu nôm na bạn hãy tưởng tượng CPU là 1 người mù nhưng lại vô cùng thông minh, anh ấy không nhìn thấy gì hết, nên anh ta đã sử dụng 1 cây gậy dẫn đường để dò đường, cây gậy đó chính là **PC**.

Lấy ví dụ:
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

## Link Register (LR)

**LR (Link Register)** là thanh ghi dùng để lưu **địa chỉ trả về (Return Address)** khi một hàm được gọi.

Bạn có thể hiểu đơn giản rằng khi CPU gọi một hàm khác, nó cần phải **ghi nhớ mình đang ở đâu** để sau khi hàm đó kết thúc, CPU có thể quay trở lại và tiếp tục thực thi chương trình.

Ví dụ:

```text
P1:

    ...

    CALL P2

    ...

```

Khi thực hiện `CALL P2`, CPU sẽ chuyển sang thực thi `P2`.

Nhưng trước khi làm điều đó, CPU cần lưu lại **địa chỉ của lệnh tiếp theo sau `CALL P2`**. Địa chỉ này chính là **Return Address** và được lưu vào thanh ghi **LR**.

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

lệnh tiếp theo nằm tại địa chỉ `08`, vì vậy:

```text
LR = 0x0008
```

Sau đó CPU chuyển sang thực thi `P2`.

Khi `P2` kết thúc, CPU sử dụng giá trị trong `LR` để biết rằng nó cần quay trở lại:

```text
PC = LR
PC = 0x0008
```

CPU tiếp tục thực thi chương trình từ địa chỉ `0x0008`.

---

## Khi chỉ có một lời gọi hàm

Nếu chương trình chỉ gọi một hàm duy nhất thì việc sử dụng **LR** là hoàn toàn đủ.

Ví dụ:

```text
P1

 └── CALL P2
```

Quá trình diễn ra như sau:

```text
P1
 │
 │ CALL P2
 │
 ├── LR = địa chỉ trả về của P1
 │
 ▼
P2
 │
 │ kết thúc
 │
 ▼
quay lại P1 bằng LR
```

Mọi thứ đều hoạt động bình thường.

Tuy nhiên, vấn đề sẽ xuất hiện khi **các hàm gọi lồng nhau**.

---

## Khi các hàm gọi lồng nhau

Hãy thử tưởng tượng chương trình có cấu trúc:

```text
P1

 └── CALL P2

         └── CALL P3
```

Ban đầu, `P1` gọi `P2`:

```text
P1 gọi P2

LR = địa chỉ trả về của P1
```

Lúc này `LR` đang chứa địa chỉ để `P2` có thể quay trở lại `P1`.

Nhưng sau đó `P2` lại gọi `P3`.

Khi thực hiện:

```text
CALL P3
```

CPU lại cần lưu một **Return Address** mới vào `LR`:

```text
P2 gọi P3

LR = địa chỉ trả về của P2
```

Vấn đề là giá trị cũ của `LR` đã bị **ghi đè**.

```text
Trước khi P2 gọi P3:

LR = địa chỉ trả về của P1


Sau khi P2 gọi P3:

LR = địa chỉ trả về của P2
```

Khi `P3` kết thúc, CPU vẫn có thể quay trở lại `P2` vì `LR` đang chứa địa chỉ trả về của `P2`.

Nhưng khi `P2` kết thúc, CPU **không còn biết phải quay trở lại đâu trong P1** nữa.

Địa chỉ trả về ban đầu đã bị mất.

Nói cách khác:

> **LR chỉ có thể lưu một địa chỉ trả về tại một thời điểm.**

Nếu các hàm có thể gọi lồng nhau, chúng ta cần một nơi khác để lưu lại những giá trị `LR` cũ.

---

## Vậy làm sao để giải quyết điều này?

Một cách phổ biến là trước khi gọi một hàm khác, chương trình sẽ **lưu giá trị hiện tại của LR vào Stack**.

Ví dụ:

```text
P1
 │
 │ CALL P2
 │
 │ LR = địa chỉ quay về P1
 ▼
P2
 │
 │ lưu LR vào Stack
 │
 │ CALL P3
 │
 │ LR = địa chỉ quay về P2
 ▼
P3
```

Khi `P3` kết thúc, `P2` có thể lấy lại địa chỉ cũ từ Stack để tiếp tục xử lý.

Sau đó khi `P2` kết thúc, chương trình tiếp tục sử dụng địa chỉ đã được lưu trước đó để quay trở lại `P1`.

Như vậy, **Stack có thể lưu nhiều Return Address trong khi LR chỉ lưu được một giá trị tại một thời điểm**.

Đây cũng chính là một trong những lý do Stack đóng vai trò rất quan trọng trong việc **quản lý lời gọi hàm**.

Trong chương tiếp theo, chúng ta sẽ tìm hiểu **Stack (Ngăn xếp)** là gì, cách Stack hoạt động và vì sao nó lại đóng vai trò quan trọng trong **Return-Oriented Programming**.

---

[<- Quay lại](0_MoDau.md) | [Tiếp theo ->](2_Stack.md)
