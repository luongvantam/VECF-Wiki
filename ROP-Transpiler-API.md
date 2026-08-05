# ROP Transpiler API

VECF cung cấp một API công khai cho phép bạn dịch cú pháp tự động giữa các ngôn ngữ ROP khác nhau (ROPIDE, RAC, HD, BBBB, PYR).

## Endpoint

`POST /api/transpile`

## Request Headers

- `Content-Type: application/json`

## Request Body

| Trường | Kiểu dữ liệu | Bắt buộc | Mô tả |
| ------ | ------------ | -------- | ----- |
| `code` | string | Có | Mã nguồn cần dịch. |
| `from` | string | Có | Định dạng nguồn (hỗ trợ: `ROPIDE`, `RAC`, `HD`, `BBBB`, `PYR`). |
| `to` | string | Có | Định dạng đích (hỗ trợ: `ROPIDE`, `RAC`, `HD`, `BBBB`, `PYR`). |
| `model` | string | Không | Mã máy tính sử dụng để tải bộ gadgets tương ứng. Mặc định là `580vnx`. |

## Response

### Thành công (200 OK)

```json
{
  "success": true,
  "result": "..." // Đoạn mã đã được dịch sang ngôn ngữ đích
}
```

### Lỗi (400 Bad Request / 404 Not Found / 500 Internal Server Error)

```json
{
  "error": "Thông báo lỗi chi tiết"
}
```

## Ví dụ bằng cURL

```bash
curl -X POST https://casiovn.vercel.app/api/transpile \
-H "Content-Type: application/json" \
-d '{
  "code": "pop xr0;hex 30 30 30 30",
  "from": "RAC",
  "to": "ROPIDE"
}'
```

*(Bạn có thể tự thêm các ví dụ khác của mình vào đây)*

## Lưu ý

- API này sử dụng chung một thư viện phân tích cú pháp (parser) ở phía backend để đảm bảo độ chính xác như trên website.
- Nếu bạn sử dụng mã lệnh yêu cầu nạp `gadgets` (ví dụ: chuyển từ RAC sang ROPIDE), đảm bảo trường `model` cung cấp là một model hợp lệ có sẵn trên hệ thống (ví dụ: `580vnx`).
