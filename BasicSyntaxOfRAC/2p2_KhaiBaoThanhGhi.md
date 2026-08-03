# Khai Báo Thanh Ghi (Registers)

Bên cạnh Virtual Variable, RAC Compiler cho phép bạn gán giá trị trực tiếp cho các thanh ghi của CPU. Không giống biến ảo, thao tác này sẽ được compiler chuyển thành các lệnh ROP/Assembly tương ứng với kiến trúc mục tiêu.

## 1. Cú pháp gán thanh ghi

Bạn có thể sử dụng từ khóa `reg` hoặc gán trực tiếp:

- `reg <thanh_ghi> = <giá_trị>`
- `<thanh_ghi> = <giá_trị>`

Ví dụ:

```rsc
reg r1 = 0x05          # Gán giá trị 5 cho thanh ghi r1
r2 = 0xFF              # Gán giá trị 255 cho thanh ghi r2
er0 = 0x1234           # Gán giá trị 16-bit cho thanh ghi er0
```

> **Lưu ý:** Khác với Virtual Variable, việc gán thanh ghi sẽ sinh mã ROP/Assembly trực tiếp vào output.

Ví dụ:

```rsc
xr0 = hex 30 30 30 30
```

Compiler sẽ sinh mã tương đương:

```rsc
pop xr0
hex 30 30 30 30
```

> Giá trị được nạp vào thanh ghi thông qua **stack**. Vì vậy compiler sẽ tự động sinh gadget phù hợp (ví dụ: `pop`) để đưa dữ liệu vào thanh ghi trước khi chương trình tiếp tục thực thi.

---

[<- Quay lại](2p1_KhaiBaoBienAo.md) | [Tiếp theo ->](2p3_KhaiBaoGadget.md)
