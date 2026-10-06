# 3.4 靜態語意：屬性文法（Static Semantics: Attribute Grammars）

> Programming Languages — Chapter 3: Describing Semantics（NUK 資工系 余亞儒教授講義 Ch3-2，Slides 3-2 ~ 3-27）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Static vs. Dynamic Semantics | 編譯期可決定的規則 vs. 執行期的意義 |
| 為何需要 Attribute Grammars | BNF 難以或無法描述型別相容、先宣告後使用等規則 |
| Attributes | synthesized（向上傳）與 inherited（向下、跨兄弟傳） |
| Semantic functions | 規定 attribute 如何計算 |
| Predicate functions | 規定 attribute 必須滿足的條件 |
| Intrinsic attributes | 葉節點的 synthesized attribute，值來自 parse tree 外部 |
| 範例一：`A = A + B` | 型別檢查的完整流程 |
| 範例二：aⁿbⁿcⁿ | BNF 無法限制，加上 attribute 後可以 |
| 優缺點 | 正式、可產生編譯器；但複雜、難讀難寫 |

---

## 1. Static Semantics 與 Dynamic Semantics

| | Syntax | Semantics |
|---|---|---|
| 意義 | the program's **form** | the program's **meaning** |

Semantics 再分成兩種：

| 類別 | 何時決定 | 內容 | 描述方法 |
|---|---|---|---|
| **Static semantics** | **compile time** | 多與型別限制有關：先宣告後使用、型別相容、禁止重複宣告 | **Attribute Grammars** |
| **Dynamic semantics** | **run time** | 程式執行時發生什麼，例如 `while cond do block` 的意義 | Operational、Denotational、Axiomatic |

> Static semantics 名稱有「semantics」，但其實**與執行時的意義無關**，它規範的是程式的**合法形式**，本質上比較接近 syntax。

---

## 2. 為什麼需要 Attribute Grammars

### 2.1 BNF 描述起來太麻煩的規則

型別相容規則（type compatibility rule）：

- 例：Java 不允許把 floating-point 值指派給 integer 變數
- 這條限制**可以**用 BNF 描述，但需要額外的 nonterminal 與規則
- 文法會變得**過於龐大而失去實用性**

### 2.2 BNF 根本無法描述的規則

- 例：**變數必須先宣告才能使用**
- 這需要「記住」前面出現過的內容，超出 context-free grammar 的能力

### 2.3 解決方式

> **Attribute grammars（屬性文法）**：對 context-free grammar 的擴充，用來描述 static semantics（多數是型別限制）。

---

## 3. 從 `A = A + B` 思考

### 3.1 問題設定

- 在程式執行前（或語言處理器翻譯時）檢查
- 檢查 `=` 左邊變數的 type 與右邊加法結果的 type 是否相符
- 簡化規則：
  - 兩邊 type **完全一樣**才算相符（實際語言通常只要相容即可）
  - type 只有 `int` 與 `real` 兩種
  - 只有 `int + int` 的結果為 `int`，其他組合結果都是 `real`

### 3.2 要把規則加到 BNF 上，需要三件事

```text
                 <assign>          ← 檢查 "=" 兩端的 type，必須知道 <var> 的 type
               /    |    \
           <var>    =    <expr>
             |          /   |   \
             A      <var>   +   <var>
                      |           |
                      A           B    ← 必須知道 A、B 的 type
```

| 需求 | 對應機制 |
|---|---|
| 每個 symbol（terminal 或 nonterminal）都要儲存相關資訊（如 type） | **Attributes** |
| 規範這些資訊如何在樹中的 symbol 之間流通 | **Semantic functions**（attribute computation functions） |
| 規範如何檢查這些資訊 | **Predicate functions** |

---

## 4. Attributes

每個 grammar symbol X 都對應一組 attributes **A(X)**，由兩個**互斥**的集合組成：

| 集合 | 名稱 | 傳遞方向 |
|---|---|---|
| S(X) | **Synthesized attributes** | 在 parse tree 中**向上**傳遞 semantic 資訊（由 children 算出 parent） |
| I(X) | **Inherited attributes** | 在 parse tree 中**向下**（往子孫）及**跨分支**（往兄弟）傳遞 |

### 4.1 Intrinsic Attributes

- 是**葉節點**的 **synthesized** attributes（不是 inherited）
- 值由 **parse tree 外部**決定
- 例：變數的 type 來自 **symbol table**（儲存變數名稱與其 type）

> 只有葉節點才有 intrinsic attribute，且值來自外界。

---

## 5. Semantic Functions 與 Predicate Functions

### 5.1 Semantic Functions

每條文法規則都附帶一組 semantic functions。對規則 `X0 → X1 X2 … Xn`：

```text
S(X0) = f(A(X1), …, A(Xn))           ; 由 X0 的 children 算出 X0 的 synthesized attribute

I(Xj) = f(A(X0), …, A(Xn)), 1 ≤ j ≤ n    ; 由 parent 與兄弟算出 Xj 的 inherited attribute
  或
I(Xj) = f(A(X0), …, A(Xj-1))         ; 只用 parent 與「左邊」的兄弟，避免循環相依（circularity）
```

```text
          X0            ← synthesized：從下面算上來
        /  |  \
      X1  X2 … Xn       ← inherited：從上面或旁邊傳過來
```

### 5.2 Predicate Functions

- 每條規則也可附帶一組 predicate functions
- 是定義在 `{A(X0), …, A(Xn)}` 上的 **Boolean expression**

> 某條 rule 相關的所有 predicate function **都必須為 true**，這條 rule 才能用在 derivation 中。

---

## 6. 範例一：`A = A + B` 的型別檢查

### 6.1 文法與屬性

```text
<assign> → <var> = <expr>
<expr>   → <var> + <var> | <var>
<var>    → A | B | C
```

| Grammar symbol | Synthesized | Inherited |
|---|---|---|
| `<var>` | `actual_type` | — |
| `<expr>` | `actual_type` | `expected_type` |

### 6.2 規則與對應函數

同一條規則中名稱相同的 nonterminal，以 `[2]`、`[3]` 區分。

**規則 1：`<assign> → <var> = <expr>`**

```text
Semantic rule:
    <expr>.expected_type ← <var>.actual_type    ; 左邊變數的 type 往右傳給 <expr>（跨兄弟 → inherited）
```

**規則 2：`<expr> → <var>[2] + <var>[3]`**

```text
Semantic rule:
    <expr>.actual_type ←
        if (<var>[2].actual_type = int) and (<var>[3].actual_type = int)
            then int
        else real
        end if

Predicate:
    <expr>.actual_type == <expr>.expected_type
```

**規則 3：`<expr> → <var>`**

```text
Semantic rule:
    <expr>.actual_type ← <var>.actual_type      ; 由 child 往上 → synthesized

Predicate:
    <expr>.actual_type == <expr>.expected_type
```

**規則 4：`<var> → A | B | C`**

```text
Semantic rule:
    <var>.actual_type ← lookup(<var>.string)    ; 查 symbol table → intrinsic attribute
```

### 6.3 Attribute 的計算順序

| 若所有 attributes 都是… | 裝飾（decorate）樹的順序 |
|---|---|
| inherited | top-down |
| synthesized | bottom-up |
| 兩者混用（多數情況） | top-down 與 bottom-up 的組合 |

`A = A + B` 的計算流程（對應講義 Figure 3.7）：

| 步驟 | 計算 | 使用規則 | 方向 |
|---|---|---|---|
| ① | `<var>.actual_type ← lookup(A)` | 規則 4 | 葉節點取值 |
| ② | `<expr>.expected_type ← <var>.actual_type` | 規則 1 | 跨兄弟（inherited） |
| ③ | `<var>[2].actual_type ← lookup(A)` | 規則 4 | 葉節點取值 |
| ④ | `<var>[3].actual_type ← lookup(B)` | 規則 4 | 葉節點取值 |
| ⑤ | `<expr>.actual_type ← int 或 real` | 規則 2 | 向上（synthesized） |
| 檢查 | `<expr>.actual_type == <expr>.expected_type` | 規則 2 predicate | 結果為 TRUE 或 FALSE |

### 6.4 Fully Attributed Parse Tree

所有 attribute 都已計算完成的 parse tree 稱為 **fully attributed**。設 A 為 `real`、B 為 `int`（Figure 3.8）：

```text
                         <assign>
                /           |            \
   <var>                    =             <expr>  expected_type = real
   actual_type = real                             actual_type   = real
     |                                 /           |          \
     A                     <var>[2]                +          <var>[3]
                           actual_type = real                 actual_type = int
                             |                                  |
                             A                                  B
```

`real + int → real`，與 `expected_type = real` 相同 → predicate 為 **true**，敘述合法。

---

## 7. 範例二：aⁿbⁿcⁿ

目標：辨識 a、b、c 數量相同的字串。

| 字串 | 是否屬於 aⁿbⁿcⁿ |
|---|---|
| `abc`、`aaabbbccc` | 是 |
| `aaabbbbcc`、`aabbbcc` | 否 |

### 7.1 只用 BNF

```text
<letter_sequence> ::= <a_sequence> <b_sequence> <c_sequence>
<a_sequence>      ::= a | <a_sequence> a
<b_sequence>      ::= b | <b_sequence> b
<c_sequence>      ::= c | <c_sequence> c
```

這個文法能產生 `aaabbbccc`，**但也能產生** `aaabbbbcc`：BNF 無法要求三段的長度相同。

> aⁿbⁿcⁿ 不是 context-free language，任何 CFG 都無法剛好描述它。

### 7.2 加上 Attribute 與條件

```text
<letter_sequence> ::= <a_sequence> <b_sequence> <c_sequence>
    condition: Size(<a_sequence>) = Size(<b_sequence>) = Size(<c_sequence>)

<a_sequence> ::= a
                     Size(<a_sequence>) ← 1
               | <a_sequence>[2] a
                     Size(<a_sequence>) ← Size(<a_sequence>[2]) + 1

<b_sequence> ::= b
                     Size(<b_sequence>) ← 1
               | <b_sequence>[2] b
                     Size(<b_sequence>) ← Size(<b_sequence>[2]) + 1

<c_sequence> ::= c
                     Size(<c_sequence>) ← 1
               | <c_sequence>[2] c
                     Size(<c_sequence>) ← Size(<c_sequence>[2]) + 1
```

`Size` 是 **synthesized attribute**，由葉節點逐層往上加 1。

| 字串 | Size(a) | Size(b) | Size(c) | Condition | 結果 |
|---|---|---|---|---|---|
| `aaabbbccc` | 3 | 3 | 3 | true | 接受 |
| `aaabbbbcc` | 3 | 4 | 2 | **false** | 拒絕（BNF 部分合法，但條件不成立） |

---

## 8. 屬性文法的優缺點

| | 內容 |
|---|---|
| 特性 | 用來描述程式語言的**語法**及其**靜態語意** |
| 優點 | 可**正式定義**一個語言，並可作為**編譯器產生系統**的輸入 |
| 缺點 1 | 很難描述一個語言**所有**的語法與靜態語意：複雜度高、耗費大量記憶體 |
| 缺點 2 | 屬性與語意規則數量多，**難讀也難寫** |
| 缺點 3 | 套用在大型 parse tree 時**代價很高** |

---

## 重點整理

| 概念 | 定義 | 注意事項 |
|---|---|---|
| Static semantics | 編譯期可檢查的規則 | 與執行時的意義無關，多為型別限制 |
| Dynamic semantics | 執行期的意義 | 下一份筆記 |
| Attribute grammar | CFG + attributes + semantic functions + predicates | 描述 static semantics |
| Synthesized attribute | 由 children 算出，向上傳 | `<expr>.actual_type` |
| Inherited attribute | 由 parent 或兄弟算出，向下或跨分支傳 | `<expr>.expected_type` |
| Intrinsic attribute | 葉節點的 synthesized attribute，值來自外部 | 如查 symbol table 的 `lookup` |
| Semantic function | attribute 的計算規則 | inherited 只用左邊兄弟可避免循環 |
| Predicate function | 定義在 attributes 上的 Boolean 條件 | 全部為 true 才能套用該 rule |
| Fully attributed | 所有 attribute 都已計算 | |
| aⁿbⁿcⁿ | BNF 無法限制，加上 `Size` 條件後可以 | 不是 context-free language |
