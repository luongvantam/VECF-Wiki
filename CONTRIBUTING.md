# Hướng dẫn Đóng góp (Contributing Guide)

Cảm ơn bạn đã quan tâm đến việc đóng góp cho **VECF Wiki**! Bất kỳ ai cũng có thể đóng góp nội dung, sửa lỗi chính tả, hoặc viết bài hướng dẫn mới để giúp cộng đồng phát triển.

## Cách thức đóng góp

VECF Wiki được lưu trữ công khai trên GitHub, do đó chúng tôi sử dụng quy trình Pull Request (PR) chuẩn để tiếp nhận đóng góp:

1. **Fork repository** này về tài khoản GitHub của bạn (bấm nút "Fork" ở góc trên bên phải trang GitHub).
2. **Clone** repository đã fork về máy tính:
   ```bash
   git clone https://github.com/<tên-đăng-nhập-của-bạn>/VECF-Wiki.git
   ```
3. Tạo một **branch mới** cho tính năng hoặc bài viết của bạn:
   ```bash
   git checkout -b ten-bai-viet-cua-ban
   ```
4. Thêm bài viết mới hoặc chỉnh sửa bài viết hiện có. Bài viết phải sử dụng định dạng Markdown (`.md`).
5. Commit các thay đổi của bạn với thông điệp rõ ràng:
   ```bash
   git commit -m "Thêm bài viết hướng dẫn về XYZ"
   ```
6. **Push** branch đó lên GitHub của bạn:
   ```bash
   git push origin ten-bai-viet-cua-ban
   ```
7. Mở một **Pull Request (PR)** trên repository gốc `luongvantam/VECF-Wiki`. Quản trị viên sẽ xem xét và gộp (merge) bài viết của bạn vào Wiki chính thức.

## Quy định khi viết bài

- **Định dạng**: Tất cả bài viết phải sử dụng định dạng Markdown. Vui lòng đọc kỹ [Hướng dẫn định dạng bài viết](huong-dan-dinh-dang) trước khi bắt đầu để đảm bảo bài viết hiển thị đẹp mắt trên VECF App.
- **Văn phong**: Sử dụng tiếng Việt rõ ràng, thân thiện, dễ hiểu. Đối với các thuật ngữ kỹ thuật chuyên ngành (như *Register*, *Stack*, *Gadget*...), bạn có thể giữ nguyên tiếng Anh hoặc ghi chú tiếng Việt trong ngoặc đơn ở lần xuất hiện đầu tiên.
- **Cấu trúc thư mục**: Hãy đặt bài viết của bạn vào các thư mục phù hợp (ví dụ: `LapTrinhROP/`, `ThuThuatFx580VNX/`, `Decompilation/`, ...). Nếu chủ đề hoàn toàn mới, bạn có thể tạo thư mục mới.
- **Cập nhật README**: Sau khi tạo xong bài viết, **hãy nhớ thêm link bài viết của bạn vào file `README.md`** để người đọc có thể tìm thấy nó ở trang chủ.

## Báo cáo lỗi (Issues) & Thảo luận

Nếu bạn phát hiện lỗi sai trong bài viết, link bị hỏng, hoặc có ý tưởng cho một chủ đề mới nhưng chưa có thời gian viết:
- Vui lòng tạo một [Issue mới](https://github.com/luongvantam/VECF-Wiki/issues) trên GitHub.
- Điều này giúp cộng đồng biết được vấn đề và những người khác có thể hỗ trợ sửa lỗi.

Cộng đồng VECF rất trân trọng mọi đóng góp của bạn!
