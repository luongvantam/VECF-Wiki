# Bẻ lái luồng thực thi - Nhảy (Jump) bằng thanh ghi SP

Ở các chương trước, chúng ta đã biết một chuỗi ROP luôn thực thi theo một hướng duy nhất từ trên xuống dưới.

Tại sao lại như vậy? Hãy cùng nhìn kỹ hơn vào cách thanh ghi **SP (Stack Pointer)** hoạt động tự nhiên.

## **Sự "trượt xuống" tự nhiên của SP**

Mỗi khi CPU gặp một lệnh POP (chẳng hạn như POP PC hoặc POP ER0), nó sẽ lấy dữ liệu tại vị trí mà SP đang trỏ tới. Ngay sau khi lấy xong, **SP sẽ tự động trượt xuống** vùng nhớ tiếp theo (tăng địa chỉ lên) để chuẩn bị cho lệnh POP kế tiếp.

Hãy hình dung quá trình này qua sơ đồ:

```text
Ban đầu:

 SP -> <Gadget 1>  
       <Gadget 2>  
       <Gadget 3>  
       <BOTTOM>
```

Sau khi Gadget 1 kết thúc bằng POP PC, SP đã tự động trượt xuống:

```text
       <Gadget 1> (đã thực thi)  
 SP -> <Gadget 2>  
       <Gadget 3>  
       <BOTTOM>
```

Sự "trượt xuống" đều đặn này chính là lực đẩy giúp chuỗi ROP của chúng ta chạy tuần tự hết lệnh này đến lệnh khác.

Tuy nhiên, trong thực tế chúng ta không chỉ muốn chương trình chạy một mạch rồi dừng. Chúng ta thường cần:

* **Bỏ qua (skip)** một đoạn mã (Nhảy tiến).  
* **Nhảy hẳn sang một vùng nhớ khác** (Chuyển Stack).  
* **Quay ngược lại** để lặp lại một công việc (Nhảy lùi / Vòng lặp).

Nếu chỉ phụ thuộc vào sự trượt xuống tự nhiên của SP, chúng ta không thể làm được những điều này.

## **Ý tưởng cốt lõi: Tại sao đổi SP lại là "Nhảy"?**

Trong lập trình thông thường, nếu bạn muốn chương trình nhảy đi đâu đó, bạn sẽ dùng lệnh tác động trực tiếp lên thanh ghi **PC** (Program Counter).

Nhưng trong ROP, PC hoàn toàn bị "mù". Nó không tự quyết định được sẽ đi đâu, mà nó **buộc phải đọc địa chỉ từ bất cứ nơi nào mà SP đang trỏ tới**.

**Vì PC phụ thuộc hoàn toàn vào SP, ý tưởng để tạo ra một cú "Nhảy" vô cùng đơn giản:**

Bạn không cần bẻ lái PC, bạn chỉ cần **"nhấc bổng" thanh ghi SP và đặt nó vào đúng tọa độ mong muốn**.

* Nếu ném SP **xuống dưới** xa hơn: Bạn tạo ra một bước nhảy tiến để bỏ qua code.  
* Nếu ném SP **ra một vùng nhớ hoàn toàn khác**: Bạn chuyển chuỗi ROP sang nơi khác thực thi.  
* Nếu ném SP **ngược lên trên**: Bạn tạo ra một cú nhảy lùi (Tiền đề của Vòng lặp).

## **Gadget điều hướng SP**

Để ép SP đi nơi khác, chúng ta cần tìm các gadget có khả năng ghi đè giá trị vào SP.

Trên firmware của dòng nX-U8/100, chúng ta **có thể sử dụng** các gadget chứa lệnh `MOV SP, ERn` Ví dụ, chúng ta có một gadget rất tiện lợi nằm tại địa chỉ `0x20d60` mang chức năng: `sp = er14, pop er14`.

Bản chất mã máy (assembly) của gadget này gồm 3 lệnh:

```asm
MOV SP, ER14  
POP ER14  
POP PC
```

Gadget này thực hiện ba bước vô cùng lợi hại:

1. `MOV SP, ER14`: Lấy tọa độ đích (đã được bạn tính toán sẵn và nạp vào ER14 từ trước) chép thẳng vào SP. Ngay lập tức, con trỏ Stack bay đến tọa độ mới!  
2. `POP ER14`: SP hiện tại đang ở tọa độ mới. Lệnh này lấy một giá trị đệm (padding) tại đó ra ngoài, làm cho SP trượt xuống thêm 2 byte cho đúng vị trí chuẩn.  
3. `POP PC`: Đọc địa chỉ kế tiếp tại vùng Stack mới để CPU tiếp tục thực thi. Cú nhảy hoàn tất!

## **Cách xây dựng Stack để thực hiện cú Nhảy**

Giả sử đoạn gadget bạn muốn nhảy tới bắt đầu tại địa chỉ **A**. Bạn cần chuẩn bị Stack của mình như sau:

```text
<TOP>

<Địa chỉ A - 2> : [Dữ liệu đệm 2-byte]   <--- Nơi SP sẽ đáp xuống đầu tiên  
<Địa chỉ A>     : <Gadget Đích>          <--- ĐÍCH ĐẾN CỦA CÚ NHẢY  
                  ...

<Vị trí hiện tại của ROP Chain>  
<Địa chỉ Gadget: POP ER14; POP PC>  
[Giá trị A - 2]                          <--- Dữ liệu nạp vào ER14  
<Địa chỉ 0x20d60>                        <--- Gọi gadget: sp = er14, pop er14

<BOTTOM>
```

**Quá trình thực thi:**

1. CPU chạy đến `POP ER14`, nó lấy giá trị `A - 2` bỏ vào thanh ghi ER14.  
2. CPU chạy vào `0x20d60` (`MOV SP, ER14`). Ngay lập tức, SP dịch chuyển tức thời đến tọa độ `A - 2`.  
3. Gadget tiếp tục chạy `POP ER14`. Dữ liệu đệm bị lấy ra, SP tự động trượt xuống đúng địa chỉ A.  
4. Cuối cùng, lệnh `POP PC` ăn chính xác `<Gadget Đích>`. Luồng chương trình đã được bẻ lái thành công!

## **Cách viết bằng ngôn ngữ RSC**

Thay vì phải tự tính toán địa chỉ và đệm bằng tay như sơ đồ Stack ở trên, RAC Compiler cho phép chúng ta viết cú nhảy một cách vô cùng ngắn gọn và trực quan giống như các ví dụ ở các chương sau:

**Ví dụ 1: Sử dụng ER14 (Phổ biến nhất)**

```rsc
    # Tính toán địa chỉ đích (trừ đi 0x2 để bù trừ cho lệnh POP ER14) 
    # và nạp vào thanh ghi ER14
    er14 = eval(adr(<label>) - 0x2)
    
    # Gọi gadget bẻ lái SP
    sp = er14, pop er14
```

**Ví dụ 2: Sử dụng các thanh ghi khác**

Trong trường hợp thanh ghi ER14 đang bận chứa dữ liệu khác, bạn hoàn toàn có thể sử dụng các thanh ghi khác nếu tìm được gadget tương ứng trên ROM. Ví dụ, nếu bạn tìm được gadget `sp = er6, pop er8`:

```rsc
    # Sử dụng thanh ghi ER6 thay cho ER14
    er6 = eval(adr(<label>) - 0x2)
    
    # Kéo SP tới địa chỉ mới
    sp = er6, pop er8
```

## **Lưu ý quan trọng: Nhảy ngược (Vòng lặp) và sự tàn phá Stack**

Như đã nói ở trên, nếu bạn ném SP ngược lên trên (về những địa chỉ cũ), bạn sẽ tạo ra một **Vòng lặp (Loop)**. Về mặt lý thuyết, điều này hoạt động hoàn hảo.

Nhưng trong thực tế của ROP, đây là một con dao hai lưỡi.

Khi các gadget hoặc các hàm ROM của bạn chạy, chúng thường sử dụng các lệnh PUSH để lưu biến tạm. Quá trình này sẽ **ghi đè và làm hỏng** những dữ liệu ROP nằm bên dưới nó. Nếu bạn chỉ đơn thuần kéo SP lùi lại để lặp, ở chu kỳ thứ hai, CPU sẽ đọc phải toàn bộ dữ liệu rác đã bị ghi đè, dẫn đến Crash máy.

Do đó, khái niệm "Nhảy" bằng SP là công cụ cốt lõi để điều hướng. Nhưng để ứng dụng nó vào việc tạo Vòng lặp an toàn, chúng ta bắt buộc phải có cơ chế **Sao lưu (Backup)** và **Khôi phục (Restore)** bộ nhớ.

Vấn đề phức tạp này sẽ được chúng ta mổ xẻ và giải quyết triệt để bằng kỹ thuật memcpy ở **Chương 10**.

Còn bây giờ, ở chương tiếp theo, chúng ta sẽ kết hợp sự linh hoạt của thao tác SP này với các phép tính logic để tạo ra khả năng **Rẽ nhánh có điều kiện (If/Else)**!

---

[<- Quay lại](6_GoiHamVaMidFunctionJump.md) | [Tiếp theo ->](8_ReNhanh.md)
