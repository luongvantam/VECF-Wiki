# Stack

## Stack là gì?

**Stack (Ngăn xếp)** là một cấu trúc dữ liệu hoạt động theo nguyên tắc **LIFO (Last In, First Out)**, nghĩa là **phần tử được đưa vào sau sẽ được lấy ra trước**.

Có thể hiểu đơn giản thì **Stack (ngăn xếp)** là một kiểu dữ liệu hoạt động giống như **chồng đĩa**. Giả sử nhà bạn có rất nhiều đĩa bẩn.

Bạn không thể lấy một cái đĩa **ở giữa chồng** ra trước, vì những cái ở trên đang đè lên nó. Vì vậy:

* Đĩa nào **đặt vào sau** → nằm **trên cùng**

* Đĩa nào **lấy ra trước** → cũng là **đĩa trên cùng**

Đây chính là nguyên tắc:

> **LIFO — Last In, First Out (vào sau → ra trước)**

---

### 1. PUSH — thêm phần tử

Bạn có một chồng đĩa:

```text
  [Đĩa 3]  ← trên cùng
  [Đĩa 2]
  [Đĩa 1]
```

Bạn có thêm **Đĩa 4** và đặt lên trên:

```text
  [Đĩa 4]  ← trên cùng
  [Đĩa 3]
  [Đĩa 2]
  [Đĩa 1]
```

Hành động **đặt thêm Đĩa 4 vào Stack** gọi là:

**PUSH(4)**

---

### 2. POP — lấy phần tử ra

Bây giờ bạn muốn rửa đĩa.

Bạn phải lấy **Đĩa 4** ra trước:

```text
POP()
```

Kết quả:

```text
  [Đĩa 3]  ← trên cùng
  [Đĩa 2]
  [Đĩa 1]
```

Tiếp tục `POP()`:

```text
  [Đĩa 2]
  [Đĩa 1]
```

Tiếp tục:

```text
  [Đĩa 1]
```

Cuối cùng:

```text
Stack rỗng
```

---

### Điểm quan trọng nhất

Stack chỉ quan tâm đến **một đầu — TOP (đỉnh)**.

```text
        TOP
         ↓

      [ 4 ]  ← PUSH / POP ở đây
      [ 3 ]
      [ 2 ]
      [ 1 ]
```

Bạn **không lấy trực tiếp phần tử 1 hoặc 2** ra được theo cách hoạt động chuẩn của Stack.

Nếu:

```text
PUSH(10)
PUSH(20)
PUSH(30)
```

thì Stack là:

```text
[30] ← TOP
[20]
[10]
```

`POP()` → **30**

`POP()` → **20**

`POP()` → **10**

---

## Stack Pointer (SP)

Để CPU có thể biết **đỉnh Stack đang nằm ở đâu**, CPU cần một thanh ghi đặc biệt gọi là **SP (Stack Pointer)**.

**SP** là thanh ghi chứa **địa chỉ của vị trí hiện tại của Stack**, thường được sử dụng để xác định **đỉnh Stack (TOP)**.

Bạn có thể tưởng tượng SP giống như một **ngón tay chỉ vào đỉnh của chồng đĩa**:

```text
             SP
              ↓

           [ 30 ] ← TOP
           [ 20 ]
           [ 10 ]
```

Khi thực hiện `PUSH`, Stack có thêm một phần tử mới và **SP sẽ thay đổi để trỏ tới vị trí mới**.

Ví dụ:

```text
Trước PUSH:

SP → [ 20 ]
     [ 10 ]
```

Sau:

```text
PUSH(30)

SP → [ 30 ]
     [ 20 ]
     [ 10 ]
```

Ngược lại, khi thực hiện `POP`, phần tử trên cùng được lấy ra và **SP sẽ thay đổi để trỏ tới phần tử tiếp theo**.

```text
Trước POP:

SP → [ 30 ]
     [ 20 ]
     [ 10 ]
```

Sau:

```text
POP()

SP → [ 20 ]
     [ 10 ]
```

Như vậy có thể hiểu đơn giản:

> **SP = thanh ghi dùng để theo dõi vị trí của TOP trong Stack.**

---

## SP và địa chỉ bộ nhớ

Stack thường được đặt trong **bộ nhớ** chứ không phải nằm bên trong các thanh ghi.

Vì vậy, SP không chứa trực tiếp dữ liệu của toàn bộ Stack. Nó chủ yếu chứa **địa chỉ bộ nhớ nơi Stack đang hoạt động**.

Ví dụ:

```text
Memory:

0x1000: 10
0x1001: 20
0x1002: 30
```

Nếu `30` đang nằm trên cùng:

```text
SP = 0x1002
```

CPU có thể sử dụng `SP` để biết vị trí cần thao tác khi thực hiện `PUSH` hoặc `POP`.

Điều này cũng giải thích tại sao **SP là một thanh ghi rất quan trọng khi làm việc với Stack**.

Đặc biệt, khi kết hợp với **LR (Link Register)**, Stack có thể được sử dụng để lưu lại các **Return Address** khi các hàm gọi lồng nhau.

---

[<- Quay lại](1_Registers.md) | [Tiếp theo ->](3_ROP.md)
