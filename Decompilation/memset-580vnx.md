# memset fx580vnx

## Khai báo hàm (Prototype)
```c
byte * // ER0
memset (
    byte *ptr, // ER0
    uword data, // ER2
    uword length // Truyền qua Stack
);
```

## Mô tả chức năng
Mục đích của hàm này là thiết lập `length` byte được trỏ bởi `ptr` thành byte thấp của `data` (tức là R2/R10). Sau khi hoàn thành, hàm trả về `ptr`, và giá trị `length` trên stack trở thành 0.

## Mã Assembly
```assembly
f_1EB94:
	push er14                      ; 1EB94 | FE5E
	mov er14, sp                   ; 1EB96 | AE1A
	push xr8                       ; 1EB98 | F86E
	push xr4                       ; 1EB9A | F46E
	push er12                      ; 1EB9C | FC5E
	mov er10, er2                  ; 1EB9E | FA25
	mov er8, er0                   ; 1EBA0 | F805
	mov r6, r10                    ; 1EBA2 | 86A0
	mov er4, er0                   ; 1EBA4 | F405
	bal .l_022                     ; 1EBA6 | CE07
.l_014:
	mov er12, er4                  ; 1EBA8 | FC45
	st r6, [er4]                   ; 1EBAA | 9641
	add er12, #01h                 ; 1EBAC | EC81
	mov er4, er12                  ; 1EBAE | F4C5
	l er12, +2h[fp]                ; 1EBB0 | BC42
	add er12, #-1h                 ; 1EBB2 | ECFF
	st er12, +2h[fp]               ; 1EBB4 | BCC2
.l_022:
	mov er0, #00h                  ; 1EBB6 | E000
	l er12, +2h[fp]                ; 1EBB8 | BC42
	cmp er0, er12                  ; 1EBBA | F0C7
	blt .l_014                     ; 1EBBC | C1F5
	mov er0, er8                   ; 1EBBE | F085
	pop er12                       ; 1EBC0 | FC1E
	pop xr4                        ; 1EBC2 | F42E
	pop xr8                        ; 1EBC4 | F82E
	mov sp, er14                   ; 1EBC6 | A1EA
	pop er14                       ; 1EBC8 | FE1E
	rt                             ; 1EBCA | FE1F
```

Bài viết gốc: [GitHub](https://github.com/Physics365/991CN-X-CW-Decompilation/)