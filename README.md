# VECF Wiki
Chào mừng bạn đến với **VECF Wiki**.

Đây là nơi tổng hợp các tài liệu, hướng dẫn và bài viết về **máy tính Casio ClassWiz**, **Return-Oriented Programming (ROP)**, **RAC Compiler**, kỹ thuật **Spell**, cũng như các nghiên cứu và phân tích liên quan đến firmware nX-U8/100.

Mục tiêu của VECF Wiki là cung cấp một nguồn tài liệu đầy đủ từ kiến thức cơ bản đến nâng cao, giúp người mới có thể bắt đầu học, đồng thời cũng là nơi tham khảo dành cho những người đã có kinh nghiệm.


## Cú pháp cơ bản của RAC Compiler
- [0/ Cài đặt (Installation)](BasicSyntaxOfRAC/0_Installation)
- [1/ In chữ Hello World](BasicSyntaxOfRAC/1_PrintHelloWorld)
- [2.0/ Kiểu dữ liệu cơ bản (Data Types)](BasicSyntaxOfRAC/2p0_KieuDuLieu)
- [2.1/ Khai Báo Biến Ảo (Virtual Variables)](BasicSyntaxOfRAC/2p1_KhaiBaoBienAo)
- [2.2/ Khai Báo Thanh Ghi (Registers)](BasicSyntaxOfRAC/2p2_KhaiBaoThanhGhi)
- [2.3/ Khai báo Gadget (Gadgets)](BasicSyntaxOfRAC/2p3_KhaiBaoGadget)
- [3/ Nhãn & Lệnh Nhảy (Labels & Jumps)](BasicSyntaxOfRAC/3_Label)
- [4/ Phép toán Thời gian Biên dịch (Compile-time Evaluation)](BasicSyntaxOfRAC/4_TinhToan)
- [5/ Chỉ thị Vị trí Nạp (`org` & `backup`)](BasicSyntaxOfRAC/5_DatViTriProgram)
- [6/ Phân vùng bộ nhớ (Sections)](BasicSyntaxOfRAC/6_Section)
- [7/ Ghi chú và Ghép lệnh (Comments & Compound Statements)](BasicSyntaxOfRAC/7_Comment)


## Lập trình ROP
- [Chương 0: Mở đầu](LapTrinhROP/0_MoDau)
- [Chương 1: ROP là gì? (không kỹ thuật)](LapTrinhROP/1_ROPLaGi)
- [Chương 2: Register (Thanh ghi)](LapTrinhROP/2_Registers)
- [Chương 3: Stack (Ngăn xếp)](LapTrinhROP/3_Stack)
- [Chương 4: Cơ chế ROP (Return-Oriented Programming)](LapTrinhROP/4_ROP)
- [Chương 5: Viết chương trình đầu tiên](LapTrinhROP/5_VietChuongTrinhDonGian)
- [Chương 6: Gọi hàm và Mid-Function Jump](LapTrinhROP/6_GoiHamVaMidFunctionJump)
- [Chương 7: Nhảy (Jump) bằng thanh ghi SP](LapTrinhROP/7_JumpSP)
- [Chương 8: Rẽ nhánh có điều kiện (If/Else)](LapTrinhROP/8_ReNhanh)
- [Chương 9: Vòng lặp (Loops)](LapTrinhROP/9_Loops)
- [Chương 10: Cách viết Launcher](LapTrinhROP/A_CachVietLauncher)
- [Chương 11: Giải bài toán lập trình cơ bản bằng ROP](LapTrinhROP/A1_GiaiBaiToanCoBan)
- Đang tiếp tục cập nhập...


## Thủ thuật máy tính fx-580VNX
- [Hướng dẫn chơi 2048](ThuThuatFx580VNX/cach-choi-2048-tren-fx580vnx)
- [Cách vào Quickcpy Max](ThuThuatFx580VNX/cach-vao-quickcpy-max-tren-fx580vnx)
- [Pixel editor (Trình chỉnh sửa pixel 4 màu)](ThuThuatFx580VNX/cach-ve-tren-fx580vnx)
- [Hex editor (384 bytes)](ThuThuatFx580VNX/hex-editor)
- [Spinning Cube 3 axis (Trình quay khối lập phương theo 3 trục)](ThuThuatFx580VNX/spinning-cube-3-axis)
- [Neural network (AI phân biệt số 1 và số 0)](ThuThuatFx580VNX/neural-network-fx580vnx)
- [RAM Editor MAX (933 Bytes)](ThuThuatFx580VNX/ram-editor-max)
- [Text Editor with Font Custom](ThuThuatFx580VNX/text-editor-font-custom)


## Phân tích hàm hệ thống (Decompilation)
- [Cách hoạt động của keycode_cvt trên fx580vnx](Decompilation/cach-hoat-dong-cua-keycode_cvt-580vnx)
- [memset của fx580vnx](Decompilation/memset-580vnx)
- [strcpy của fx580vnx](Decompilation/strcpy-580vnx)


## Khác
- [Hướng dẫn định dạng bài viết (Tutorial Formatting Guide)](huong-dan-dinh-dang)
- [RAC Compiler API](RAC-Compiler-API)
- [ROP Transpiler API](ROP-Transpiler-API)

## Tài liệu tham khảo:

- [ROP Tutorial by user202729](ROP%20tutorial.pdf)
- [Stack (Wikipedia)](https://en.wikipedia.org/wiki/Stack-based_memory_allocation)
- https://zhuanlan.zhihu.com/p/618704031
- https://zhuanlan.zhihu.com/p/657962148
- https://zhuanlan.zhihu.com/p/620584634
- [nX-U8/100 Core Instruction Manual](nX-U8100_Core_Instruction_Manual.pdf)
- [Thư viện ma trận của steveyboi](https://gamingwithevets.github.io/thu-vien-ma-tran/)
- [Casio Calculator Hacking Wiki](http://casiocalc.wikidot.com/)

## Tham gia Đóng góp (Contributing)
VECF Wiki là một dự án mở và phát triển dựa vào cộng đồng. Nếu bạn muốn thêm bài viết mới, sửa lỗi chính tả, hoặc cải thiện nội dung, chúng tôi luôn sẵn sàng đón nhận!
-> Vui lòng xem [Hướng dẫn đóng góp (Contributing Guide)](CONTRIBUTING) để biết thêm chi tiết về cách đóng góp thông qua GitHub.

## Những người đóng góp (Contributors)
Cảm ơn tất cả những người đã dành thời gian đóng góp cho VECF Wiki!

<a href="https://github.com/luongvantam/VECF-Wiki/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=luongvantam/VECF-Wiki" alt="Contributors" />
</a>