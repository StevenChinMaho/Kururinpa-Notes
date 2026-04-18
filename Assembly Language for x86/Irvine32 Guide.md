# Irvine32 Guide

## 一、使用前準備

```asm
INCLUDE Irvine32.inc      ; 引入函式庫（放在程式最開頭）

.386
.model flat, stdcall
.stack 4096
ExitProcess PROTO, dwExitCode:DWORD
```

> 引入 `Irvine32.inc` 後即可使用所有 Irvine32 提供的函式，呼叫方式統一為 `call 函式名稱`。

---

## 二、字串 / 字元輸出函式

### WriteString — 輸出字串

```asm
; 前置條件：EDX = 字串的起始位址（OFFSET）
; 字串必須以 null (0) 結尾

.data
msg BYTE "Hello, World!", 0

.code
mov edx, OFFSET msg
call WriteString
```

### WriteChar — 輸出單一字元

```asm
; 前置條件：AL = 要輸出的字元（ASCII 碼）

mov al, 'A'
call WriteChar          ; 輸出 A
```

### Crlf — 輸出換行

```asm
; 無需參數，輸出 0Dh + 0Ah（等同 C 的 \n）

call Crlf
```

### WriteBin — 以二進位格式輸出

```asm
; 前置條件：EAX = 要輸出的值

mov eax, 00001111b
call WriteBin           ; 輸出：00000000 00000000 00000000 00001111
```

### WriteHex — 以十六進位格式輸出

```asm
; 前置條件：EAX = 要輸出的值

mov eax, 0FFh
call WriteHex           ; 輸出：000000FF
```

### WriteHexB — 以十六進位輸出（指定寬度）

```asm
; 前置條件：EAX = 值，EBX = 輸出位元組數（1/2/4）

mov eax, 0FFh
mov ebx, 1
call WriteHexB          ; 輸出：FF
```

---

## 三、整數輸出函式

### WriteInt — 輸出有號十進位整數

```asm
; 前置條件：EAX = 要輸出的有號整數

mov eax, -216
call WriteInt           ; 輸出：-216

mov eax, 500
call WriteInt           ; 輸出：+500
```

### WriteDec — 輸出無號十進位整數

```asm
; 前置條件：EAX = 要輸出的無號整數（無正負號）

mov eax, 216
call WriteDec           ; 輸出：216
```

### 整數輸出比較

|函式|EAX 內容|輸出結果|說明|
|---|---|---|---|
|`WriteInt`|-216|`-216`|有號，顯示正負號|
|`WriteInt`|500|`+500`|有號，正數有 +|
|`WriteDec`|216|`216`|無號，無正負號|
|`WriteHex`|255|`000000FF`|十六進位，固定8位|
|`WriteBin`|15|`00000000 ... 00001111`|二進位，固定32位|

---

## 四、字串 / 字元輸入函式

### ReadString — 讀取字串

```asm
; 前置條件：
;   EDX = 存放字串的緩衝區位址
;   ECX = 緩衝區最大長度（含 null）
; 回傳：EAX = 實際讀入的字元數

.data
buffer BYTE 21 DUP(0)    ; 宣告 21 bytes 緩衝區

.code
mov edx, OFFSET buffer
mov ecx, SIZEOF buffer
call ReadString
```

### ReadChar — 讀取單一字元

```asm
; 無需參數
; 回傳：AL = 讀入的字元（ASCII 碼），不顯示在螢幕

call ReadChar
mov bl, al              ; 將讀入字元存到 BL
```

---

## 五、整數輸入函式

### ReadInt — 讀取有號十進位整數

```asm
; 無需參數
; 回傳：EAX = 讀入的有號整數
; 可接受負數（使用者輸入 -100）

call ReadInt
mov myVar, eax
```

### ReadDec — 讀取無號十進位整數

```asm
; 無需參數
; 回傳：EAX = 讀入的無號整數

call ReadDec
mov myVar, eax
```

### ReadHex — 讀取十六進位整數

```asm
; 無需參數
; 回傳：EAX = 讀入的值（十六進位輸入）

call ReadHex
mov myVar, eax
```

---

## 六、畫面控制函式

### Clrscr — 清除螢幕

```asm
; 無需參數

call Clrscr
```

### Gotoxy — 移動游標至指定位置

```asm
; 前置條件：DL = X 座標（欄），DH = Y 座標（列）

mov dl, 10              ; 第 10 欄
mov dh, 5               ; 第 5 列
call Gotoxy
```

### GetTextColor — 取得目前文字顏色

```asm
; 回傳：AX = 目前顏色屬性（高4位=背景色，低4位=前景色）

call GetTextColor
```

### SetTextColor — 設定文字顏色

```asm
; 前置條件：EAX = 顏色屬性值

mov eax, white + (blue * 16)   ; 藍底白字
call SetTextColor
```

顏色常數（Irvine32 已定義）：

|常數|值|顏色|
|---|---|---|
|black|0|黑|
|blue|1|藍|
|green|2|綠|
|cyan|3|青|
|red|4|紅|
|magenta|5|洋紅|
|brown|6|棕|
|lightGray|7|淺灰|
|white|15|白|

---

## 七、其他常用函式

### WaitMsg — 顯示提示並等待 Enter

```asm
; 顯示 "Press any key to continue..." 並暫停

call WaitMsg
```

### Delay — 程式暫停（毫秒）

```asm
; 前置條件：EAX = 暫停毫秒數

mov eax, 1000           ; 暫停 1 秒
call Delay
```

### GetMseconds — 取得系統時間（毫秒）

```asm
; 回傳：EAX = 從系統啟動至今的毫秒數
; 常用於計算程式執行時間

call GetMseconds
mov startTime, eax      ; 記錄開始時間
; ... 程式碼 ...
call GetMseconds
sub eax, startTime      ; 計算經過時間
```

### DumpRegs — 顯示所有暫存器狀態

```asm
; 無需參數，顯示 EAX EBX ECX EDX ESI EDI EBP ESP EIP EFL

call DumpRegs           ; 除錯時非常好用
```

### DumpMem — 顯示記憶體內容

```asm
; 前置條件：
;   ESI = 起始位址
;   ECX = 單元數量
;   EBX = 每單元位元組數（1/2/4）

mov esi, OFFSET myArray
mov ecx, 10             ; 顯示 10 個單元
mov ebx, 4              ; 每單元 4 bytes（DWORD）
call DumpMem
```

### RandomRange — 產生隨機整數

```asm
; 前置條件：EAX = 範圍上限（不含）
; 回傳：EAX = 0 ~ (上限-1) 的隨機值

call Randomize          ; 初始化亂數種子（只需呼叫一次）
mov eax, 100
call RandomRange        ; EAX = 0~99 的亂數
```

---

## 八、整數 I/O 完整應用範例

```asm
; 讀取兩個整數並輸出總和

INCLUDE Irvine32.inc
.386
.model flat, stdcall
.stack 4096
ExitProcess PROTO, dwExitCode:DWORD

.data
msg1  BYTE "請輸入第一個整數：", 0
msg2  BYTE "請輸入第二個整數：", 0
msg3  BYTE "總和為：", 0
num1  SDWORD 0
num2  SDWORD 0

.code
main PROC
    call Clrscr                  ; 清除螢幕

    ; 輸入第一個數
    mov  edx, OFFSET msg1
    call WriteString
    call ReadInt                 ; 讀入有號整數 → EAX
    mov  num1, eax

    ; 輸入第二個數
    mov  edx, OFFSET msg2
    call WriteString
    call ReadInt
    mov  num2, eax

    ; 計算並輸出總和
    mov  edx, OFFSET msg3
    call WriteString
    mov  eax, num1
    add  eax, num2
    call WriteInt                ; 輸出有號整數
    call Crlf

    call WaitMsg                 ; 等待使用者按 Enter
    INVOKE ExitProcess, 0
main ENDP
END main
```

---

## 九、常用函式快速參照表

|函式|輸入參數|輸出|功能|
|---|---|---|---|
|`WriteString`|EDX = 字串位址|—|輸出字串|
|`WriteChar`|AL = 字元|—|輸出字元|
|`WriteInt`|EAX = 有號整數|—|輸出有號十進位|
|`WriteDec`|EAX = 無號整數|—|輸出無號十進位|
|`WriteHex`|EAX = 值|—|輸出十六進位|
|`WriteBin`|EAX = 值|—|輸出二進位|
|`Crlf`|—|—|換行|
|`Clrscr`|—|—|清除螢幕|
|`ReadString`|EDX=緩衝區, ECX=長度|EAX=字元數|讀取字串|
|`ReadChar`|—|AL = 字元|讀取字元|
|`ReadInt`|—|EAX = 有號整數|讀取有號整數|
|`ReadDec`|—|EAX = 無號整數|讀取無號整數|
|`ReadHex`|—|EAX = 值|讀取十六進位|
|`WaitMsg`|—|—|暫停等待 Enter|
|`Delay`|EAX = 毫秒|—|延遲執行|
|`DumpRegs`|—|—|顯示暫存器|
|`DumpMem`|ESI/ECX/EBX|—|顯示記憶體|
|`Randomize`|—|—|初始化亂數|
|`RandomRange`|EAX = 上限|EAX = 亂數|產生隨機數|
|`Gotoxy`|DL=X, DH=Y|—|移動游標|