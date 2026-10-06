# 3.5 動態語意（二）：公理語意（Axiomatic Semantics）

> Programming Languages — Chapter 3: Describing Semantics（NUK 資工系 余亞儒教授講義 Ch3-2，Slides 3-51 ~ 3-69）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| 基本概念 | 以數學邏輯（predicate calculus）證明程式正確性 |
| Pre-post form | `{P} statement {Q}` |
| Weakest precondition | 能保證 postcondition 的最寬鬆前置條件 |
| 程式證明流程 | 從最後一行往前推 wp |
| Inference rule 與 axiom | 推論規則與無前提的推論規則 |
| Assignment axiom | `{Q[x→E]} x = E {Q}` |
| Rule of consequence | 前置條件可加強，後置條件可放寬 |
| Sequence rule | 串接兩段敘述 |
| Loop rule 與 loop invariant | while 迴圈的證明 |
| 評價與章節總結 | |

---

## 1. 基本概念

| 項目 | 內容 |
|---|---|
| 意義 | 提供**數學規則**來表示程式執行的結果 |
| 作法 | 對每個語法單元提供一條數學規則，用**數學推論證明程式的正確性** |
| 名稱由來 | 基於**數學邏輯（predicate calculus）** |
| 邏輯運算式 | 稱為 **predicates** 或 **assertions** |
| 原始目的 | **證明程式的正確性** |
| 敘述的意義 | 執行該敘述的效果，以「關於被處理資料的邏輯運算式」表示 |

---

## 2. Pre-Post Form 與 Assertions

### 2.1 形式

```text
{P} statement {Q}
 ↑              ↑
precondition    postcondition
```

| Assertion | 位置 | 意義 |
|---|---|---|
| **Precondition** | 敘述之前 | 執行到此時變數之間成立的關係與限制（RHS 變數的值域） |
| **Postcondition** | 敘述之後 | 執行結果的限制（LHS 的結果範圍） |

- 若執行前 precondition 成立，則執行後 postcondition 成立
- 若執行前 precondition 不成立，執行後 postcondition **仍有可能**成立

### 2.2 Weakest Precondition（wp）

> **Weakest precondition**：能保證 postcondition 成立的**限制最少（最寬鬆）**的 precondition。

例：`a = b + 1 {a > 1}`

| Precondition | 是否保證 a > 1 | 說明 |
|---|---|---|
| `{b > 10}` | 是 | 一個可能的 precondition，但太嚴格 |
| `{b > 0}` | 是 | **weakest precondition**：b + 1 > 1 ⇔ b > 0 |

```text
{b > 10} a = b + 1 {a > 1}     ; 正確，但不是最弱
{b > 0}  a = b + 1 {a > 1}     ; weakest precondition
```

> `b > 10` ⇒ `b > 0`：範圍較小的條件**蘊含**範圍較大的條件。wp 是範圍最大的那一個。

---

## 3. 程式證明流程（Program Proof Process）

1. 把程式**期望的執行結果**當作**最後一個敘述**的 postcondition
2. 用 inference rules 與 axioms 算出最後一個敘述的 **weakest precondition**
3. 把這個 wp 當作**倒數第二個**敘述的 postcondition
4. 重複，直到推回程式開頭
5. 若第一個敘述的 wp 能被程式規格滿足，則**程式正確**

> 證明方向是**由後往前**。

---

## 4. Inference Rule 與 Axiom

### 4.1 Inference Rule（推論規則）

根據其他 assertion 的真假推得某個 assertion 為真：

```text
S1, S2, …, Sn       ← antecedent（前提）
─────────────
      S             ← consequence（結論）
```

若 S1, S2, …, Sn 皆為真，則可推得 S 為真。

### 4.2 Axiom（公理）

- 被**假定為真**的邏輯敘述
- 是**沒有 antecedent** 的 inference rule
- 例：給定指派敘述與其 postcondition，其 weakest precondition 可由 axiom 定義

---

## 5. 指派敘述的公理（Axiom for Assignment）

```text
{Q[x→E]} x = E {Q}
```

`Q[x→E]`：把 Q 中所有的 x **替換成** E。

例：`a = b / 2 - 1 {a < 10}`

```text
Q[a → b/2 - 1]:   b / 2 - 1 < 10
                  b / 2 < 11
                  b < 22

{b < 22} a = b / 2 - 1 {a < 10}     ; b < 22 是 weakest precondition
```

> 口訣：**把 postcondition 裡被指派的變數，換成等號右邊的運算式**。

---

## 6. Rule of Consequence

```text
{P} S {Q},   P' ⇒ P,   Q ⇒ Q'
──────────────────────────────
          {P'} S {Q'}
```

| 條件 | 意義 |
|---|---|
| `P' ⇒ P` | precondition 可以**加強**（換成範圍更小、更嚴格的 P'） |
| `Q ⇒ Q'` | postcondition 可以**放寬**（換成範圍更大的 Q'） |

> 直覺：P' 比 P 更 tight，所以 P' 成立時 P 一定成立；執行 S 得到 Q，而 Q 落在更大的 Q' 範圍內，因此 Q' 也成立。

**例：證明 `{x > 5} x = x - 3 {x > -1}`**

```text
1. 由 assignment axiom：
       {x > 3} x = x - 3 {x > 0}          ; x - 3 > 0 ⇔ x > 3

2. 由 rule of consequence：
       {x > 3} x = x - 3 {x > 0},  (x > 5) ⇒ (x > 3),  (x > 0) ⇒ (x > -1)
       ──────────────────────────────────────────────────────────────────
                         {x > 5} x = x - 3 {x > -1}
```

---

## 7. 串接的推論規則（Sequence Rule）

```text
{P1} S1 {P2},   {P2} S2 {P3}
────────────────────────────
     {P1} S1; S2 {P3}
```

**例：**

```c
y = 3 * x + 1;
x = y + 3;
// {x < 10}
```

由後往前推：

```text
1. x = y + 3 {x < 10}          → y + 3 < 10 → wp: {y < 7}
2. y = 3 * x + 1 {y < 7}       → 3x + 1 < 7 → wp: {x < 2}

結論：{x < 2} y = 3 * x + 1; x = y + 3; {x < 10}
```

---

## 8. While 迴圈的推論規則

### 8.1 推論規則

要證明 `{P} while B do S end {Q}`，最關鍵的是找到 **loop invariant I**：

```text
      {I and B} S {I}
──────────────────────────────
{I} while B do S {I and (not B)}
```

- I 是迴圈不變量（inductive hypothesis），**很難找**

### 8.2 Loop Invariant 必須滿足的條件

| 條件 | 意義 |
|---|---|
| `P ⇒ I` | weakest precondition 必須蘊含 loop invariant |
| `{I and B} S {I}` | 執行迴圈本體**不會改變** I 的真假 |
| `(I and (not B)) ⇒ Q` | 若 I 為真且 B 為假（迴圈結束），可推得 Q |
| The loop terminates | 迴圈會結束（**難以證明**） |

完整規則：

```text
P ⇒ I,   {I and B} S {I},   (I and (not B)) ⇒ Q
───────────────────────────────────────────────
             {P} while B do S {Q}
```

### 8.3 如何找 Loop Invariant

- 用 Q 計算迴圈本體執行 **0 次、1 次、2 次…** 時的 precondition
- 從中**找出規律**

### 8.4 範例：`while y <> x do y = y + 1 end {y = x}`

| 執行次數 | Weakest precondition | 推導 |
|---|---|---|
| 0 | `{y = x}` | 不進迴圈，必須已經 y = x |
| 1 | `{y = x - 1}` | `{y = x - 1} y = y + 1 {y = x}` |
| 2 | `{y = x - 2}` | `{y = x - 2} y = y + 1 {y = x - 1}` |
| 3 | `{y = x - 3}` | `{y = x - 3} y = y + 1 {y = x - 2}` |
| ≥ 1 | `{y < x}` | 規律 |

合併 0 次與 ≥ 1 次的情況 → **I = `{y ≤ x}`**

**證明** `{y < x - 8} while y <> x do y = y + 1 end {y = x}`：

| 條件 | 檢查 | 結果 |
|---|---|---|
| `P ⇒ I` | y < x - 8 ⇒ y ≤ x | ✓ |
| `{I and B} S {I}` | y ≤ x 且 y ≠ x → y < x；執行 y = y + 1 後 y ≤ x | ✓ |
| `(I and (not B)) ⇒ Q` | y ≤ x 且 y = x ⇒ y = x | ✓ |
| Loop terminates | y < x 時每次 y 加 1，最終會等於 x | ✓ |

四項皆成立 → 得證。

---

## 9. 公理語意的評價

| 面向 | 評價 |
|---|---|
| 困難點 | 為某些敘述設計 axiom 或 inference rule 很困難；解法之一是**用公理方法設計語言**，但這樣的語言會很小、很簡單 |
| 優點 | 研究**正確性證明**的好工具，也是推理程式的絕佳框架 |
| 限制 | 對**語言使用者**或**編譯器撰寫者**描述語言意義的用處有限 |

---

## 10. 第 3 章總結（Summary）

| 主題 | 結論 |
|---|---|
| BNF 與 CFG | 等價的 meta-languages，很適合描述程式語言語法 |
| Attribute grammar | 可同時描述語言的**語法**與**（靜態）語意**的形式化方法 |
| 三種主要語意描述方法 | Operational、Axiomatic、Denotational |

### 三種動態語意方法比較

| | Operational | Denotational | Axiomatic |
|---|---|---|---|
| 意義表示為 | 機器狀態的變化 | 數學物件（映射函數） | 邏輯斷言（pre/post conditions） |
| 基礎 | 低階程式語言 | 遞迴函數論 | 數學邏輯（predicate calculus） |
| 抽象程度 | 低 | **最抽象** | 高 |
| 主要用途 | 語言手冊、教學 | 證明正確性、語言設計、編譯器產生 | **證明程式正確性** |
| 缺點 | 正式使用極複雜（VDL） | 對語言使用者太複雜 | 部分敘述難寫規則；對使用者用處有限 |

---

## 重點整理

| 概念 | 規則 / 定義 | 注意事項 |
|---|---|---|
| Pre-post form | `{P} S {Q}` | P 為前置條件，Q 為後置條件 |
| Weakest precondition | 保證 Q 的最寬鬆 P | 範圍最大的那個 |
| 證明流程 | 由最後一行往前推 wp | |
| Axiom | 無 antecedent 的 inference rule | |
| Assignment axiom | `{Q[x→E]} x = E {Q}` | 把 Q 中的 x 換成 E |
| Rule of consequence | `P' ⇒ P`、`Q ⇒ Q'` | 前置可加強，後置可放寬 |
| Sequence rule | `{P1}S1{P2}, {P2}S2{P3} ⊢ {P1}S1;S2{P3}` | 中間條件相接 |
| Loop rule | `{I∧B}S{I} ⊢ {I} while B do S {I∧¬B}` | I 難找 |
| Loop invariant 四條件 | P⇒I、{I∧B}S{I}、(I∧¬B)⇒Q、會終止 | 終止性最難證 |
| 找 I 的方法 | 算 0、1、2…次的 wp 找規律 | 例：I = y ≤ x |
