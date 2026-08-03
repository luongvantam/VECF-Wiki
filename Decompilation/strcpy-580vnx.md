# strcpy fx580vnx

## Khai báo hàm (Prototype)
```c
char * // ER0
strcpy (
    char *destination, // ER0
    const char *source // ER2
);
```

## Mô tả chức năng
Mục đích của hàm này là sao chép chuỗi kết thúc bằng ký tự null (`\0`) được trỏ bởi `source` sang vị trí được trỏ bởi `destination`. Sau khi hoàn thành, hàm trả về `destination`, tức là địa chỉ bắt đầu của chuỗi đã sao chép.

## Mã Assembly
```assembly
f_0E5C8:
	push xr8                       ; 0E5C8 | F86E
	push er12                      ; 0E5CA | FC5E
	mov er8, er0                   ; 0E5CC | F805
	mov er10, er2                  ; 0E5CE | FA25
	mov er12, er0                  ; 0E5D0 | FC05
.l_00A:
	l r0, [er10]                   ; 0E5D2 | 90A0
	st r0, [er12]                  ; 0E5D4 | 90C1
	add er10, #01h                 ; 0E5D6 | EA81
	add er12, #01h                 ; 0E5D8 | EC81
	mov r0, r0                     ; 0E5DA | 8000
	bne .l_00a                     ; 0E5DC | C8FA
	mov er0, er8                   ; 0E5DE | F085
	pop er12                       ; 0E5E0 | FC1E
	pop xr8                        ; 0E5E2 | F82E
	rt                             ; 0E5E4 | FE1F
```

Bài viết gốc: [GitHub](https://github.com/Physics365/991CN-X-CW-Decompilation/)