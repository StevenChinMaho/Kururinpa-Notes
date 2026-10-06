# 3.3 描述語法的形式方法：BNF、推導與剖析樹（BNF, Derivation & Parse Tree）

> Programming Languages — Chapter 3: Describing Syntax（NUK 資工系 余亞儒教授講義 Ch3-1，Slides 3-10 ~ 3-21）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| 形式方法概覽 | BNF/CFG、EBNF、Grammars and Recognizers |
| Context-Free Grammar | Chomsky 提出，用來描述 context-free language |
| BNF | Backus 提出，與 CFG 等價的 metalanguage |
| BNF 要素 | nonterminal、terminal、grammar、start symbol |
| BNF 規則 | LHS → RHS，以 `\|` 表示多種定義 |
| 描述串列 | 以遞迴取代 `…` |
| Derivation | 從 start symbol 反覆套用規則得到 sentence |
| Parse Tree | derivation 的階層式表示 |

---

## 1. 形式方法概覽（Formal Methods of Describing Syntax）

| 方法 | 說明 |
|---|---|
| BNF 與 Context-Free Grammars | 描述程式語言語法**最廣為人知**的方法 |
| Extended BNF（EBNF） | 提升 BNF 的易讀性與易寫性 |
| Grammars and Recognizers | 能被 context-free grammar 產生（辨識）的語言稱為 **context-free language** |

---

## 2. Context-Free Grammars（CFG）

- 由 **Noam Chomsky** 於 **1950 年代中期**提出
- 是一組**遞迴規則**，用來產生字串的模式
- 用來描述 **context-free languages**
- 能描述所有 regular languages 以及更多語言，但**無法描述所有可能的語言**
- 應用於理論計算機科學、編譯器設計，以及描述程式語言
- 編譯器中的 **parser 可以由 CFG 自動產生**

> 語言能力的包含關係：Regular languages ⊂ Context-free languages ⊂ 所有語言

---

## 3. Backus-Naur Form（BNF）

| 項目 | 內容 |
|---|---|
| 提出時間 | 1959 |
| 提出者 | **John Backus**，用來描述 **ALGOL 58** |
| 與 CFG 的關係 | **BNF 與 context-free grammar 等價** |
| 性質 | **metalanguage**：用來描述另一個語言的語言 |
| 抽象（abstraction） | 用來代表一類語法結構，作用如同語法變數，又稱 **nonterminal symbols** |

---

## 4. BNF 文法要素（BNF Fundamentals）

| 要素 | 說明 | 例子 |
|---|---|---|
| Nonterminals（非終端符號） | BNF 的抽象，可再被展開 | `<ident_list>`、`<if_stmt>`、`<logic_expr>`、`<stmt>` |
| Terminals（終端符號） | lexemes 與 tokens，不可再展開 | `if`、`then`、`,`、`identifier` |
| Grammar（文法） | 規則的集合 | 下方兩條規則合起來 |
| Start symbol（起始符號） | 一個特殊的 nonterminal，推導由它開始 | `<program>` |

```text
<ident_list> → identifier | identifier , <ident_list>
<if_stmt>    → if <logic_expr> then <stmt>
```

- `→`、`|`：grammar symbol（文法本身的符號）
- `identifier`、`,`、`if`、`then`：terminal
- `<ident_list>`、`<logic_expr>`、`<stmt>`：nonterminal

### 4.1 BNF 文法符號說明

| 符號 | 意義 | 備註 |
|---|---|---|
| `::=` | 「定義為」，相當於 `→` | |
| `\|` | OR | |
| `< >` | 非終端符號 | |
| `{ }` | 出現 0 次、1 次、…（重複） | **EBNF 才有** |
| `[ ]` | 出現 0 次或 1 次（可省略） | **EBNF 才有** |

---

## 5. BNF 規則（BNF Rules）

- 一條規則有 **LHS**（left-hand side）與 **RHS**（right-hand side），由 terminal 與 nonterminal 組成
- `→` 讀作「can be recognized as」或「can be expanded to」
- Grammar 是**有限且非空**的規則集合
- 一個 nonterminal 可以有**多個 RHS**

```text
<stmt> → <single_stmt>               ; LHS 是 <stmt>，第一個 RHS
       | begin <stmt_list> end       ; 第二個 RHS
```

多個定義可以分開寫，也可以用 `|` 合併成一條：

```text
<ident_list> → ident                     ; 寫法一：分成兩條規則
<ident_list> → ident , <ident_list>

<ident_list> → ident                     ; 寫法二：用 | 合併
             | ident , <ident_list>
```

---

## 6. 描述串列（Describing Lists）

要描述「一個或多個以逗號分隔的 identifier」：

```text
apple
apple, egg
apple, egg, book
apple, egg, book, …
```

BNF **沒有省略號 `…`**，因此串列必須用**遞迴**描述：

```text
<ident_list> → ident
             | ident , <ident_list>   ; RHS 中再次出現 LHS → 遞迴
```

---

## 7. 推導（Derivation）

### 7.1 定義

**Derivation**：從 start symbol 開始，**反覆套用規則**，直到得到一個 sentence（全部是 terminal symbols）。`=>` 讀作「derives」。

範例文法：

```text
<program> → <stmts>                                  ; start symbol
<stmts>   → <stmt> | <stmt> ; <stmts>
<stmt>    → <var> = <expr>
<var>     → a | b | c | d
<expr>    → <term> + <term> | <term> - <term>
<term>    → <var> | const
```

推導 `a = b + const`（每次展開一個 nonterminal）：

```text
<program>
=> <stmts>
=> <stmt>
=> <var> = <expr>
=> a = <expr>
=> a = <term> + <term>
=> a = <var> + <term>
=> a = b + <term>
=> a = b + const
```

### 7.2 相關術語

| 術語 | 定義 |
|---|---|
| Sentential form | 推導過程中出現的**每一個**符號字串（如 `a = <term> + <term>`） |
| Sentence | **只含 terminal symbols** 的 sentential form |
| Leftmost derivation | 每一步都展開**最左邊**的 nonterminal |
| Rightmost derivation | 每一步都展開**最右邊**的 nonterminal |
| 一般推導 | 可以既非 leftmost 也非 rightmost（展開次序不固定） |

> 上面的推導每一步都展開最左邊的 nonterminal，所以是 **leftmost derivation**。

---

## 8. 剖析樹（Parse Tree）

Parse tree 是 derivation 的**階層式表示**。

- **Internal node**：nonterminal
- **Leaf node**：terminal symbol
- 由左到右讀出所有 leaf，就是推導出的 sentence

`a = b + const` 的 parse tree：

```text
            <program>
                |
             <stmts>
                |
             <stmt>
         /      |      \
      <var>     =     <expr>
        |           /    |    \
        a       <term>   +   <term>
                  |             |
                <var>         const
                  |
                  b
```

> 同一棵 parse tree 可以對應多種推導順序（leftmost、rightmost…），但 parse tree 本身只表達**結構**，不表達展開順序。

---

## 重點整理

| 概念 | 重點 | 注意事項 |
|---|---|---|
| CFG | Chomsky，1950s 中期；遞迴規則 | 能描述 regular languages 以上，但非所有語言 |
| BNF | Backus，1959，描述 ALGOL 58 | **與 CFG 等價**；是 metalanguage |
| Nonterminal | `< >` 包住，可再展開 | 又稱 abstraction |
| Terminal | lexeme / token | 不可再展開 |
| Start symbol | 特殊 nonterminal | 推導的起點 |
| 規則 | LHS → RHS | 一個 LHS 可有多個 RHS，用 `\|` 分隔 |
| 串列 | 用遞迴描述 | BNF 沒有 `…` |
| Derivation | start symbol → sentence | `=>` 讀作 derives |
| Sentential form vs. sentence | 推導中每個字串 vs. 全為 terminal | sentence 是 sentential form 的特例 |
| Parse tree | 內部節點 nonterminal，葉節點 terminal | 不記錄展開順序 |
