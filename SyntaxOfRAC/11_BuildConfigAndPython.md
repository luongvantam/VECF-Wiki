# Cấu Hình Build & Nhúng Python (`@build` & `@python`)

Khi cần xuất file kết quả tự động, tùy biến định dạng hiển thị hoặc thực hiện các thuật toán sinh dữ liệu nâng cao, bạn có thể sử dụng chỉ thị `@build` và khối mã nhúng `@python`.

---

## 1. Cấu Hình Tùy Chọn Biên Dịch (`@build`)

Chỉ thị `@build` cho phép bạn tùy chỉnh các thiết lập của trình biên dịch:

### 1.1. Cú pháp dạng khối:
```rsc
@build {
    emu.inj = <true|false>
    emu.inj_file = "<tên_file>"
    emu.inj_var = "<tên_biến>"
    emu.inj_adr[<tên_phân_vùng>] = <địa_chỉ>
    line.bytes = <số_byte_mỗi_dòng>
    line.gadgets = <base_offset>
    output.file = <true|false>
    output.file_name = "<tên_file_output>"
}
```

### 1.2. Cú pháp trên một dòng:
```rsc
@build output.file = true; output.file_name = "payload.bin";
```

### 1.3. Ý nghĩa các tùy chọn:
- `emu.inj`: Bật (`true`) hoặc tắt (`false`) tính năng tự động tiêm payload vào bộ nhớ giả lập.
- `emu.inj_file`: Tên file dữ liệu giả lập cần tiêm vào.
- `emu.inj_var`: Tên biến bộ nhớ được tiêm dữ liệu.
- `emu.inj_adr[sec]`: Địa chỉ tiêm dữ liệu cho section `sec` cụ thể.
- `line.bytes`: Số lượng byte hiển thị trên mỗi dòng log kết quả (mặc định là 16).
- `line.gadgets`: Địa chỉ cơ sở khi hiển thị danh sách gadget.
- `output.file`: Thiết lập có lưu chuỗi kết quả ra file hay không.
- `output.file_name`: Tên file để lưu kết quả đầu ra.

### Ví dụ cấu hình:
```rsc
@build {
    emu.inj = true
    emu.inj_file = "casio_emu.sav"
    emu.inj_var = "rop_buffer"
    line.bytes = 16
    output.file = true
    output.file_name = "build_payload.txt"
}
```

---

## 2. Khối Lệnh Python Nhúng (`@python`)

Nếu bạn cần thực hiện các thuật toán sinh dữ liệu phức tạp (như tính toán bảng băm, mã hóa, sinh mảng lượng giác,...), bạn có thể viết trực tiếp code Python ngay bên trong file `.rsc`.

### Cú pháp:
```rsc
@python {
    <đoạn_mã_python>
}
```

### Cách thức hoạt động:
- Compiler sẽ chạy đoạn mã Python này ngay trong lúc biên dịch file `.rsc`.
- Để chuyển giá trị vừa tính toán từ Python sang RAC, bạn lưu giá trị đó vào từ điển: `loader.vars_dict["tên_biến"]`.

### Ví dụ:

```rsc
@python {
    # Viết mã Python để tính toán công thức:
    calculated_value = (0x1234 * 3) ^ 0x55AA
    loader.vars_dict["computed_seed"] = calculated_value
}

# Sau đó sử dụng biến computed_seed như bình thường trong RAC:
er0 = computed_seed
```

---

[<- Quay lại: 10/ Ghi Chú, Ghép Lệnh & Xuống Dòng](10_CommentsAndFormatting.md) | [Trang chủ Wiki ->](../README.md)
