# 3.3（續） 歧義、優先權、結合性與 EBNF（Ambiguity, Precedence, Associativity & EBNF）

> Programming Languages — Chapter 3: Describing Syntax（NUK 資工系 余亞儒教授講義 Ch3-1，Slides 3-22 ~ 3-38）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Ambiguity | 同一 sentential form 有兩棵以上不同的 parse tree |
| 以 parse tree 表示優先權 | 越底層的 operator 優先權越高 |
| 消除歧義 | 用不同 nonterminal 區分優先權層級 |
| Associativity | 同優先權 operator 的運算方向 |
| 左遞迴 / 右遞迴 | 分別規範左結合 / 右結合 |
| Exercise、HW1 | 改寫文法、判斷句子能否被推導 |
| EBNF | `[ ]`、`( \| )`、`{ }` 三種擴充 |

---

## 1. 文法的歧義（Ambiguity in Grammars）

> A grammar is **ambiguous** if and only if it generates a sentential form that has **two or more distinct parse trees**.

- 注意：**不同的** sentential form 本來就對應不同的 parse tree，與 grammar 是否 ambiguous 無關
- 判斷重點是：**同一個**句子能否畫出兩棵不同的樹

### 1.1 一個有歧義的運算式文法

```text
<expr> → <expr> <op> <expr> | const
<op>   → / | -
```

句子 `const - const / const` 有兩棵不同的 parse tree：

```text
樹 A：先減後除 (const - const) / const      樹 B：先除後減 const - (const / const)

              <expr>                                 <expr>
           /    |    \                            /    |    \
      <expr>  <op>  <expr>                   <expr>  <op>    <expr>
     /  |  \    |      |                        |      |    /   |   \
<expr><op><expr> /   const                   const     -  <expr><op><expr>
  |    |    |                                              |    |    |
const  -  const                                          const  /  const
```

兩棵樹的 leaf 由左到右讀出來**完全相同**，但結構不同 → 此文法為 ambiguous。

> 講義將兩棵樹分別標為 leftmost / rightmost derivation。更精確的等價定義是：**同一個句子存在兩種不同的 leftmost derivation**（或兩種不同的 rightmost derivation）時，文法為 ambiguous。

---

## 2. 以 Parse Tree 表示運算子優先權（Operator Precedence）

> 運算式展開成 parse tree 後，**越底層的 operator 優先權越高**（越先計算）。

- 樹 A：`-` 在底層 → 先做減法，結果再除以最後一個 `const`
- 樹 B：`/` 在底層 → 先做除法
- 不同的 parse tree 代表**不同的運算優先次序**，計算結果也不同

因此：**若要用 parse tree 表示運算子的優先權，文法就不能有歧義。**

### 2.1 無歧義的運算式文法

```text
<expr> → <expr> - <term> | <term>
<term> → <term> / const  | const
```

`const - const / const` 只有一種 parse tree，且符合「先除後減」：

```text
            <expr>
         /    |    \
     <expr>   -    <term>
       |          /   |   \
     <term>   <term>  /   const
       |        |
     const    const
```

### 2.2 如何規範 Operator 的運算次序

1. 用**不同的 nonterminal** 代表不同優先權 operator 各自的 operand
2. 讓**低優先權**的運算**先展開**（先展開 → 位於樹的上層 → 後運算）

| 原文法 | 改寫後 |
|---|---|
| `<op>` 可展開為 `/` 或 `-`，兩者**沒有**優先權差異 | `-` 在 `<expr>` 層先展開，`/` 在 `<term>` 層 → `/` 優先權較高 |

> 口訣：**優先權越低，越靠近 start symbol；優先權越高，越靠近 leaf。**

---

## 3. 結合性（Associativity）

### 3.1 定義

當運算式中兩個 operator 擁有**相同優先權**時，先算哪一個？規範此情況的規則稱為 **associativity**。

以 `B + C + A` 為例：

| 結合性 | 方向 | 結果 |
|---|---|---|
| Left-associative（左結合） | 由左算至右 | `((B + C) + A)` |
| Right-associative（右結合） | 由右算至左 | `(B + (C + A))` |

對某些運算（如次方），左右結合的結果不同：

```text
2 ^ 3 ^ 2
左結合：(2 ^ 3) ^ 2 = 8 ^ 2 = 64
右結合：2 ^ (3 ^ 2) = 2 ^ 9 = 512     ; 數學慣例：次方為右結合
```

### 3.2 用文法表示結合性

`const + const + const` 可被以下兩種文法產生：

```text
<expr> → <expr> + <expr> | const    ; ambiguous：無法設定 associativity
<expr> → <expr> + const  | const    ; unambiguous：left-associative
```

第二種文法的 parse tree（左邊的 `+` 在底層 → 先算）：

```text
              <expr>
           /    |    \
       <expr>   +   const
     /   |   \
 <expr>  +  const
   |
 const
```

### 3.3 如何規範 Associativity

| 遞迴型式 | 定義 | 例子 | 規範 |
|---|---|---|---|
| Left recursive | LHS 出現在 RHS **最前面** | `<expr> → <expr> + const \| const` | **left associative** |
| Right recursive | LHS 出現在 RHS **最後面** | `<expr> → const + <expr> \| const` | **right associative** |

> 口訣：**遞迴在哪一邊，就往哪一邊結合。**

---

## 4. Exercise

> Rewrite the BNF of Example 3.4 to give `+` precedence over `*` and force `+` to be right associative.

Example 3.4 是 Sebesta 課本中的無歧義運算式文法（講義未附，以下依課本）：

```text
<assign> → <id> = <expr>
<id>     → A | B | C
<expr>   → <expr> + <term> | <term>
<term>   → <term> * <factor> | <factor>
<factor> → ( <expr> ) | <id>
```

**解題思路：**

1. `+` 優先權高於 `*` → 兩者**互換層級**：`*` 放到上層 `<expr>`，`+` 放到下層 `<term>`
2. `+` 右結合 → `<term>` 改成**右遞迴**
3. `*` 未要求改變，維持左遞迴（左結合）

```text
<assign> → <id> = <expr>
<id>     → A | B | C
<expr>   → <expr> * <term> | <term>       ; * 在上層：優先權低，左結合
<term>   → <factor> + <term> | <factor>   ; + 在下層：優先權高，右遞迴 → 右結合
<factor> → ( <expr> ) | <id>
```

驗證：`A = B + C * A` 會被剖析為 `(B + C) * A`；`B + C + A` 會被剖析為 `B + (C + A)`。

---

## 5. HW1：以程式實現 BNF

### 5.1 題目文法

```text
<assign> → <id> = <expr>
<id>     → A | B | C | D
<expr>   → <expr> - <term> | <id>
<term>   → <term> * <factor> | <term> / <factor> | <factor>
<factor> → ( <expr> ) | <id>
```

要求：判斷以下 statement 能否被推導，可以則輸出 `True`，否則輸出 `False`。

### 5.2 實作提示：消除左遞迴

`<expr>`、`<term>` 都是**左遞迴**，直接寫成遞迴下降（recursive descent）函數會無限遞迴。先用 EBNF 改寫成迴圈形式：

```text
<expr>   → <id> { - <term> }
<term>   → <factor> { (* | /) <factor> }
<factor> → ( <expr> ) | <id>
```

> 注意：依講義文法，`<expr>` 的另一個選項是 `<id>` 而**不是** `<term>`。因此 `<expr>` 的**第一個運算元必須是單一 identifier**，括號內的 `<expr>` 也一樣。

### 5.3 依文法字面推導的結果

| Statement | 判斷依據 | 結果 |
|---|---|---|
| `A=B - C / A - A` | `B` 為 id；`- C/A`、`- A` 都是合法的 `- <term>` | **True** |
| `A=B*C/D-A` | `-` 左邊的 `B*C/D` 必須是 `<expr>`，但 `<expr>` 開頭只能是單一 id | **False** |
| `A=B / (C-A)` | 整個 `<expr>` 沒有 `-`，只能是單一 id，但它是 `B / (...)` | **False** |
| `A=B - (C*A)` | 外層合法，但括號內 `C*A` 是 `<expr>`，開頭後面不是 `-` | **False** |
| `A=B * (D-A)` | 整個 `<expr>` 開頭後面接 `*` 而非 `-` | **False** |

推導 Statement 1 的過程（`B - C / A - A`）：

```text
<expr>
=> <expr> - <term>
=> <expr> - <term> - <term>
=> <id> - <term> - <term>
=> B - <term> - <term>
=> B - <term> / <factor> - <term>
=> B - <factor> / <factor> - <term>
=> B - C / A - <term>
=> B - C / A - A
```

> 若老師原意是 `<expr> → <expr> - <term> | <term>`（Sebesta 課本常見寫法），五個 statement 都會是 **True**。建議向老師或助教確認題目是否刻意使用 `<id>`。

---

## 6. Extended BNF（EBNF）

### 6.1 為什麼要擴充

- BNF 有一些小不便，因此衍生出多種擴充版本
- 擴充**不會增強** BNF 的描述能力，只提升**易讀性與易寫性**
- 各版本 EBNF 常見以下三種擴充

### 6.2 三種擴充

| 擴充 | 符號 | 意義 |
|---|---|---|
| Optional parts | `[ ]` | 出現 0 次或 1 次 |
| Alternative parts | `( \| )` | RHS 中多選一 |
| Repetitions | `{ }` | 出現 0 次或多次 |

**(1) Optional：`[ ]`**

```text
; EBNF
<proc_call> → ident [ ( <expr_list> ) ]

; BNF
<proc_call> → ident
            | ident ( <expr_list> )
```

C 的 if-else：

```text
; EBNF
<if_stmt> → if ( <expression> ) <statement> [ else <statement> ]

; BNF
<if_stmt> → if ( <expression> ) <statement>
          | if ( <expression> ) <statement> else <statement>
```

**(2) Alternative：`( | )`**

```text
; EBNF
<term> → <term> ( + | - ) const

; BNF
<term> → <term> + const
       | <term> - const
```

**(3) Repetition：`{ }`**（可用來化簡遞迴版本的 BNF）

```text
; EBNF
<ident_list> → <identifier> { , <identifier> }

; BNF
<ident_list> → <identifier>
             | <identifier> , <ident_list>
```

### 6.3 BNF 與 EBNF 對照

```text
; BNF
<expr> → <expr> + <term>
       | <expr> - <term>
       | <term>
<term> → <term> * <factor>
       | <term> / <factor>
       | <factor>

; EBNF
<expr> → <term> { ( + | - ) <term> }
<term> → <factor> { ( * | / ) <factor> }
```

> EBNF 的 `{ }` 對應程式中的 **while 迴圈**，這也是 recursive-descent parser 常先把文法改寫成 EBNF 的原因（見 HW1 提示）。

---

## 重點整理

| 概念 | 重點 | 注意事項 |
|---|---|---|
| Ambiguous grammar | 同一句子有 ≥ 2 棵不同 parse tree | 不同句子有不同樹不算 |
| 優先權 | 樹中越底層越先算 | 低優先權 operator 放在靠近 start symbol 的 nonterminal |
| 消除歧義 | 每個優先權層級用一個 nonterminal | `<expr>` → `<term>` → `<factor>` |
| Associativity | 同優先權時的運算方向 | 次方通常為右結合 |
| Left recursive | LHS 在 RHS 最前面 | → left associative |
| Right recursive | LHS 在 RHS 最後面 | → right associative |
| EBNF `[ ]` | 0 或 1 次 | 選用部分 |
| EBNF `( \| )` | 多選一 | |
| EBNF `{ }` | 0 或多次 | 取代遞迴；對應迴圈 |
| EBNF 描述能力 | 與 BNF 相同 | 只提升易讀性與易寫性 |
