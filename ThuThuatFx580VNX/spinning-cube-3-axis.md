# Spinning Cube 3 axis (Trình quay khối lập phương theo 3 trục)

## ① DỮ LIỆU VỀ CHƯƠNG TRÌNH & BỘ KHỞI ĐỘNG

**Data: 0xE9E0**
```
34 7B 31 30 10 F0 00 00 D2 03 32 30 34 7B 31 30
0A F0 00 00 D2 03 32 30 34 7B 31 30 0A D9 24 D3
22 79 31 30 34 7B 31 30 0C D9 7E D3 22 79 31 30
34 7B 31 30 10 D9 2E D3 22 79 31 30 34 7B 31 30
12 D9 38 D3 22 79 31 30 34 7B 31 30 14 D9 42 D3
22 79 31 30 34 7B 31 30 16 D9 4C D3 22 79 31 30

34 7B 31 30 18 D9 A0 D3 22 79 31 30 34 7B 31 30
1A D9 B0 D3 22 79 31 30 34 7B 31 30 1C D9 C0 D3
22 79 31 30 34 7B 31 30 1E D9 D0 D3 22 79 31 30
34 7B 31 30 0E D9 1A D3 22 79 31 30 02 26 31 30
A0 D3 58 ED 31 30 64 8E 30 30 02 26 31 30 B0 D3
58 ED 31 30 64 8E 30 30 02 26 31 30 C0 D3 58 ED

31 30 64 8E 30 30 02 26 31 30 D0 D3 58 ED 31 30
64 8E 30 30 02 26 31 30 74 D3 58 ED 31 30 9E 80
32 30 02 D9 20 9C 30 30 4A C6 31 30 74 1F 32 30
34 7B 31 30 74 D3 10 00 30 DC 31 30 34 7B 31 30
20 D9 E0 D3 22 79 31 30 34 7B 31 30 22 D9 F0 D3
22 79 31 30 34 7B 31 30 24 D9 00 D4 22 79 31 30

34 7B 31 30 26 D9 10 D4 22 79 31 30 02 26 31 30
E0 D3 58 ED 31 30 64 8E 30 30 02 26 31 30 F0 D3
58 ED 31 30 64 8E 30 30 02 26 31 30 00 D4 58 ED
31 30 64 8E 30 30 02 26 31 30 10 D4 58 ED 31 30
64 8E 30 30 7E 94 30 30 D0 08 33 30 60 8C 30 30
B0 3A 31 30 F0 D8 74 1F 32 30 34 7B 31 30 56 D3

2E D3 20 9D 30 30 34 7B 31 30 60 D3 38 D3 20 9D
30 30 34 7B 31 30 6A D3 42 D3 20 9D 30 30 34 7B
31 30 88 D3 4C D3 20 9D 30 30 34 7B 31 30 28 D9
1A D3 22 79 31 30 34 7B 31 30 74 D3 24 00 30 DC
31 30 5C 04 32 30 48 D7 60 03 F8 E9 3C D7 BA B2
32 30 24 00 1E D8 00 00 A8 D8 2A D9 2E D9 32 D9

3C D9 81 D9 C6 D9 0B DA 50 DA 58 DA 60 DA 68 DA
70 DA 78 DA 80 DA 88 DA 37 D9 77 40 D0 00 78 40
D0 00 40 A6 31 30 00 40 A7 31 30 00 32 35 36 83
33 32 A6 34 60 60 49 A7 32 36 D0 4A A6 31 30 41
60 4A A7 41 D0 D0 C8 60 33 A7 41 A7 4A D0 D0 A6
83 39 36 A6 34 60 60 49 A7 32 36 D0 4A A6 31 30

4A 60 41 A7 4A D0 D0 C8 60 33 A7 41 A7 4A D0 D0
00 32 35 36 83 33 32 A6 34 60 60 49 A7 32 36 D0
4A A7 31 30 41 60 41 A6 4A D0 D0 C8 60 33 A6 41
A7 4A D0 D0 A6 83 39 36 A6 34 60 60 49 A7 32 36
D0 41 A6 31 30 4A 60 41 A6 4A D0 D0 C8 60 33 A6
41 A7 4A D0 D0 00 32 35 36 83 33 32 A6 34 60 60

49 A7 32 36 D0 4A A6 31 30 41 60 41 A7 4A D0 D0
C8 60 33 A6 41 A6 4A D0 D0 A6 83 39 36 A6 34 60
60 49 A7 32 36 D0 41 A6 31 30 4A 60 4A A7 41 D0
D0 C8 60 33 A6 41 A6 4A D0 D0 00 32 35 36 83 33
32 A6 34 60 60 49 A7 32 36 D0 4A A6 31 30 41 60
41 A6 4A D0 D0 C8 60 33 A7 41 A6 4A D0 D0 A6 83

39 36 A6 34 60 60 49 A7 32 36 D0 41 A7 31 30 4A
60 41 A6 4A D0 D0 C8 60 33 A7 41 A6 4A D0 D0 00
32 35 36 D5 43 A6 42 00 32 35 36 D5 44 A6 43 00
32 35 36 D5 45 A6 44 00 32 35 36 D5 42 A6 45 00
32 35 36 D5 46 A6 42 00 32 35 36 D5 47 A6 43 00
32 35 36 D5 48 A6 44 00 32 35 36 D5 4B A6 45 00
```

**Bộ khởi động: 0xD180**
- **Lưu ý: Đây là bộ khởi động không chứa an-mode (non-an launcher) !**
```
FD 20 30 D7 60 03 30 30 30 30 E0 E9 24 D7 D0 08
33 30 E0 A0 30 30 5C A0 30 30 BA B2 32 30
```

## ② THÔNG TIN VỀ CHƯƠNG TRÌNH
• Chương trình có độ dài 864 bytes
• Tại phiên bản này của chương trình khối lập phương sẽ quay có chút bất ổn định, bù lại chương trình đã được rút gọn hơn so với dự kiến ban đầu cũng như tốc độ quay đã được cải thiện (Dù không đáng kể)
• Chương trình sử dụng phương pháp xoay bằng các phép lượng giác & phép chiếu hình, khối lập phương được xoay theo 3 trục x,y,z
• Do chứa các phép tính lượng giác phức tạp cũng như kết quả cho ra từ các phép tính tốn nhiều thời gian nên nếu chạy chương trình trên máy thật sẽ mất nhiều thời gian cho 1 lần hiển thị (Ước tính 2 FPS)
• Đối với việc chạy thử trên giả lập Casio, khuyến khích chỉnh *Cycle Per Second (CPS)* lên 2^25 - 2^28 ở mục *Hardware* để có trải nghiệm tốt nhất
• Chương trình được tạo ra bởi NguyenMinhKhang cũng như sự đồng hành và đóng góp to lớn của serA28 (Về mặt toán học), nếu đăng lên các nền tảng mạng xã hội vui lòng để lại **credit** để thể hiện sự tri ân cũng như tôn trọng với nhà phát triển chương trình
