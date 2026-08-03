# VECF API Documentation

VECF cung cấp Public API cho phép bạn tự động hoá việc biên dịch ROP Scripts trực tiếp thông qua giao thức HTTP.

## RAC API (Biên dịch ROP Script sang Hex)

**Endpoint:** `POST /api/rac`

### Cấu trúc Request (JSON)

| Thuộc Tính | Kiểu | Bắt Buộc | Mô tả |
| :--- | :--- | :--- | :--- |
| `code` | `string` | **Có** | Mã nguồn RSC cần biên dịch. |
| `config.json` | `string` | Không | Nội dung file config.json (dạng chuỗi JSON). Các field `disassembly_file`, `gadgets_file`, `labels_file`, `extensions_file` sẽ tự động bị bỏ qua khi sử dụng API. |
| `gadgets.txt` | `string` | Không | Nội dung file gadgets.txt. |
| `labels.txt` | `string` | Không | Nội dung file labels.txt. |
| `disas.txt` | `string` | Không | Nội dung file disas.txt. |
| `extensions.txt` | `string` | Không | Nội dung file extensions.txt. |

> **Lưu ý quan trọng:** Tất cả các trường file đều nhận **nội dung** (content) chứ không phải đường dẫn (path). Nếu `config.json` có chứa các key như `disassembly_file`, `gadgets_file`, `labels_file`, `extensions_file` thì API sẽ tự động bỏ qua chúng (không báo lỗi).

**Ví dụ Request Body:**
```json
{
  "code": "print 123",
  "config.json": "{\"overflow_initial_sp\": 59872, \"char_to_hex\": {\"0\": \"30\"}}",
  "gadgets.txt": "",
  "labels.txt": "",
  "disas.txt": "",
  "extensions.txt": ""
}
```

### Cấu trúc Response (JSON)

| Thuộc Tính | Kiểu | Mô tả |
| :--- | :--- | :--- |
| `success` | `boolean` | `true` nếu API xử lý bình thường. |
| `output` | `string` | Kết quả mã Hex (Hex Dump). |
| `notifications` | `string[]` | Mảng chứa các log hoặc lỗi trong quá trình biên dịch. |

**Ví dụ Response thành công:**
```json
{
  "success": true,
  "output": "=== @section main ===\n=== 0x8000 -> (0x8010) ===\n12 34 56 78\n======",
  "notifications": ["Compile success"]
}
```

---

## Ví dụ Tích hợp

### cURL (Terminal)

```bash
curl -X POST https://your-domain.vercel.app/api/rac \
  -H "Content-Type: application/json" \
  -d '{"code": "print 123"}'
```

### JavaScript (Fetch API)

```javascript
const response = await fetch('/api/rac', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    code: "print 123",
    "config.json": JSON.stringify({ overflow_initial_sp: 59872 }),
    "gadgets.txt": "",
    "labels.txt": ""
  })
});

const data = await response.json();
console.log("Hex:", data.output);
console.log("Log:", data.notifications);
```
