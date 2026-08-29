# Macros Động & Hàm (Dynamic Macros & Functions)

Khi viết chương trình, nếu có những đoạn mã hoặc công thức tính toán cần lặp lại nhiều lần, bạn có thể đóng gói chúng lại bằng **Macros Động** hoặc **Hàm** để mã nguồn ngắn gọn và dễ quản lý.

---

## 1. Macros Động (Dynamic Macros)

Macro cho phép bạn tạo ra các mẫu câu lệnh tùy biến có chứa tham số dạng `<tên_tham_số>`. Mỗi khi bạn gọi macro, compiler sẽ tự động thay thế các tham số và chèn đoạn mã tương ứng vào đúng vị trí đó.

### 1.1. Macro một dòng:
Cú pháp: `def <tên_macro>(<tham_số_1>, <tham_số_2>, ...) => <lệnh_thay_thế>`

Ví dụ:
```rsc
# Định nghĩa macro cộng 2 số hex:
def add_hex(<val1>, <val2>) => eval(<val1> + <val2>)

# Sử dụng macro:
add_hex(0x10, 0x20)       # Tự động chuyển thành: eval(0x10 + 0x20) -> output 0x30
```

### 1.2. Macro dạng khối lệnh:
Cú pháp:
```rsc
def <tên_macro>(<tham_số_1>, <tham_số_2>, ...) => {
    <câu_lệnh_1>
    <câu_lệnh_2>
    ...
}
```

Ví dụ:
```rsc
# Định nghĩa macro nạp đồng thời 2 thanh ghi:
def set_two_regs(<addr>, <val>) => {
    er0 = <addr>
    er2 = <val>
}

# Sử dụng macro:
set_two_regs(0xD730, 0xFF)
```

---

## 2. Hàm Tự Định Nghĩa (Functions)

Hàm (`func`) trong RAC giúp bạn nhóm một tập hợp các câu lệnh lại với nhau, có thể truyền tham số và trả về giá trị (`return`).

### 2.1. Hàm thực thi nhiều dòng:
Cú pháp:
```rsc
func <tên_hàm>(<tham_số_1>, <tham_số_2>, ...) {
    <các_câu_lệnh>
}
```

Để gọi hàm, viết tên hàm kèm các đối số: `<tên_hàm>(<đối_số_1>, <đối_số_2>, ...)`

Ví dụ:
```rsc
func show_welcome(person) {
    "Hello {person}!"
    hex 00
}

# Gọi hàm:
show_welcome("Alice")
show_welcome("Bob")
```

### 2.2. Hàm có giá trị trả về (`return`):
Bạn có thể viết một hàm ngắn gọn có lệnh `return` để trả về giá trị tính toán và gán trực tiếp cho thanh ghi hoặc biến:

Cú pháp: `func <tên_hàm>(<các_tham_số>) { return <biểu_thức> }`

Ví dụ:
```rsc
func add(x, y) { return x + y }
func make_offset(base, index) { return base + index * 4 }

# Gán giá trị trả về của hàm vào thanh ghi:
r1 = add(5, 10)                        # r1 = 15 (0x0F)
er0 = make_offset(0x8000, 3)           # er0 = 0x800C
```

---

[<- Quay lại: 7/ Phép Toán & Biểu Thức Biên Dịch](7_CompileTimeEvaluation.md) | [Tiếp theo: 9/ Vòng Lặp & Đệm Dữ Liệu ->](9_LoopsAndPadding.md)
