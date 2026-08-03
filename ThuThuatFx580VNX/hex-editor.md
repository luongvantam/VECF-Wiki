# Hex editor

## I.Chương trình & Bộ khởi động

**Data 0xE9E0:**
```
7A 6E 31 30 69 E0 68 E0 74 82 31 30 10 40 32 30
D6 4B 31 30 86 53 31 30 74 82 31 30 10 40 32 30
9E 80 32 30 D0 E0 40 97 31 30 B6 93 30 30 DC E0
68 E0 40 DE 9E 3B 31 30 74 82 31 30 10 40 32 30
30 33 31 30 CA E0 DA 43 31 30 C6 99 30 30 86 53
31 30 EA F5 32 30 9E 80 32 30 0A DF 7A 6E 31 30

00 00 68 E0 20 9C 30 30 C0 C2 31 30 A0 9C 30 30
0A F0 40 DE AA 27 31 30 9E 93 31 30 04 F2 32 30
A6 A8 30 30 E8 DE 72 1F 32 30 34 7B 31 30 40 DE
DA 83 92 98 32 30 70 61 31 30 30 08 32 30 44 08
32 30 74 89 31 30 BA E0 D4 76 31 30 9E 80 32 30
3A DF 20 9C 30 30 86 53 31 30 4A C6 31 30 74 1F

32 30 23 E0 74 89 31 30 50 D1 D4 76 31 30 54 E2
30 30 A8 DE 6C EA 30 30 00 00 9E 80 32 30 42 DF
20 9C 30 30 4A C6 31 30 74 1F 32 30 63 E0 54 E2
30 30 32 C2 86 53 31 30 78 78 32 30 40 08 32 30
92 7E 32 30 74 89 31 30 50 D5 D4 76 31 30 34 7B
31 30 28 E0 50 00 D2 03 32 30 5C 04 32 30 E0 DD

80 01 60 DF D4 DD BA B2 32 30 40 04 30 00 FF FF
2C DE 80 08 30 00 01 00 2C DE 40 08 30 00 00 01
2C DE 80 04 30 00 00 FF 2C DE 04 10 01 00 01 00
1E DE 00 00 00 00 00 00 2C DE 30 03 F8 DE 03 03
A2 DE 51 00 CC DE 00 00 F8 DE 41 64 64 72 20 3A
44 35 35 30 00 00 56 61 6C 75 65 3A 30 30 00 00
```

**Launcher 0xD180:**

• Hex:
```
FD 24 30 30 34 7B 31 30 E0 DD E0 E9 36 89 31 30
FE 01 30 30 34 7B 31 30 60 DF E0 E9 36 89 31 30
FE 01 A2 DD D0 08 33 FD 5C A0 30 30 9E 93 31 30
```
• Token:
```font-fx580vnx
@ 0 0 4 cos⁻¹ ( 1 0 𝐆 ⁻¹ 𝐆 ► Simp 6 LCM( 1 0 in►cm 0 0 4 cos⁻¹ ( 1 0 ( 𝐓 𝐆 ► Simp 6 LCM( 1 0 in►cm xnor ⁻¹ ) @ 3 ՠτ or 0 0 @ Angle( 1 0
```

## II. Hướng dẫn sử dụng
```
[→]: +1H giá trị địa chỉ
[←]: -1H giá trị địa chỉ
[↑]: -100H giá trị địa chỉ
[↓]: +100H giá trị địa chỉ
[AC]: Đặt Stack Pointer (SP) tại địa chỉ
```

## III. Thông tin về chương trình
• Nếu người dùng muốn chỉnh địa chỉ theo ý muốn ngay từ lúc khởi động, hãy chỉnh phần địa chỉ trong dòng sau đây trong chương trình (tại vị trí `50 D5`):
```
92 7E 32 30 74 89 31 30 50 D5 D4 76 31 30 34 7B
```
• Nếu người dùng muốn chỉnh địa chỉ ngay trong chương trình, hãy chỉnh ở địa chỉ E068 để chỉnh byte thấp và E069 để chỉnh byte cao.

• Chương trình có độ dài 384 bytes, tức là 4 lần [MENU][3] trong *QuickCPY MAX* để mọi người dễ hình dung. Thực tế có thể ngắn hơn nữa nhưng sẽ không rút gọn để tiện cho việc người dùng nhập launcher.

• Chương trình giữ nguyên các tính năng như có thể giữ phím để tua địa chỉ như chương trình gốc (*Hex Editor by Xyzst*).

• Chương trình có thể thực hiện ghi đè byte vào địa chỉ F010

• Nếu người dùng thực hiện inject chương trình *Tetris* hay *Snake* của Xyzst, chương trình vẫn có thể chạy hoàn hảo sau khi khởi động bằng phím [AC] dù phông chữ hiển thị trình chỉnh sửa hex vẫn là font 0E.

## IV. Thông tin nhà phát triển
• Chương trình gốc *Hex Editor*: Xyzst
• Biên dịch về fx•580 VNX và nâng cấp lên *Hex Editor MAX*: Casio2009 
• Rút ngắn và làm lại: NguyenMinhKhang
