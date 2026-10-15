# 5.1–5.3 名稱與變數（Introduction, Names & Variables）

> Programming Languages — Chapter 5: Names, Bindings, Type Checking, and Scopes（NUK 資工系 余亞儒教授講義，Slides 5-2 ~ 5-13）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| 導論 | 指令式語言是 von Neumann 架構的抽象，變數是記憶體單元的抽象 |
| 名稱的設計議題 | 長度、連接字元、大小寫、特殊字 |
| Keyword vs. Reserved word | 只在特定情境特殊 vs. 完全不能當名稱 |
| 變數的六個屬性 | Name、Address、Value、Type、Lifetime、Scope |
| Aliases | 兩個名稱存取同一塊記憶體 |
| l-value 與 r-value | 位址 vs. 值 |

---

## 1. 導論（Introduction）

- 指令式語言（imperative languages）是 **von Neumann 架構**的抽象
- 架構的兩大元件：**Memory**、**Processor**
- 變數由一組**屬性（attributes）**描述，其中最重要的是 **Type**

---

## 2. 名稱（Names）

### 2.1 設計議題（Design Issues）

| 議題 | 問題 |
|---|---|
| 長度 | 最大長度是多少？ |
| 連接字元 | 能否使用 `_` 等連接字元？ |
| 大小寫 | 名稱是否區分大小寫（case sensitive）？ |
| 特殊字 | 特殊字是 reserved word 還是 keyword？ |

### 2.2 長度（Length）

名稱太短會缺乏意義。

| 語言 | 最大長度 |
|---|---|
| FORTRAN I | 6 |
| COBOL | 30 |
| FORTRAN 90、ANSI C | 31 |
| Ada、Java | **無限制**，且每個字元都有意義 |
| C++ | 無限制，但實作上常會設上限 |

### 2.3 連接字元（Connectors）

- Pascal、Modula-2、FORTRAN 77：**不允許**
- 其他語言大多允許

### 2.4 大小寫（Case Sensitivity）

- C、C++、Java 的名稱**區分大小寫**；其他語言多數不區分
- 缺點：**降低易讀性**，看起來相似的名稱其實是不同的變數

```c
int Int;   // 合法：Int 與 int 是不同的名稱
int INT;   // 又是另一個變數
```

- C++、Java 更嚴重，因為預先定義的名稱混用大小寫（如 `IndexOutOfBoundsException`），必須記住正確寫法

### 2.5 特殊字（Special Words）

特殊字可以是 **keyword** 或 **reserved word**：

| 類型 | 定義 | 例子 |
|---|---|---|
| Keyword | **只在特定情境下**才有特殊意義 | Fortran 的 `Real` |
| Reserved word | **不能**當作使用者定義的名稱 | C 的 `int`、`while` |

Fortran 的 keyword 範例：

```fortran
Real VarName        ! Real 後面接名稱 → Real 是資料型別（keyword）
Real = 3.4          ! Real 出現在指派左邊 → Real 是變數
```

缺點：**易讀性差**

```fortran
REAL INTEGER        ! 宣告一個名為 INTEGER 的 REAL 變數
INTEGER REAL        ! 宣告一個名為 REAL 的 INTEGER 變數
```

> Keyword 能依情境改變意義，因此可以拿型別名稱當變數名稱；reserved word 則完全禁止，易讀性較好。

---

## 3. 變數（Variables）

### 3.1 定義與六個屬性

> A variable is an **abstraction of a memory cell**.

變數可以用六個屬性（sextuplet）描述：

| 屬性 | 說明 |
|---|---|
| **Name** | 名稱（不是所有變數都有，例如 heap 上的匿名變數） |
| **Address** | 對應的記憶體位址 |
| **Value** | 記憶體單元中的內容 |
| **Type** | 值的範圍與可用的運算 |
| **Lifetime** | 綁定到某記憶體單元的期間 |
| **Scope** | 變數的有效範圍 |

> 口訣：**N-A-V-T-L-S**

### 3.2 Address

**同一個名稱**在不同**位置**可以對應不同位址：

```c
int sub1() {
    int sum;    // sub1 的 sum
    ...
}
int sub2() {
    int sum;    // sub2 的 sum：名稱相同，但是不同變數、不同位址
    ...
}
```

**同一個變數**在不同**時間**也可以對應不同位址：

```c
int fun1() {
    int cnt;    // 第 1、2、3 次呼叫 fun1 時，cnt 可能被配置在不同位址（stack 上）
    ...
}
```

### 3.3 別名（Aliases）

- 兩個以上的變數名稱可以存取**同一塊記憶體**時，稱為 aliases
- 產生方式：**pointers**、**reference variables**、C/C++ 的 **unions**

```c
int a;
int *b = &a;
int *c = &a;    // *b 與 *c 都存取 a → 互為 aliases
```

- 缺點：**降低易讀性**，讀程式的人必須記住所有別名

### 3.4 Type

- 決定變數值的**範圍**以及可對該型別使用的**運算**
- 浮點數的 type 也決定**精確度（precision）**

### 3.5 Value：l-value 與 r-value

| 名稱 | 意義 | 出現位置 |
|---|---|---|
| **l-value** | 變數的**位址** | 指派敘述左邊 |
| **r-value** | 變數的**值** | 指派敘述右邊 |

```c
x = x + 1;
// 左邊的 x 用的是 l-value（要寫入的位址）
// 右邊的 x 用的是 r-value（目前的值）
```

---

## 重點整理

| 概念 | 重點 | 注意事項 |
|---|---|---|
| 名稱長度 | FORTRAN I: 6、COBOL: 30、C: 31、Ada/Java: 無限制 | C++ 實作常設上限 |
| 連接字元 | Pascal、Modula-2、FORTRAN 77 不允許 | |
| Case sensitive | C、C++、Java | 缺點：易讀性 |
| Keyword | 只在特定情境特殊 | Fortran `Real`；易讀性差 |
| Reserved word | 不能當名稱 | |
| 變數六屬性 | Name、Address、Value、Type、Lifetime、Scope | 變數是記憶體單元的抽象 |
| Address | 同名不同處、同變數不同時，位址都可能不同 | |
| Aliases | 多名稱指向同一記憶體 | 來自 pointer、reference、union；傷害易讀性 |
| l-value / r-value | 位址 / 值 | |
