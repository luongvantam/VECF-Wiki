# Cách viết Launcher

Trải qua các chương trước, bạn đã nắm được cách xây dựng một chương trình ROP hoàn thiện: từ việc gọi hàm, rẽ nhánh, cho đến tạo vòng lặp an toàn. Thế nhưng, một chương trình ROP dù hoàn hảo đến đâu, nếu chỉ nằm im trong bộ nhớ thì cũng hoàn toàn vô dụng. 

Câu hỏi lớn nhất lúc này là: *"Làm thế nào để 'kích nổ' chuỗi ROP đó? Làm sao để bắt CPU ngừng công việc hiện tại và bắt đầu chạy đoạn mã của chúng ta?"*

Đó chính là nhiệm vụ của **Launcher**. Bài viết này sẽ giúp bạn hiểu rõ bản chất và cách tự tay viết một Launcher chuẩn mực!

---

## Ý tưởng cốt lõi của Launcher

Launcher không phải là phần thân của chương trình ROP, mà nó đóng vai trò là **ngòi nổ**. Bạn có thể hiểu Launcher là một đoạn ROP rất ngắn, làm nhiệm vụ thiết lập môi trường hoàn hảo nhất trước khi chuyển giao quyền điều khiển cho chương trình chính.

Mục đích của Launcher xoay quanh 2 nhiệm vụ tối quan trọng:

1. **Sao chép và Bảo vệ (Backup & Restore):** 
   Như chúng ta đã phân tích ở Chương 9, Stack là một môi trường rất khắc nghiệt. Nếu chạy ROP trực tiếp trên bản gốc (Src), chương trình sẽ bị phá hủy ngay lập tức bởi các hàm của ROM. 
   Do đó, nhiệm vụ đầu tiên của Launcher là lấy bản ROP gốc đang được cất giữ ở vùng lưu trữ an toàn (Src, ví dụ `0xE9E0`), chép đè nó ra một "bãi thử nghiệm" trên Stack (Dest, ví dụ `0xD730`).

2. **Bẻ lái luồng thực thi (Hijack SP):** 
   Sau khi đã chuẩn bị xong xuôi bản sao tại vùng Dest, Launcher sẽ thực hiện một cú nhảy (Jump) cuối cùng: Thay đổi thanh ghi Stack Pointer (SP) để ép nó trỏ thẳng vào đầu vùng Dest. Kể từ khoảnh khắc lệnh `POP PC` tiếp theo được chạy, CPU chính thức bước vào chương trình chính của bạn.

Có thể tóm tắt vòng đời khởi động của một chương trình ROP như sau:
`Khai thác lỗ hổng (Crash) -> Kích hoạt Launcher -> Copy mã từ Src sang Dest -> Ép SP nhảy sang Dest -> Chương trình chính bắt đầu chạy!`

Ngoài ra, bạn còn có thể thực hiện các thao tác khởi tạo chương trình ngay từ Launcher. Ví dụ như khôi phục thanh ghi trạng thái (SFR), thanh ghi liên kết (LR), hoặc chỉnh sửa các cờ dữ liệu trong RAM (chỉnh sửa kích thước font, điều chỉnh mode, ...) tùy theo mong muốn trước khi ROP chain chính thức bắt đầu.

---

## Cách viết Launcher bằng RAC Compiler

Với những mục đích trên, cấu trúc của một Launcher viết bằng RAC Compiler (RSC) thường sẽ chứa các đoạn mã khởi tạo, chuẩn bị tham số cho hàm `memcpy` (để copy từ Src sang Dest), và cuối cùng là thao tác ghi đè thanh ghi SP.

Dưới đây là một ví dụ minh họa về mã RSC của một Launcher tiêu chuẩn:

```rsc
hex FD 24 30 30             # khai thác lỗ hổng để SP bắt buộc phải điều hướng qua dòng tiếp theo
setlr_pc
setsfr
di, rt
pop xr4, pop xr12
0xd730
pr_length
0xe9e0
0xd724
memcpy_auto_jump
```

Trong ví dụ trên, chúng ta có thể phân tích Launcher thành 3 bước hoạt động như sau:

**1. Khai thác lỗ hổng (Crash) và Khởi tạo**
* `hex FD 24 30 30`: Đây là payload lỗi ép CPU trượt thanh ghi SP xuống dòng tiếp theo để bắt đầu đọc ROP chain. Tùy vào lỗi bạn khai thác mà giá trị này có thể khác nhau.
* `setlr_pc`, `setsfr`, `di, rt`: Đây là các gadget khởi tạo môi trường cơ bản (khôi phục thanh ghi LR, SFR, và vô hiệu hóa ngắt `di` để chương trình không bị gián đoạn).

**2. Chuẩn bị tham số cho memcpy_auto_jump**
Như đã nhắc đến ở Chương 9, `memcpy_auto_jump` là một gadget tối ưu 2-trong-1: vừa copy dữ liệu, vừa tự động nhảy. Cú pháp truyền tham số chuẩn của gadget này yêu cầu:
* **ER4 = dest**: Địa chỉ đích trên Stack (`0xd730`).
* **ER6 = len**: Kích thước chuỗi ROP (được lấy tự động qua biến `pr_length`).
* **ER12 = src**: Địa chỉ nguồn chứa bản gốc an toàn (`0xe9e0`).
* **ER14 = addr_jump**: Địa chỉ nhảy tới sau khi copy (`0xd724`, tương đương `adr(main) - 0xc`).

Lệnh `pop xr4, pop xr12` sẽ thực hiện "nuốt" 4 dòng giá trị bên dưới nó trên Stack và nạp chính xác vào 4 vị trí thanh ghi tương ứng.

**3. Kích nổ**
Lệnh `memcpy_auto_jump` được gọi. Nó sẽ lấy bản gốc từ `0xe9e0` chép đè lên `0xd730`, sau đó lập tức bẻ lái SP về `0xd724` và phát nổ chương trình chính!

Chỉ với vỏn vẹn chừng đó lệnh, Launcher của bạn đã làm xuất sắc nhiệm vụ là cây cầu nối đưa CPU vào thế giới Return-Oriented Programming một cách hoàn hảo và an toàn.

---

[<- Quay lại](9_Loops.md)
