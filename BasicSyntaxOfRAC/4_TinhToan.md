# Phép toán Thời gian Biên dịch (Compile-time Evaluation)

RAC Compiler cung cấp hai hàm `eval()` và `calc()` để tính toán biểu thức trong quá trình biên dịch. Sau khi tính toán xong, **compiler sẽ thay thế biểu thức bằng giá trị kết quả trong hex output**.

## Cú pháp

- `eval(<biểu_thức>)`
- `calc(<biểu_thức>)`

Biểu thức có thể trải dài trên nhiều dòng bằng cách sử dụng dấu ngoặc `()`.

> **Lưu ý:** Hàm calc() tương đương với hàm eval() chỉ là do cách gọi tên khác cho gần gũi, dễ dùng.

## Ví dụ

```rsc
eval(0x1 + 0x2 * 0x3)         # Output: 0x07

var distance = calc(
    adr(label1) - adr(label2)
)
```

> `eval()` và `calc()` đều được thực hiện trong quá trình biên dịch. Chúng không được ghi nguyên dạng vào output mà **được thay thế bằng giá trị Hex tương ứng**, tương tự như `sizeof()` và `dist`.

---

[<- Quay lại](3_Label.md) | [Tiếp theo ->](5_DatViTriProgram.md)
