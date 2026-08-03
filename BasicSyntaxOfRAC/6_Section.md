# Phân vùng bộ nhớ (Sections)

Section giúp chia chương trình thành nhiều vùng mã độc lập, mỗi vùng có không gian địa chỉ riêng. Điều này giúp quản lý các ROP chain hoặc shellcode lớn một cách rõ ràng và thuận tiện hơn.

## Cú pháp

```rsc
@section.<tên> [at <địa_chỉ_gốc> [backup <địa_chỉ_sao_lưu>]]
```

Trong đó:

- `at` thiết lập địa chỉ thực thi của section (tương đương với việc sử dụng lệnh `org` bên trong section).
- `backup` thiết lập địa chỉ lưu trữ của section (tương đương với lệnh `backup`) và chỉ được sử dụng khi `at` đã được khai báo.

Ví dụ hợp lệ:

```rsc
@section.main

@section.main at 0xE9E0

@section.main at 0xE9E0 backup 0xF000
```

Các ví dụ trên tương đương với:

```rsc
@section.main

@section.main
org 0xE9E0

@section.main
org 0xE9E0
backup 0xF000
```

Ví dụ không hợp lệ:

```rsc
@section.main backup 0xF000

@section.main org 0xE9E0
```

> **Lưu ý:** `at` và `backup` chỉ là các tham số của `@section`. Chúng **không phải** là các lệnh `org` và `backup`, vì vậy không thể viết `@section.<tên> org ...`.

## Ví dụ

```rsc
@section.main at 0xE9E0 backup 0xF000

0x1234
call init_func

@section.launcher at 0xD180

lbl init_func
r1 = 0x05
```

> Khi `at` hoặc `backup` được khai báo trong `@section`, compiler sẽ tự động sử dụng các giá trị này làm `org` và `backup` của section đó. Vì vậy bạn không cần khai báo lại bằng các lệnh `org` hoặc `backup`.

---

[<- Quay lại](5_DatViTriProgram.md) | [Tiếp theo ->](7_Comment.md)
