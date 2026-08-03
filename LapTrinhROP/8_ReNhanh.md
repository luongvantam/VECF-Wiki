# Rẽ nhánh có điều kiện (If/Else) trong ROP

Ở chương 7, chúng ta đã biết cách bẻ lái luồng thực thi (Jump) bằng cách can thiệp trực tiếp vào thanh ghi SP. Tuy nhiên, thao tác ném SP đến một địa chỉ cố định đó là một bước nhảy vô điều kiện (Unconditional Jump) — cứ chạy đến đó là nhảy.

Một chương trình thực tế thì không thể cứng nhắc như vậy. Chúng ta cần chương trình biết đưa ra quyết định: *"Nếu điều kiện này ĐÚNG thì nhảy tới đoạn mã A, nếu SAI thì nhảy tới đoạn mã B"*.

Trong lập trình hợp ngữ thông thường, việc này rất dễ dàng. CPU có sẵn các lệnh nhảy có điều kiện như BEQ (Nhảy nếu bằng nhau) hoặc BGT (Nhảy nếu lớn hơn).

Nhưng trong thế giới của ROP, CPU bị "mù". Mọi sự di chuyển của chương trình (PC) đều bị trói buộc vào dữ liệu nằm trên Stack thông qua lệnh POP PC. Lệnh POP thì không hề biết so sánh!

> Vậy làm sao để ROP có thể "rẽ nhánh"?
> 
> Câu trả lời là: **Nếu không thể dùng lệnh điều kiện để bẻ lái PC, chúng ta sẽ tự tính toán ra địa chỉ đích bằng toán học, sau đó bẻ lái SP tới địa chỉ vừa tính được!**

## **Ý tưởng cốt lõi: Biến Logic thành Số học**

Mọi kỹ thuật rẽ nhánh trong ROP, dù được viết bằng phương pháp nào, đều tuân theo một quy trình 3 bước cốt lõi. Hãy ghi nhớ thật kỹ quy trình này:

**Bước 1: Thực hiện So sánh (CMP)**

Chúng ta sẽ tìm và gọi một gadget có khả năng thực hiện phép trừ hoặc so sánh hai thanh ghi (ví dụ: ER0 và ER2).

**Bước 2: Số hóa kết quả (Tạo ra 0 hoặc 1)**

Ngay sau khi so sánh, gadget này phải trả về một kết quả rõ ràng:

* Nếu điều kiện **ĐÚNG**, nó gán một thanh ghi (ví dụ R0) bằng 1.  
* Nếu điều kiện **SAI**, nó gán thanh ghi đó bằng 0.  
  *(Hoặc ngược lại, tùy gadget).*

Lúc này, khái niệm trừu tượng "Đúng/Sai" đã bị ép trở thành một con số vật lý rõ ràng là 1 hoặc 0 nằm trong thanh ghi.

**Bước 3: Bẻ lái thanh ghi SP**

Đây là bước quyết định. Chúng ta sẽ lấy con số 0 hoặc 1 vừa tạo ra để tính toán ra **địa chỉ của nhánh cần nhảy tới**. Sau khi có địa chỉ, ta nạp nó vào ER14 và dùng kỹ thuật MOV SP, ER14 (đã học ở Chương 7) để ép Stack trượt tới đúng nhánh đó.

```text
 [So sánh A và B] ---> [Kết quả: 0 hoặc 1] ---> [Tính địa chỉ dựa trên 0/1] ---> [Kéo SP tới địa chỉ]
```

Để hiểu rõ hơn cách con số 0 và 1 này được dùng để tính địa chỉ như thế nào, chúng ta hãy cùng xem xét hai **ví dụ minh họa** được viết bằng ngôn ngữ RSC.

## **Ví dụ 1: Sử dụng "0 hoặc 1" làm Chỉ mục tra cứu (Table Lookup)**

Cách đơn giản nhất để dùng số 0 hoặc 1 là coi nó như số thứ tự (index) để tra cứu một cái Bảng (Table).

Đoạn code RSC sau thực hiện rẽ nhánh: *"Nếu ER0 > ER2 thì TRUE, ngược lại FALSE"*.

```rsc
lbl main  
    # ... (Khởi tạo ER0 và ER2) ...  
      
    # BƯỚC 1 & 2: So sánh và tạo ra số 0 hoặc 1  
    # Gadget này kiểm tra: Nếu ER0 > ER2, gán R0 = 0. Ngược lại gán R0 = 1.
    er0 - er2_gt,r0 = 0 |r0 = 1,rt        
    r1 = 0,rt

    # BƯỚC 3: Dùng số 0/1 để tính địa chỉ đích và bẻ lái SP  
    er2 = adr(table)     # Nạp địa chỉ của Bảng (table) vào ER2  
      
    # Gadget load_table: Lấy phần tử thứ [ER0] trong bảng  
    er0 += er0,er2 += er0,er0 = [er2],rt  
      
    er14 = er0, pop xr0  # Lấy địa chỉ tra cứu được nạp vào ER14  
    hex 30 30 30 30  
    sp = er14,pop er14   # KÉO SP tới nhánh được chọn!  
    hex 00 00

# ----- CÁC NHÁNH ĐÍCH -----  
lbl true  
    # ... code của nhánh True ...

lbl false  
    # ... code của nhánh False ...

# ----- BẢNG TRA CỨU -----  
lbl table  
    eval(adr(true) - 0x2)   # Vị trí số 0 (Nếu điều kiện Đúng)  
    eval(adr(false) - 0x2)  # Vị trí số 1 (Nếu điều kiện Sai)
```

**Nguyên lý hoạt động của chuỗi load_table:**

Bản chất của việc tra bảng là một chuỗi phép tính trên một dòng gadget `er0 += er0,er2 += er0,er0 = [er2],rt`:

* Lúc này thanh ghi ER0 đang là số 0 (nếu đúng) hoặc 1 (nếu sai).  
* Lệnh `er0 += er0` chính là phép nhân đôi. Nếu ER0 là 1, nó thành 2. Tại sao phải nhân đôi? Vì mỗi địa chỉ bộ nhớ (như địa chỉ của lbl true) chiếm **2 byte (16-bit)**.  
* Lệnh `er2 += er0` lấy địa chỉ gốc của Bảng cộng với khoảng cách (0 hoặc 2 byte) để trỏ chính xác vào dòng số 0 hoặc dòng số 1 của Bảng.  
* Lệnh `er0 = [er2]` đọc địa chỉ đã lưu trong Bảng ra để sử dụng.  
  *(Lưu ý: Các địa chỉ trong Bảng bị trừ đi 0x2 để bù trừ cho lệnh POP ER14 ở cuối gadget nhảy, một kỹ thuật căn chỉnh Stack quen thuộc từ Chương 5).*

## **Ví dụ 2: Sử dụng "0 hoặc 1" để làm Phép nhân (Toán học/Offset)**

Phương pháp tra cứu Bảng ở trên rất chuẩn xác, nhưng tốn bộ nhớ để lưu cái Bảng. Thay vì tra bảng, chúng ta có thể dùng toán học trực tiếp!

Bất cứ số nào nhân với 0 cũng bằng 0. Số nào nhân với 1 thì giữ nguyên. Khai thác tính chất này, đoạn code sau rẽ nhánh: *"Nếu ER0 == ER2 thì TRUE, ngược lại FALSE"*.

```rsc
# ... (Khởi tạo ER0 và ER2) ...  
    
# BƯỚC 1 & 2: So sánh và tạo ra số 0 hoặc 1  
# Gadget này kiểm tra: Nếu BẰNG nhau, R0 = 1. Ngược lại R0 = 0.  
er0 - er2_eq,r0 = 1|r0 = 0,rt    
    
# BƯỚC 3: Tính toán địa chỉ và bẻ lái SP  
er2 = eval(adr(true) - adr(false))  # Tính "Khoảng cách" (số byte) giữa 2 nhánh  
    
# Thực hiện phép tính: Địa chỉ đích = (Kết quả logic * Khoảng cách) + Địa chỉ gốc  
er0 *= r2,er2 = er0,er0 += er4,rt  
    
er14 = er0, pop xr0  
hex 00 00 00 00  
sp = er14, pop er14  # KÉO SP tới nhánh vừa tính ra!
```

**Nguyên lý:**

1. Đầu tiên, chúng ta tính ra "Khoảng cách" (số byte) từ nhánh False đến nhánh True và nạp vào thanh ghi R2 (ER2).  
2. Giả sử thanh ghi ER4 đang lưu "Địa chỉ gốc" (địa chỉ nhánh False).  
3. Lệnh `er0 *= r2` lấy con số logic (0 hoặc 1) nhân với Khoảng cách.  
   * Nếu Sai (0): ER0 = 0 * Khoảng cách = 0.  
   * Nếu Đúng (1): ER0 = 1 * Khoảng cách = Khoảng cách.  
4. Lệnh `er0 += er4` cộng kết quả trên vào Địa chỉ gốc.  
   * Kết quả sai: 0 + Địa chỉ gốc = Địa chỉ nhánh False.  
   * Kết quả đúng: Khoảng cách + Địa chỉ gốc = Địa chỉ nhánh True.

Kết quả tính toán cuối cùng vẫn tự động sinh ra được địa chỉ chính xác. Chúng ta lại nạp nó vào SP để bẻ lái luồng thực thi!

---

Dù bạn viết mã rẽ nhánh bằng cách nào (tra bảng hay tính toán trực tiếp), hãy luôn nhớ tới trình tự 3 bước:

1. **Tìm gadget So sánh (CMP)**.  
2. **Chuyển kết quả ĐÚNG/SAI thành con số 1/0**.  
3. **Dùng số 1/0 đó làm dữ liệu đầu vào (để index mảng hoặc nhân chia cộng trừ) nhằm suy ra địa chỉ đích, rồi kéo thanh ghi SP đến đó.**

Khi kết hợp được **Gọi hàm (Chương 6)**, **Vòng lặp (Chương 7)** và **Rẽ nhánh (Chương 8)**, Stack của chúng ta không còn là một đường thẳng cứng nhắc nữa. Chương trình ROP giờ đây có thể tự do nhảy múa, lặp lại và đưa ra quyết định hệt như một phần mềm được lập trình bằng ngôn ngữ bậc cao!

Đến đây, bạn đã nắm vững toàn bộ những kỹ thuật điều khiển luồng (Control Flow) phức tạp nhất của Return-Oriented Programming.

---

[<- Quay lại](7_JumpSP.md) | [Tiếp theo ->](9_Loops.md)
