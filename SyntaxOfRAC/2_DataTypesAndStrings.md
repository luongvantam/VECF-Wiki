# Kiểu Dữ Liệu, Chuỗi Ký Tự & Mảng (Data Types, Strings & Arrays)

Trong RAC, bạn có thể biểu diễn dữ liệu dưới dạng số hex, chuỗi ký tự hoặc danh sách mảng dữ liệu.

---

## 1. Dữ liệu Số & Byte Hex

### 1.1. Viết số Hex trực tiếp
Bạn có thể ghi trực tiếp các số thập lục phân (Hex) bắt đầu bằng tiền tố `0x`:

```rsc
0x02        # 1 byte
0x1234      # 2 byte (16-bit)
0xeeff      # 2 byte (16-bit)
```

### 1.2. Dùng từ khóa `hex`
Từ khóa `hex` cho phép bạn ghi một chuỗi nhiều byte liên tiếp phân tách bằng khoảng trắng:

```rsc
hex 30
hex ff ee
hex 01 02 03 04
```

> **Quy tắc thứ tự byte (Little Endian):** Trên máy tính Casio, dữ liệu số 16-bit được lưu trữ theo quy tắc byte thấp ghi trước, byte cao ghi sau. Vì vậy, số `0xeeff` tương đương với `hex ff ee`.

---

## 2. Chuỗi Ký Tự (Strings)

RAC Compiler hỗ trợ 4 cách viết chuỗi phục vụ các nhu cầu khác nhau:

### 2.1. Chuỗi thông thường (`"..."`)
Đặt chuỗi ký tự trong cặp dấu ngoặc kép. Bạn có thể sử dụng khoảng trắng bình thường giữa các từ:

```rsc
"Hello"
"Xin chào"
"Hello World!"
```

### 2.2. Chuỗi nội suy biến (F-Strings)
Bạn có thể chèn giá trị của một biến trực tiếp vào bên trong chuỗi bằng cặp ngoặc nhọn `{tên_biến}`:

```rsc
var name = "World"
"Hello {name}!"           # Kết quả: "Hello World!"

var score = 100
"Score: {score}"          # Kết quả: "Score: 100"
```

### 2.3. Chuỗi Token (`'...'`)
Đặt chuỗi trong cặp dấu nháy đơn `'...'` khi bạn muốn giữ nguyên các ký hiệu đặc thù của máy tính Casio (như hàm toán học, phím bấm):

```rsc
'sin( 9 0 )'
'Ans + 1'
```

### 2.4. Lệnh chuỗi thô `str`
Từ khóa `str` dùng để xử lý dữ liệu chuỗi thô:

- `str "<chuỗi>"`: Xuất trực tiếp chuỗi thô ra output.
- `str <tên_biến> "<chuỗi>"`: Tạo một biến kiểu chuỗi thô.
- `str <tên_biến>`: Xuất lại nội dung chuỗi của biến đó ra output.

```rsc
str "PAYLOAD_RAW"

str greeting "Xin chào"
str greeting              # Xuất lại chuỗi "Xin chào"
```

---

## 3. Mảng Dữ Liệu (Arrays)

Mảng giúp bạn nhóm nhiều giá trị byte lại với nhau trong cặp ngoặc vuông `[...]`.

### 3.1. Mảng viết trên một dòng (Inline)
Các phần tử phân tách nhau bằng dấu chấm phẩy (`;`):

```rsc
[0x01; 0x02; 0x03; 0x04]
```

### 3.2. Mảng viết trên nhiều dòng
Khi danh sách dữ liệu dài, bạn có thể xuống dòng cho từng phần tử:

```rsc
[
    0x10
    0x20
    0x30
    0x40
]
```

---

[<- Quay lại: 1/ Hello World](1_PrintHelloWorld.md) | [Tiếp theo: 3/ Biến Ảo, Thanh Ghi & Bí Danh ->](3_VariablesAndRegisters.md)
