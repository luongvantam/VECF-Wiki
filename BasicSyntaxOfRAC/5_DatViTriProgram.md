# Chỉ thị Vị trí Nạp (`org` & `backup`)

`org` và `backup` là hai chỉ thị giúp RAC Compiler xác định vị trí của chương trình trong bộ nhớ. Chúng được sử dụng để tính toán địa chỉ, label, khoảng cách (`dist`) và sinh mã chính xác.

## 1. `org` (Origin)

- **Cú pháp:** `org <địa_chỉ>`

`org` xác định **địa chỉ thực thi** của chương trình. Kể từ dòng lệnh này, mọi label (`lbl`) (không bao gồm địa chỉ tương đối) sẽ được tính dựa trên địa chỉ được chỉ định.

> **Lưu ý:** Nếu section đã được khai báo bằng `@set.<section> at <địa_chỉ>`, bạn không cần sử dụng `org` nữa.

## 2. `backup`

- **Cú pháp:** `backup <địa_chỉ>`

`backup` xác định **địa chỉ lưu trữ** của chương trình. Thông tin này được compiler sử dụng để tính toán khoảng cách giữa vị trí thực thi (`org`) và vị trí lưu trữ (`backup`), chẳng hạn như khi sử dụng `dist`.

## Ví dụ

```rsc
org 0xD730        # Địa chỉ chương trình sẽ được thực thi
backup 0xE9E0     # Địa chỉ chương trình được lưu trữ
```

> `org` và `backup` không sinh dữ liệu trực tiếp vào output. Chúng chỉ cung cấp thông tin cho compiler để tính toán địa chỉ và khoảng cách trong quá trình biên dịch.

---

[<- Quay lại](4_TinhToan.md) | [Tiếp theo ->](6_Section.md)
