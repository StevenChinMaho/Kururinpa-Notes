# 1.1–1.2 研讀理由與應用領域（Reasons for Studying & Programming Domains）

> Programming Languages — Chapter 1: Preliminaries（NUK 資工系 余亞儒教授講義，Slides 1-3 ~ 1-9）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| 研讀程式語言概念的理由 | 學習語言概念能帶來的五項能力 |
| 科學應用（Scientific） | 大量浮點運算、重視效能；FORTRAN、ALGOL 60 |
| 商業應用（Business） | 報表、十進位與字元資料；COBOL |
| 人工智慧（AI） | 符號運算、linked list；LISP、Prolog |
| 系統程式（System programming） | 需低階功能以操作硬體；PL/S、C |
| 網頁軟體（Web software） | 標記語言 + 腳本語言 + 通用語言 |

---

## 1. 研讀程式語言概念的理由（Reasons for Studying Concepts of PL）

| 理由 | 說明 |
|---|---|
| Increased ability to express ideas | 使用的語言會限制思考方式；認識更多 constructs，能表達的演算法與資料結構更豐富 |
| Improved background for choosing appropriate languages | 了解各語言特性，才能依問題選擇合適語言，而非只用最熟悉的那一種 |
| Increased ability to learn new languages | 掌握共通概念（型別、控制結構、抽象化）後，學新語言只需對應語法 |
| Better understanding of significance of implementation | 了解語言的實作方式，能寫出更有效率的程式，也更容易理解某些 bug |
| Overall advancement of computing | 懂語言設計的人越多，較好的語言越容易被採用、推動整體發展 |

> Summary 投影片將理由濃縮為三點：**增加使用不同 constructs 的能力**、**更聰明地選擇語言**、**更容易學習新語言**。

---

## 2. 應用領域（Programming Domains）

不同應用領域對語言的需求不同，也因此催生了不同語言。

### 2.1 科學應用（Scientific Applications）

| 項目 | 內容 |
|---|---|
| 出現時間 | 1940 年代 |
| 資料特性 | 資料結構簡單，但有**大量浮點運算** |
| 常用資料結構 | array、matrix |
| 常用控制結構 | counting loop、selection |
| 主要考量 | **效能（efficiency）** |
| 代表語言 | FORTRAN（**For**mula **Tran**slation，IBM 設計）、ALGOL 60 |

### 2.2 商業應用（Business Applications）

| 項目 | 內容 |
|---|---|
| 出現時間 | 1950 年代 |
| 代表語言 | COBOL（**Co**mmon **B**usiness **O**riented **L**anguage） |
| 語言特色 | 能產生精緻報表（elaborate reports），能精確描述、儲存**十進位數字**與**字元資料** |
| 延伸系統 | 試算表系統（spreadsheet）、資料庫系統（database） |

### 2.3 人工智慧（Artificial Intelligence）

- 使用**符號運算（symbolic computation）**而非數值運算
- 使用 **linked list** 比 array 方便
- 函數式語言（functional language）：**LISP**（1959 年）
- 邏輯程式設計（logic programming）：**Prolog**

LISP：以遞迴計算串列長度

```lisp
(defun length (x)                      ; 定義函數 length，參數 x 為串列
  (cond ((null x) 0)                   ; 若 x 為空串列，長度為 0
        (t (+ 1 (length (cdr x))))))   ; 否則 = 1 + (去掉第一個元素後的長度)
```

- `cdr x`：取出串列除第一個元素以外的部分
- `cond`：多重條件判斷，`t` 代表 else

Prolog：以事實（fact）與規則（rule）描述知識

```prolog
likes(john, wine).          % 事實：john 喜歡 wine
likes(lance, skiing).       % 事實：lance 喜歡 skiing
likes(Z, books) :-          % 規則：若 Z 會閱讀且有好奇心，則 Z 喜歡 books
    reads(Z), is_inquisitive(Z).
```

> 講義寫成 `reads(Z) and is_inquisitive(Z)` 是為了易讀；標準 Prolog 以逗號 `,` 表示 AND。

### 2.4 系統程式（System Programming）

- 需要**低階功能（low-level features）**，讓軟體能與外部裝置溝通
- 屬於「偏機器導向的高階語言（machine-oriented high-level language）」
  - IBM：PL/S（Programming Language/Systems）或 PL/I
- UNIX 作業系統主要以 **C** 撰寫

層級關係：

| 層級 |
|---|
| Application |
| **System Program** |
| Hardware |

### 2.5 網頁軟體（Web Software）

| 類別 | 代表 | 角色 |
|---|---|---|
| 標記語言（markup） | XHTML | 在資訊頁面（文字、圖片、聲音）中嵌入**呈現指令**，由瀏覽器解讀 |
| 腳本語言（scripting） | JavaScript、PHP | 嵌入 XHTML 文件中，提供運算能力 |
| 通用語言（general-purpose） | Java | 撰寫較完整的網路應用 |

> 標記語言本身不具運算能力，運算需透過嵌入 JavaScript 或 PHP 達成。

---

## 重點整理

| 領域 | 年代 | 資料 / 運算特性 | 主要考量 | 代表語言 |
|---|---|---|---|---|
| 科學 | 1940s | 浮點運算、array/matrix | 效能 | FORTRAN、ALGOL 60 |
| 商業 | 1950s | 報表、十進位、字元資料 | 精確描述資料 | COBOL |
| 人工智慧 | 1959– | 符號運算、linked list | 符號處理 | LISP、Prolog |
| 系統程式 | — | 需操作硬體 | 低階功能、效率 | PL/S、PL/I、C |
| 網頁 | — | 呈現 + 嵌入運算 | 多語言混用 | XHTML、JavaScript、PHP、Java |
