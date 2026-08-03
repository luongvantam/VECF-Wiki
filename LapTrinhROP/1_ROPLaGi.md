# ROP là gì? (lý thuyết)

## Return-Oriented Programming là gì?

**Return-Oriented Programming (ROP)** là một kỹ thuật điều khiển luồng thực thi của chương trình bằng cách tận dụng những đoạn mã đã tồn tại sẵn trong firmware hoặc chương trình.

Thông thường, khi muốn bổ sung một chức năng mới, lập trình viên sẽ viết thêm mã máy (Machine Code) để CPU thực thi. Tuy nhiên, trong nhiều trường hợp, việc chèn thêm mã máy là không thể hoặc bị giới hạn.

Thay vì tạo ra mã máy mới, ROP sử dụng những đoạn mã đã có sẵn trong firmware, sau đó sắp xếp chúng theo một thứ tự nhất định để tạo thành chức năng mong muốn.

Những đoạn mã ngắn này được gọi là **gadget**.

Mỗi gadget thường chỉ thực hiện một hoặc một vài thao tác đơn giản, chẳng hạn như:

* Nạp dữ liệu vào một thanh ghi.
* Thực hiện phép toán.
* Đọc hoặc ghi dữ liệu vào bộ nhớ.
* Chuyển quyền điều khiển sang gadget tiếp theo.

Bản thân một gadget thường không đủ để thực hiện một công việc phức tạp. Tuy nhiên, bằng cách kết hợp nhiều gadget theo đúng thứ tự, chúng ta có thể tạo thành một chương trình hoàn chỉnh.

Chuỗi các gadget được sắp xếp để thực hiện một nhiệm vụ cụ thể được gọi là **ROP Chain (Return-Oriented Programming Chain)**.

---

## Vì sao lại dùng ROP?

Trong các bộ vi xử lý thuộc dòng **nX-U8/100**, firmware đã chứa hàng nghìn lệnh máy phục vụ cho hệ điều hành và các chức năng của máy tính.

Điều này đồng nghĩa với việc trong firmware cũng tồn tại rất nhiều đoạn mã ngắn có thể được tận dụng.

Nếu có thể điều khiển CPU thực thi các đoạn mã này theo đúng thứ tự mong muốn, chúng ta có thể xây dựng những chức năng hoàn toàn mới mà **không cần chèn thêm bất kỳ mã máy nào vào firmware**.

Đây chính là ý tưởng cốt lõi của Return-Oriented Programming.

---

## ROP hoạt động như thế nào?

Thoạt nhìn, điều này có vẻ khá khó hiểu.

Làm thế nào CPU có thể thực thi đúng những đoạn mã mà chúng ta mong muốn chỉ bằng cách thay đổi một vài giá trị trong bộ nhớ?

Câu trả lời nằm ở cơ chế gọi và trả về của hàm.

Mỗi khi một hàm được gọi, CPU cần ghi nhớ vị trí để sau khi hàm kết thúc có thể quay trở lại và tiếp tục thực thi chương trình.

Quá trình này liên quan đến ba thành phần quan trọng:

* **Registers (Thanh ghi)**
* **Stack (Ngăn xếp)**
* **Cơ chế gọi và trả về của hàm (Call / Return)**

Hiểu được cách ba thành phần này hoạt động chính là chìa khóa để hiểu Return-Oriented Programming.

---

## Những gì bạn sẽ học

Trong các chương tiếp theo, chúng ta sẽ lần lượt tìm hiểu:

1. **Registers (Thanh ghi)** – CPU lưu trữ và sử dụng dữ liệu như thế nào.
2. **Stack (Ngăn xếp)** – Cách chương trình lưu dữ liệu tạm thời và địa chỉ trả về.
3. **Call / Return** – Cơ chế gọi và kết thúc một hàm.
4. **Gadget** – Những đoạn mã ngắn tạo nên nền tảng của ROP.
5. **ROP Chain** – Cách kết hợp nhiều gadget để tạo thành một chương trình hoàn chỉnh.

Đến cuối tài liệu, bạn sẽ hiểu cách một chương trình ROP được xây dựng và hoạt động trên kiến trúc **nX-U8/100**.

---

> **Lưu ý:** Tài liệu này không yêu cầu bạn phải có kiến thức chuyên sâu về kiến trúc máy tính hay an toàn thông tin. Mọi khái niệm cần thiết sẽ được giới thiệu từ những phần cơ bản trước khi áp dụng vào Return-Oriented Programming.

---

[<- Quay lại](0_MoDau.md) | [Tiếp theo ->](2_Registers.md)
