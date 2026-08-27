# Giải bài toán lập trình cơ bản bằng ROP ASM

Xin chào! Đây là 1 bài viết mà tôi đã viết khi rảnh rỗi nối tiếp các chương trước. Sau 1 thời gian bận rộn với 1 số việc riêng và 1 số dự án vặt thì tôi đã dành thời gian viết chương này để hướng dẫn bạn viết thử 1 chương trình ROP.

Mặc dù có thể không đầy đủ hay gì đó nhưng bài viết này chỉ mang tính chất tham khảo và hướng dẫn bạn đọc.

## 1. Bài toán lập trình cơ bản 1
**Đề bài:** Cho vào 1 số nguyên dương N (1 <= N <= 100) kiểm tra xem đó là số chẵn hay số lẻ.

Input: 1 số nguyên dương N lưu vào biến A.
Output: in ra dòng "Odd" nếu N là số lẻ, in ra dòng "Even" nếu N là số chẵn.

Ví dụ:
```
| Input | Output |
+-------+--------+
| 5     | Odd    |
+-------+--------+
| 4     | Even   |
```

---
Ok được rồi đọc đề bài này tôi phải cảm thán rằng đây là 1 trong số các bài code thiếu nhi ai học lập trình cũng sẽ gặp 1 lần. Nhưng khi bắt đầu viết bằng ROP thì nó lại là 1 câu chuyện khác thú vị hơn nhiều.

Xuyên suốt các bài toán sau tôi sẽ không dạy bạn phải nhảy vào code mà tôi sẽ dạy bạn lập kế hoạch trước. Giống như khi bạn viết 1 bài văn thứ quan trọng nhất không phải là bạn viết hay đến đâu mà ý tưởng của bạn là gì và bạn triển khai nó như thế nào. Tương tự ROP ASM cũng vậy điều quan trọng nhất không phải là bạn có tìm ra được gadget hay không mà quan trọng nhất là bạn có kế hoạch cụ thể và triển khai nó như thế nào.

### Quay lại đề bài:

Bước đầu tiên của mọi bài ROP là gì? Đó chính là xác định mục tiêu và phân tích cách thức thực thi code. 

**Bước này hay được gọi là "Bước phân tích".**

Đầu tiên, hãy cùng phân tích mục tiêu của đề bài. Đầu tiên chúng ta cần phải biết đầu vào của bài toán là gì và đầu ra của bài toán là gì. Cụ thể, bài toán này đầu vào là 1 số nguyên dương N, và đầu ra là in ra dòng "Odd" hoặc "Even" tùy thuộc vào N là số chẵn hay số lẻ.

Như vậy ta có thể viết ra 1 sơ đồ như sau:
```
+---------------+
|    Input N    |
+---------------+       
       |
       v
+------------------------------------+    +--------------------+
| Kiểm tra đó là số chẵn hay số lẻ   | -> |   Nếu là số chẵn   |
+------------------------------------+    +--------------------+
       |                                             |
       v                                             v
+------------------+                      +--------------------+
|     Nếu không    |                      |     Output "Odd"   |
+------------------+                      +--------------------+
       |
       v
+------------------+
|   Output "Odd"   |
+------------------+
```

Ok vậy viết được kế hoạch như vầy rồi thì tôi sẽ chia ra làm 4 phần như sau =)):
- P0: Khởi tạo chương trình.
- P1: Lấy số N từ biến A (người dùng input trước đó).
- P2: Xử lý xem đó là số chẵn hay số lẻ rồi output ra dòng "Even" hoặc "Odd" tùy trường hợp.
- P3: Kết thúc chương trình.

Chương trình này tôi sẽ đặt ở vùng `0xE9E0`.

## P0: Khởi tạo chương trình

Đầu tiên là phần khởi tạo chúng ta sẽ thực hiện reset lại LR, SFR và lặt vặt các thứ.
```rsc
@section.main at 0xe9e0

lbl init
    setlr_pc
    setsfr
    di,rt
    buffer_clear
```
Sau khi thực hiện điều này xong ta sẽ đến bước tiếp theo.

## P1: Lấy số N từ biến A (người dùng input trước đó).

Phần này do giá trị trong biến A đang ở kiểu dữ liệu `num` nên ta có thể tiện sử dụng calc_func và verify_eq.

```rsc
lbl get_input
    xr0 = adr(lay_du), var_b        # tạm thời cho biến B lưu output

...
lbl lay_du
    'A mod 2 _'
```

Ok tạm thời tôi đã lấy được phần dư. Nên sau đó tôi sẽ cho so sánh với số 0 hoặc 1 để đưa ra nhận định cuối nên qua bước tiếp theo =)).

## P2: Xử lý xem đó là số chẵn hay số lẻ

```rsc
lbl check_num
    xr0 = var_b, var_c      # lưu ý cần đảm bảo biến C = 0
    verify_eq
    /*
        Sau dòng này ta sẽ có output là:
        - Nếu đúng thì:
            er0 = hex 00 01
            er2 = hex 01 00
        - Ngược lại
            er0 = er2 = hex 00 00
    */
    er0 = er2,rt
    er2 = adr(table_check)
    er0+=er0,er2+=er0,er0=[er2]
    er14 = er0, pop xr0
    hex 00 00 00 00
    sp = er14, pop er14

lbl is_odd
    xr0 = hex 01 01, adr(text_odd)
    line_print
    render.ddd4
    brk

lbl is_even
    xr0 = hex 01 01, adr(text_even)
    line_print
    render.ddd4
    brk

lbl table_check
    adr(is_odd, -2)
    adr(is_even, -2)

lbl text_even
    "Even"
    hex 00 00

lbl text_odd
    "Odd"
    hex 00
```

## P3: Kết thúc chương trình

Ok sau khi chạy xong thì nó đã thực hiện brk luôn rồi nên đoạn này ta sẽ vào viết launcher.
Trong chương trình này vì chúng ta không thực hiện loop hay gì hết nên ta có thể trực tiếp nhảy SP mà không cần backup
```rsc
@section.launcher at 0xd180

hex fd 24 30 30
er14 = adr(init, -2)
sp = er14, pop er14
```

---

Sau khi xong các điều này code hoàn chỉnh của bạn sẽ là:
```rsc
@section.main at 0xe9e0

lbl init
    setlr_pc
    setsfr
    di,rt
    buffer_clear

lbl get_input
    xr0 = adr(lay_du), var_b        # tạm thời cho biến B lưu output

lbl check_num
    xr0 = var_b, var_c      # lưu ý cần đảm bảo biến C = 0
    verify_eq
    /*
        Sau dòng này ta sẽ có output là:
        - Nếu đúng thì:
            er0 = hex 00 01
            er2 = hex 01 00
        - Ngược lại
            er0 = er2 = hex 00 00
    */
    er0 = er2,rt
    er2 = adr(table_check)
    er0+=er0,er2+=er0,er0=[er2]
    er14 = er0, pop xr0
    hex 00 00 00 00
    sp = er14, pop er14

lbl is_odd
    xr0 = hex 01 01, adr(text_odd)
    line_print
    render.ddd4
    brk

lbl is_even
    xr0 = hex 01 01, adr(text_even)
    line_print
    render.ddd4
    brk

lbl table_check
    adr(is_odd, -2)
    adr(is_even, -2)

lbl text_even
    "Even"
    hex 00 00

lbl text_odd
    "Odd"
    hex 00

lbl lay_du
    'A mod 2 _'

@section.launcher at 0xd180

hex fd 24 30 30
er14 = adr(init, -2)
sp = er14, pop er14
```

Đây là 1 đoạn code khá ngắn nôm na nó thực hiện chia lấy dư A với 2 để lấy phần dư (1 hoặc 0) đưa vào biến B. Sau đó so sánh với 0 nếu B == 0 thì output `Even` ngược lại là `Odd`, rồi sau đó dừng bằng brk.

Vậy tèn ten bạn đã chính thức viết ra được 1 chương trình kiểm tra xem số đó là số chẵn hay lẻ siêu dễ rồi =)). Nhưng chương trình trên yêu cầu rằng biến A = input còn biến C = 0 nên khá hạn chế.

## 2. Bài toán lập trình cơ bản 2
**Đề bài:** Cho vào 2 số nguyên dương A, B (1 <= A, B <= 100) thực hiện phép cộng và in ra kết quả.

Ví dụ:
```
| Input | Output |
+-------+--------+
| A=5,B=10 | 15     |
+-------+--------+
| A=2,B=3 | 5      |
```

---

Bro tôi biếng giải quá để lúc khác nhé...

---

[<- Quay lại](A_CachVietLauncher.md)