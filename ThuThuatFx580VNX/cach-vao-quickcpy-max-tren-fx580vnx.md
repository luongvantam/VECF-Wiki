# Cách vào Quickcpy Max trên fx580vnx

---
## B1: Vào 124an
## B2: Vào small font
Nhập: 

```font-fx580vnx
<48 byte> cos^-1( ! 2 0
```

```key-fx580vnx
=C
```

---
## B3: Nhập ROP
Lưu ý: Ghi **`< công thức chung >`** tức là nhập:

```font-fx580vnx
<48 byte> ʳ cos⁻¹( 10 <xx> ! ᒧ >t 0 0 xnor ² n 0 0 √( @ 2 0 0 0 3 :
```

---
### Dòng 1:
Gán hex:

```font-fx580vnx
12345678𝒙:
A=10.000A0D1A21F80:
B=10.280D102203E9D
```

```key-fx580vnx
r===
```

```font-fx580vnx
12345678𝒙:
@=A:
@=B:
@=123
```

```key-fx580vnx
===E!ooo!!o!!!!oooE
```

Nhập như sau:

```font-fx580vnx
< công thức chung > 0 0 0 0 0 @ 0 @ . ° @ ▸t 6 k 2 0 ln( 1 0 ˣ√( ! 0 0 **E** @ 0 0 (
```

```key-fx580vnx
=C
```

Clear màn hình là đúng

---
### Dòng 2:

```font-fx580vnx
12345678𝒙:
@=10.000BED1A21FA0:
@=19C62[ADDR]23
```

> **Lưu ý: Địa chỉ [ADDR] chính là vị trí inject trong Quickcpy Max.
Vì theo little-endian chuẩn khi ghi địa chỉ vào bộ nhớ, thứ tự byte bị đảo ngược.
Ví dụ: địa chỉ hiển thị là 0xE9E0, khi inject ta phải nhập E0 E9.**

```key-fx580vnx
r===E!!!!!oooE
```

Nhập:

```font-fx580vnx
< công thức chung > or @ 0 0 0 0 - 0 . Q( 1 0 G >Simp F ° 6 LCM( 1 0 (
```

Lưu ý: Dấu trừ là nút 

```key-fx580vnx
z
```

trên máy tính


```key-fx580vnx
=C
```

Clear màn hình là đúng

---
### Dòng 3:

```font-fx580vnx
12345678𝒙:
@=10.000D4D1A21F62:
@=1D20362A0BF23
```

```key-fx580vnx
r===E!!!!!!oooE
```

Nhập:

```font-fx580vnx
< công thức chung > 0 0 . Q( 1 0 Pol( ³ Σ( 0 ▸a+bi @ 2 0 . Q( 1 0 0 ³ or ! p @ 0 ³
```

```key-fx580vnx
=C
```

Clear màn hình là đúng

---
## B4: Nhập Launcher

```font-fx580vnx
123456𝒙:
A=10.0006211D102D2:
B=12.00362A0620DBF
```

```key-fx580vnx
r===
```

```font-fx580vnx
123456𝒙:
@=A:
@=B:
@=15A23
```

```key-fx580vnx
r===E!!ooo!!!!!!ooooE
```

Nhập:

```font-fx580vnx
<48 byte> . Q( 1 0 @ >t @ 0 ▸a+bi @ 2 0 . Q( 1 0 0 ³ or ! Q( @ 2 0 0 ³ p @ 0 0 Mean(
```

```key-fx580vnx
=C
```

Nếu con trỏ ở giữa màn hình là đã thành công

---
## B5: Cách dùng
Quickcpy max cho phép inject 6 dòng small font(tức là 96 byte) và mỗi lần nhập xong 6 dòng thì bấm 

```key-fx580vnx
w3
```

Cách thoát: 

```key-fx580vnx
q91==qCWw2w1qw13
```