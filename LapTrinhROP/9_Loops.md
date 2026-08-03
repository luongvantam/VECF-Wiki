# Sự tàn phá Stack và Ý tưởng Khôi phục bằng Memcpy

Ở các chương trước, chúng ta đã học cách tạo vòng lặp bằng cách kéo thanh ghi SP lùi về địa chỉ cũ. Về mặt lý thuyết, điều này rất hoàn hảo.

Nhưng trong thực tế, nếu bạn chỉ làm như vậy, chương trình của bạn khả năng cao sẽ **crash ngay ở vòng lặp thứ hai**. Tại sao? Vì bất cứ khi nào bạn gọi một hàm phức tạp trong ROM (như in ấn, đồ họa), hàm đó sẽ sử dụng Stack để lưu trữ tạm thời, vô tình **ghi đè nát bét** chuỗi ROP của bạn.

Trong chương này, chúng ta sẽ tìm hiểu ý tưởng cốt lõi để bảo vệ ROP Chain và 2 phương pháp lập trình phổ biến để hiện thực hóa nó.

## **Ý tưởng cốt lõi: Vùng Nguồn (Src) và Vùng Đích (Dest)**

Thay vì chỉ đặt chương trình lên Stack và hi vọng nó không bị phá hỏng, chúng ta áp dụng một chiến lược an toàn hơn: **Chạy bản sao, giữ lại bản gốc.**

Chúng ta sẽ chia bộ nhớ làm 2 khu vực:

1. **Vùng Đích (Dest - ví dụ 0xD730):** Vùng Stack đang chạy, nơi CPU thực thi ROP. Quá trình chạy và gọi hàm chắc chắn sẽ làm vùng này bị rác và hỏng hóc.  
2. **Vùng Nguồn (Src - ví dụ 0xE9E0):** Vùng lưu trữ an toàn chứa bản gốc của chuỗi ROP.  
   *(Lưu ý: 0xE9E0 là một vùng nhớ đặc biệt trên máy tính được giữ nguyên nội dung kể cả khi bạn reset máy. Việc chọn vùng nhớ này làm nơi Backup là một chiến lược vô cùng lý tưởng).*

**Mục đích của Vòng lặp lúc này là:** Ở cuối mỗi chu kỳ (khi Dest đã bị phá hỏng), chúng ta không nhảy về đầu Dest ngay. Thay vào đó, ta gọi hàm copy (memcpy) để chép một bản sao mới tinh từ Src (0xE9E0) đè lên Dest (0xD730). Sau khi copy xong xuôi, ta mới bẻ lái SP nhảy về đầu Dest để chạy lại.

Dưới đây là 2 hướng lập trình tiêu biểu bằng RSC để thực hiện ý tưởng này.

### **Hướng 1: Khôi phục bằng Chuỗi Gadget Thủ công**

Đây là cách viết cơ bản nhất.

*(Lưu ý: Trong RSC, nhãn lbl end thường được khai báo ở dòng cuối cùng của tệp để đánh dấu điểm kết thúc chương trình).*

```rsc
lbl restore  
    di,rt  
    xr0 = adr(length), hex 01 00  
    [er0]=er2,rt  
      
    # --- Nạp tham số cho vòng lặp ---  
    pop qr0  
    pr_length             # 1. Kích thước chuỗi ROP (Nạp vào ER0)  
    0xe9e0                # 2. Vùng gốc an toàn Src (Nạp vào ER2)  
    0xd730                # 3. Vùng Stack đang chạy Dest (Nạp vào ER4)  
    eval(adr(main) - 0x2) # 4. Địa chỉ nhảy về sau khi copy (Nạp vào ER6)  
      
    # --- Gọi hàm memcpy ---  
    hex 32 89             # gadget 18932 hay memcpy_indr

lbl length  
    eval(adr(end) - adr(length))  # Tính kích thước tự động  
    hex 00 00  
    sp = er6, pop er8
```

**Phân tích logic truyền tham số:**

Chương trình lần lượt "nuốt" 4 dòng dữ liệu tiếp theo trên Stack và phân bổ tương ứng:

* ER0 = Kích thước (length)  
* ER2 = Vùng Nguồn (0xe9e0)  
* ER4 = Vùng Đích (0xd730)  
* ER6 = Địa chỉ lặp eval(adr(main) - 0x2) *(Cách viết này đồng nghĩa với cú pháp rút gọn adr(main, -2) thường thấy).*

Sau khi các tham số đã nằm đúng vị trí, hàm memcpy (tại hex 32 89) sẽ được gọi để khôi phục dữ liệu, rồi gadget sp = er6 sẽ kéo Stack trượt về địa chỉ nhánh main.

### **Hướng 2: Sử dụng Gadget memcpy_auto_jump**

Hướng 1 hoạt động tốt nhưng khá dài. Trên firmware có một gadget cực kỳ tối ưu tên là memcpy_auto_jump. Gadget này tích hợp cả tính năng copy và tự động nhảy.

```rsc
# Nạp tham số vào XR4 (gồm ER4, ER6) và XR12 (gồm ER12, ER14)  
pop xr4, pop xr12  
0xd730                     # Dest (Nạp vào ER4)  
pr_length                  # Size (Nạp vào ER6)  
0xe9e0                     # Src  (Nạp vào ER12)  
eval(adr(program) - 0xc)   # Địa chỉ nhảy (Nạp vào ER14)  
    
# Gọi Gadget 2-trong-1  
memcpy_auto_jump
```

**Yêu cầu của Gadget này:**

Gadget memcpy_auto_jump yêu cầu chúng ta phải chuẩn bị sẵn 4 tham số ở đúng 4 vị trí:

1. ER4: Địa chỉ Đích (Dest)  
2. ER6: Kích thước cần chép (Size)  
3. ER12: Địa chỉ Nguồn (Src)  
4. ER14: Địa chỉ cần nhảy tới sau khi copy xong.

**Tại sao lại là - 0xc?**

Bạn có để ý khi thiết lập địa chỉ nhảy vào ER14, chúng ta phải trừ đi 0xC (12 byte) thay vì 0x2 như bình thường không?

Bản chất của memcpy_auto_jump là sau khi chép xong, nó không dùng POP PC thông thường mà nó rẽ nhánh vào một đoạn mã hệ thống tên là LEAVE.

Nếu bạn tò mò muốn biết tại sao hàm LEAVE lại ăn mất 12 byte của Stack, hãy mở công cụ Disassembler lên, tìm đọc mã máy của hàm LEAVE trong ROM và tự phân tích các lệnh POP của nó nhé! Đó là một bài tập rất thú vị để bạn luyện kỹ năng đọc hiểu Assembly.

## Tổng kết

1. **Ý tưởng cốt lõi của vòng lặp ROP thực tế:** Không bao giờ chạy trực tiếp trên bản gốc. Luôn lưu một bản gốc (Src) ở vùng nhớ bền vững, copy nó sang vùng chạy (Dest), và phục hồi lại (Restore) sau mỗi chu kỳ để chống lại sự tàn phá của các hàm ROM.  
2. Việc truyền số lượng lớn tham số có thể được tối ưu bằng các lệnh POP các thanh ghi cỡ lớn như POP QR0 hoặc POP XRn.  
3. Tùy thuộc vào việc bạn dùng gadget nào để nhảy vòng lặp, độ lệch bù trừ Stack (Offset) sẽ khác nhau (-0x2 hoặc -0xC). Hãy luôn kiểm tra disassembly của gadget nhảy để tính toán cho chuẩn.

---

[<- Quay lại](8_ReNhanh.md) | [Tiếp theo ->](A_CachVietLauncher.md)
