# RAC Compiler API

**Endpoint:** `POST https://casiovn.vercel.app/api/rac`

Biên dịch mã ROP Script (RSC) sang Hex thông qua HTTP.

## Request

**Content-Type:** `application/json`

| Field | Kiểu | Bắt buộc | Mô tả |
| :--- | :--- | :--- | :--- |
| `code` | `string` | **Có** | Mã nguồn RSC |
| `config.json` | `string` | Không | Nội dung config (chuỗi JSON) |
| `gadgets.txt` | `string` | Không | Nội dung gadgets |
| `labels.txt` | `string` | Không | Nội dung labels |
| `disas.txt` | `string` | Không | Nội dung disassembly |
| `extensions.txt` | `string` | Không | Nội dung extensions |

> Tất cả các field file nhận **nội dung**, không phải đường dẫn.

## Response

| Field | Kiểu | Mô tả |
| :--- | :--- | :--- |
| `success` | `boolean` | Trạng thái xử lý |
| `output` | `string` | Kết quả Hex Dump |
| `notifications` | `string[]` | Log / lỗi biên dịch |

## Ví dụ

### cURL

```bash
curl -X POST https://casiovn.vercel.app/api/rac \
  -H "Content-Type: application/json" \
  -d '{"code": "org 0xe9e0\nhex30 30"}'
```

### JavaScript

```javascript
const res = await fetch('https://casiovn.vercel.app/api/rac', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    code: "org 0xe9e0\nhex30 30",
    "config.json": JSON.stringify({ overflow_initial_sp: 59872 }),
    "gadgets.txt": "",
    "labels.txt": ""
  })
});
const data = await res.json();
console.log(data.output);
```
