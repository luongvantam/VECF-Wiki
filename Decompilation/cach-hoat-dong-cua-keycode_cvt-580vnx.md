# Cách hoạt động của keycode_cvt trên fx580vnx
```
    2:9892H    F86E            PUSH XR8
    2:9894H    F805            MOV ER8, ER0
    2:9896H    FA25            MOV ER10, ER2
    2:9898H    9000            L R0, [ER0]
    2:989AH    8200            MOV R2, R0
    2:989CH    90880001        L R0, +1H[ER8]
    2:98A0H    8300            MOV R3, R0
    2:98A2H    7000            CMP R0, #00H
    2:98A4H    C902            BEQ .l_018
    2:98A6H    7200            CMP R2, #00H
    2:98A8H    C802            BNE .l_01C
.l_018:
    2:98AAH    E000            MOV ER0, #00H
    2:98ACH    CE18            BAL .l_04C
.l_01C:
    2:98AEH    0100            MOV R1, #00H
    2:98B0H    CE02            BAL .l_024
.l_020:
    2:98B2H    1101            ADD R1, #01H
    2:98B4H    921C            SRL R2, 1
.l_024:
    2:98B6H    7200            CMP R2, #00H
    2:98B8H    C2FC            BGT .l_020
    2:98BAH    8210            MOV R2, R1
    2:98BCH    0100            MOV R1, #00H
    2:98BEH    CE02            BAL .l_032
.l_02E:
    2:98C0H    1101            ADD R1, #01H
    2:98C2H    931C            SRL R3, 1
.l_032:
    2:98C4H    7300            CMP R3, #00H
    2:98C6H    C2FC            BGT .l_02E
    2:98C8H    8010            MOV R0, R1
    2:98CAH    10FF            ADD R0, #-1H
    2:98CCH    8300            MOV R3, R0
    2:98CEH    12FF            ADD R2, #-1H
    2:98D0H    8020            MOV R0, R2
    2:98D2H    903A            SLL R0, 3
    2:98D4H    8031            ADD R0, R3
    2:98D6H    0100            MOV R1, #00H
    2:98D8H    F006            ADD ER0, ER0
    2:98DAH    F0A6            ADD ER0, ER10
    2:98DCH    9002            L ER0, [ER0]
.l_04C:
    2:98DEH    F82E            POP XR8
    2:98E0H    FE1F            RT
```

**I. Tham số của function này:**
- `ER0` = Keycode KI/KO destination
- `ER2` = Keycode table trong rom (`0x7B0E` là Normal keycode table, còn `0x83DA` là Base-N keycode table)

**II. Cách hoạt động. Lấy ví dụ là table 0x83DA trong rom, và giả sử keycode KI/KO của chúng ta là 01 02 (`key [2]`), lúc này nó sẽ thực hiện như sau:**
1. Backup thanh ghi XR8
2. Copy hai thanh ghi `ER0` và `ER2` chứa sẵn tham số vào lần lượt `ER8` và `ER10`
3. Tải giá trị keycode KI/KO từ địa chỉ lưu key sang `R0` (__Lưu ý:__ Đây là byte đầu tiên của keycode KI/KO, nên theo ví dụ nó sẽ là `0x01`)
4. Copy `R0` sang `R2` hay lúc này `R2` = `0x01`
5. Tải thêm giá trị keycode KI/KO từ địa chỉ lưu key + 1 sang `R0`. Lúc này `R0` = `0x02`
6. Copy `R0` sang `R3` hay lúc này `R3` = `0x02`
-> `ER2` = `01 02`
7. So sánh:
+ Nếu `R0` == 0 thì nhảy vào gán thanh ghi `ER0` = 0 và đẩy `XR8` ra và trả về RT
+ Nếu `R0` != 0 (ở đây `R0` == `0x02` nên chắc chắn nó sẽ khác 0) thì kiểm tra tiếp: Nếu `R2` == 0 thì sẽ thực hiện như lúc`R0` == 0. Ở đây do `R2` == `0x01` là `R2` != 0 nên nó sẽ nhảy vào `.l_01C`. Nó sẽ thực hiện như sau:
    1) Gán `R1` = 0
    2) So sánh `R2` với 0:
        + Nếu `R2` > 0 thì `R1` += 1 và đẩy bit của giá trị của thanh ghi `R2` sang phải 1 bit. Vì `R2` == `0x01` là lớn hơn 0 nên nó sẽ tăng `R1` lên 1 hay `R1` = `0x01` và  `R2` = `0b00000001` (`0x01`) sẽ trở thành `0b00000000` (`0x00`) hay `R2` = 0.
        + Do lúc này `R2` == 0 là điều kiện sai (0 > 0) nên sẽ thực hiện tiếp:
            1/ Copy `R1` sang `R2` hay `R2` = `0x01`
            2/ Gán `R1` = 0
            3/ Nhảy vào so sánh `R3` với 0:
                + Nếu `R3` > 0 thì `R1` += 1 và đẩy bit của giá trị của thanh ghi `R3` sang phải 1 bit. Vì `R3` == `0x02` là lớn hơn 0 nên nó sẽ tăng `R1` lên 1 hay `R1` = `0x01` và `R3` = `0b00000010` (`0x02`) sẽ trở thành `0b00000001` (`0x01`) hay `R3` = `0x01`. Do `R3` vẫn còn lớn hơn 0 nên tăng tiếp `R1` lên 1 hay `R1` = `0x02` và đẩy tiếp giá trị của thanh ghi `R3` sang phải 1 bit. Lúc này chắc chắn `R3` = 0.
                + Do `R3` == 0 là điều kiện sai (0 > 0) nên thực hiện tiếp:
                        1. Copy `R1` sang `R0` hay `R0` = `0x02`
                        2. `R0` -= 1 hay `R0` = `0x01`
                        3. Copy `R0` sang `R3` hay `R3` = `0x01`
                        4. `R2` -= 1 hay `R2` = 0
                        5. Copy `R2` sang `R0` hay `R0` = 0
                        6. Đẩy bit của giá trị của thanh ghi `R0` sang trái 3 bit hay `R0` = 0
                        7. `R0` += `R3` hay `R0` = `0x01`
                        8. Gán `R1` = 0
                        9. `ER0` += `ER0` hay `01 00` + `01 00` = `02 00` hay `ER0` = `02 00`
                        10. `ER0` += `ER10` hay `02 00` + `DA 83` = `DC 83` hay `ER0` = `0x83DC`
                        11. Tải giá trị từ địa chỉ `0x83DC` sang `ER0`. Do trong rom giá trị ở `0x83DC` là `32 00` nên sẽ load qua `ER0` hay `ER0` = `32 00`
                        12. Restore lại `XR8` và trả về RT.

**III. Tóm tắt lại formula**
Để tính được địa chỉ point vào keycode chuẩn thì ta dùng formula như sau:
1. Gọi keycode là `XX YY` (__Lưu ý:__ Keycode phải thuộc tập hợp: K = {`80 (01 02 04 08 10)`, `40 (01 02 04 08 10 20)`, `20 (01 02 04 08 10 20)`, `10 (01 02 04 08 10 20 40)`, `08 (01 02 04 08 10 20 40)`, `04 (01 02 04 08 10 40)`, `02 (01 02 04 08 10 40)`, `01 (01 02 04 08 10 40)`})
2. Lấy: `[(<số lần đẩy bit của value XX sang phải 1 bit về 1> << 3) + (<số lần đẩy bit của value YY sang phải 1 bit về 1>)] * 2 + <địa chỉ keycode table>` là ra địa chỉ point vào keycode chuyển đổi của keycode KI/KO `XX YY`
VD: Keycode của key [sin] là `10 08`, địa chỉ keycode table là `0x7B0E`. Lúc này ta phân tích và tính như sau:
- `0x10` = `0b00010000`. Số lần đẩy bit của value `0x10` sang phải 1 bit về 1 là 4, nên ta được kết quả là `0x04` = `0b00000100`. Đẩy bit của value `0x04` sang trái 3 bit ta được kết quả là `0b00100000`= `0x20`
-  `0x08` = `0b00001000`. Số lần đẩy bit của value `0x08` sang phải 1 bit về 1 là 3, nên ta được kết quả là `0x03`
- Cuối cùng ta lấy (`0x20`+`0x03`) * 2 + `0x7B0E` được kết quả là `0x7B54`. Giá trị của keycode chuyển đổi ở `0x7B54` là `77 00` nên ta kết luận `77 00` chính là keycode chuyển đổi từ KI/KO `10 08`