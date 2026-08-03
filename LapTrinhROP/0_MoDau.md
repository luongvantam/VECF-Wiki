# Lập trình ROP trên Casio ClassWiz (fx-580VN X, fx-880BTG, ...)

## 1. Mở đầu

Máy tính cầm tay là một phần không thể thiếu đối với học sinh, sinh viên, đặc biệt là các dòng máy như **Casio fx-580VN X**. Đây là chiếc máy tính quen thuộc với rất nhiều sĩ tử. Tuy nhiên, ít ai biết rằng những chiếc máy tính mà bạn cầm trên tay vào mỗi tiết toán này lại có thể chạy các chương trình do người dùng tạo ra, mặc dù Casio không hề cung cấp tính năng lập trình.

Các dòng **Casio ClassWiz** (fx-580VN X, fx-880BTG, ...) sử dụng vi xử lý [**NX-U8/100**](../nX-U8100_Core_Instruction_Manual.pdf). Về cơ bản, đây là một vi xử lý có kiến trúc 8-bit với tập lệnh (Instruction Set Architecture - ISA) riêng. Máy không cung cấp giao diện lập trình hay khả năng nạp chương trình từ người dùng, vì vậy về mặt chính thức, đây chỉ là những máy tính khoa học thông thường.

---

## 2. Lập trình Casio bằng ROP

Do đó, nếu thực sự muốn viết chương trình trên thiết bị này, chúng ta phải sử dụng một kỹ thuật đặc biệt thường gặp trong lĩnh vực kỹ thuật dịch ngược (Reverse Engineering) gọi là **Return-Oriented Programming (ROP)**.

Nguyên lý cốt lõi của ROP là khai thác các lỗ hổng trong chính phần mềm của máy để chiếm quyền điều khiển thanh ghi **PC** (Program Counter - nơi lưu trữ địa chỉ của lệnh tiếp theo sẽ được thực thi). Bằng cách này, chúng ta buộc CPU nhảy đến một địa chỉ mã lệnh có sẵn trong firmware, đồng thời sửa đổi không gian **ngăn xếp (Stack)** sao cho khi đoạn mã đó kết thúc (return), CPU sẽ tiếp tục nhảy đến một địa chỉ khác mà chúng ta đã chuẩn bị từ trước.

Những đoạn lệnh ngắn có sẵn được tận dụng này gọi là các **gadget**. Nếu sắp xếp dữ liệu trên ngăn xếp một cách hợp lý, chúng ta có thể khiến CPU nhảy liên tiếp qua nhiều gadget khác nhau, tạo thành một chuỗi gọi là **chuỗi ROP (ROP Chain)**. Nhờ đó, dù mỗi gadget chỉ làm một nhiệm vụ nhỏ, sự kết hợp của chúng lại giúp ta đạt được hiệu quả lập trình một cách hoàn chỉnh.

Nói cách khác, thay vì viết mã mới, việc lập trình ROP trên Casio ClassWiz xoay quanh quá trình phân tích firmware, tìm kiếm các gadget phù hợp và "lắp ghép" chúng lại với nhau. Quá trình này biến một chiếc máy tính khoa học thông thường thành một cỗ máy có thể thực thi bất kỳ chương trình nào do cộng đồng phát triển.

Trong các chương tiếp theo, chúng ta sẽ đi sâu vào việc tìm hiểu các khái niệm cơ bản (Thanh ghi, Ngăn xếp, Cơ chế Call/Return), cách tìm kiếm gadget và từng bước xây dựng ROP Chain trên nền tảng nX-U8/100.

***Bạn nên tự tìm hiểu về vi xử lý [NX-U8/100](../nX-U8100_Core_Instruction_Manual.pdf) trước khi đọc tài liệu này.***

---

[Tiếp theo ->](1_ROPLaGi.md)
