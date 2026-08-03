# Stack

## Stack là gì?

**Stack (Ngăn xếp)** là một cấu trúc dữ liệu hoạt động theo nguyên tắc **LIFO (Last In, First Out)**, nghĩa là **phần tử được đưa vào sau sẽ được lấy ra trước**.

Có thể hình dung Stack giống như một chồng sách.

* Khi đặt thêm một quyển sách, ta chỉ có thể đặt lên trên cùng.
* Khi lấy sách ra, ta cũng chỉ có thể lấy quyển nằm trên cùng.

CPU cũng sử dụng Stack theo nguyên tắc tương tự để lưu trữ dữ liệu tạm thời trong quá trình thực thi chương trình.

---

## PUSH và POP

CPU thao tác với Stack chủ yếu thông qua hai lệnh:

* **`PUSH`**: đưa một giá trị lên đỉnh Stack.
* **`POP`**: lấy giá trị ở đỉnh Stack ra khỏi Stack.

Ví dụ dưới đây minh họa quá trình đưa ba giá trị vào Stack rồi lấy chúng ra.

```text
         [LR:0]            [LR:0]            [LR:0]            [LR:2]            [LR:1]            [LR:0]
        PUSH <0>          PUSH <1>          PUSH <2>           POP LR            POP LR            POP LR
   +------->--------+ +------->-------+ +------->-------+ +------->-------+ +------->-------+ +------->--------+
   |                | |               | |               | |               | |               | |                |
   ^                V ^               V ^               V ^               V ^               V ^                V

 Stack:            Stack:            Stack:            Stack:            Stack:            Stack:            Stack:
<BOTTOM>          <BOTTOM>          <BOTTOM>          <BOTTOM>          <BOTTOM>          <BOTTOM>          <BOTTOM>
 <TOP>              <0>               <0>               <0>               <0>               <0>              <TOP>
                   <TOP>              <1>               <1>               <1>              <TOP>
                                     <TOP>              <2>              <TOP>
                                                       <TOP>
```

Sau khi thực hiện:

```asm
PUSH <0>
PUSH <1>
PUSH <2>
```

Stack sẽ có dạng:

```text
<TOP>
<2>
<1>
<0>
<BOTTOM>
```

Khi CPU thực hiện:

```asm
POP LR
```

Giá trị `<2>` sẽ được lấy ra trước.

Lần `POP` tiếp theo sẽ lấy `<1>`.

Cuối cùng mới đến `<0>`.

Ta có thể thấy dữ liệu được đưa vào sau sẽ được lấy ra trước, đúng với nguyên tắc **Last In, First Out (LIFO)**.

---

## Vì sao CPU cần Stack?

Ở chương trước, chúng ta đã biết rằng mỗi khi gọi một hàm, CPU sẽ lưu **địa chỉ trả về (Return Address)** vào thanh ghi **LR**.

Tuy nhiên, **LR chỉ có một thanh ghi duy nhất**.

Nếu một hàm đang thực thi lại tiếp tục gọi một hàm khác thì giá trị cũ trong LR sẽ bị ghi đè.

Ví dụ:

```text
P1
 └── CALL P2
         └── CALL P3
```

Quá trình sẽ diễn ra như sau:

```text
P1 gọi P2

LR = địa chỉ trả về của P1
```

Sau đó:

```text
P2 gọi P3

LR = địa chỉ trả về của P2
```

Lúc này địa chỉ trả về của `P1` đã bị mất.

Nếu `P2` kết thúc, CPU sẽ không còn biết phải quay trở lại đâu trong `P1`.

Đây chính là lý do Stack được sử dụng.

---

## Lưu địa chỉ trả về bằng Stack

Trước khi gọi một hàm khác, chương trình sẽ lưu giá trị hiện tại của **LR** lên Stack.

```asm
PUSH LR
```

Sau đó mới thực hiện lời gọi hàm.

Khi hàm con kết thúc, địa chỉ trả về đã lưu trên Stack sẽ được lấy ra để CPU tiếp tục thực thi chương trình.

Trên kiến trúc **nX-U8/100**, nhiều gadget và nhiều đoạn mã trong firmware kết thúc bằng:

```asm
POP PC
```

Lệnh này lấy giá trị trên đỉnh Stack và nạp trực tiếp vào thanh ghi **PC (Program Counter)**.

Vì **PC luôn chứa địa chỉ của lệnh sẽ được thực thi tiếp theo**, CPU sẽ ngay lập tức tiếp tục chạy tại địa chỉ vừa lấy từ Stack.

```text
Bước 1                 Bước 2                 Bước 3                 Bước 4                 Bước 5
──────────────────────────────────────────────────────────────────────────────────────────────────────────

PC = <p1>              PC = <p2>              PC = <p3>              PC = <p2 return>       PC = <p1 return>
LR = <p1 return>       LR = <p1 return>       LR = <p2 return>       LR = <p2 return>       LR = <p1 return>

 Stack:                 Stack:                 Stack:                 Stack:                 Stack:
<BOTTOM>               <BOTTOM>               <BOTTOM>               <BOTTOM>               <BOTTOM>
 <TOP>               <p1 return>             <p1 return>           <p1 return>               <TOP>
                        <TOP>                <p2 return>              <TOP>
                                               <TOP>

P1              +-->   P2              +---->   P3                     P3                    P2
───             |      ───             |        ───                    ───                   ───
...             |      PUSH LR         |        PUSH LR                PUSH LR               POP LR
CALL P2  -------+      CALL P3  -------+        ...                    ...                   CALL P3
...                    ...                      POP PC                 POP PC  --------->    ...
                       POP PC                                                                POP PC  ------> <p1 return>
```

Sau hình, giải thích:

1. `P1` gọi `P2`, địa chỉ trả về của `P1` được lưu vào `LR`.
2. `P2` thực hiện `PUSH LR`, lưu địa chỉ trả về của `P1` lên Stack.
3. `P2` gọi `P3`, lúc này `LR` được cập nhật thành địa chỉ trả về của `P2`.
4. `P3` cũng thực hiện `PUSH LR`, lưu địa chỉ trả về của `P2` lên Stack.
5. Khi `P3` kết thúc, `POP PC` lấy địa chỉ trả về của `P2` từ Stack để CPU tiếp tục thực thi trong `P2`.
6. Khi `P2` kết thúc, `POP PC` tiếp tục lấy địa chỉ trả về của `P1`, giúp CPU quay trở lại `P1`.

Nhờ có Stack, mỗi lời gọi hàm đều có thể lưu lại địa chỉ trả về của riêng mình. Vì vậy, dù các hàm gọi lồng nhau nhiều cấp, CPU vẫn luôn có thể quay trở về đúng vị trí đã gọi chúng.

---

## Stack cân bằng (Balanced Stack)

Trong một chương trình được viết đúng, mỗi giá trị được đưa lên Stack bằng `PUSH` sẽ có một lệnh `POP` tương ứng để lấy giá trị đó ra.

Khi điều này được đảm bảo, Stack sẽ trở về đúng trạng thái ban đầu sau khi hàm kết thúc.

Đây được gọi là **Stack cân bằng (Balanced Stack)**.

Ngược lại, nếu số lần `PUSH` và `POP` không khớp nhau thì Stack sẽ bị **mất cân bằng (Stack Imbalance)**.

Ví dụ:

* Nếu `PUSH` nhiều hơn `POP`, dữ liệu cũ sẽ tiếp tục nằm lại trên Stack, khiến Stack ngày càng đầy.
* Nếu `POP` nhiều hơn `PUSH`, CPU sẽ lấy những dữ liệu không mong muốn từ Stack. Điều này có thể khiến chương trình sử dụng dữ liệu sai hoặc nhảy đến một địa chỉ không hợp lệ.

Vì vậy, giữ cho Stack luôn cân bằng là một nguyên tắc rất quan trọng trong lập trình hợp ngữ.

---

## Tổng kết

Đến thời điểm này, chúng ta đã biết:

* **PC** quyết định CPU sẽ thực thi lệnh nào tiếp theo.
* **LR** lưu địa chỉ trả về của một lời gọi hàm.
* **Stack** được sử dụng để lưu tạm các địa chỉ trả về khi các hàm gọi lồng nhau.
* `PUSH` và `POP` là hai thao tác cơ bản để làm việc với Stack.

Tuy nhiên, Return-Oriented Programming không chỉ sử dụng Stack để lưu địa chỉ trả về của hàm.

Trong chương tiếp theo, chúng ta sẽ tìm hiểu điều gì xảy ra khi **Stack không còn chứa địa chỉ trả về do chương trình tạo ra**, mà thay vào đó là những giá trị được **chuẩn bị trước**. Đây chính là bước chuyển từ cơ chế gọi hàm thông thường sang **Return-Oriented Programming (ROP)**.

---

## Tài liệu tham khảo:

* https://en.wikipedia.org/wiki/Stack-based_memory_allocation

---

[<- Quay lại](2_Registers.md) | [Tiếp theo ->](4_ROP.md)
