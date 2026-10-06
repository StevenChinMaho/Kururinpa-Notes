# 1.3 語言評估準則（Language Evaluation Criteria）

> Programming Languages — Chapter 1: Preliminaries（NUK 資工系 余亞儒教授講義，Slides 1-10 ~ 1-17）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| 易讀性（Readability） | 程式是否容易閱讀與理解 |
| 易寫性（Writability） | 語言是否容易用來撰寫程式 |
| 可靠度（Reliability） | 程式是否符合規格正確執行 |
| 成本（Cost） | 語言使用的總成本 |
| 其他準則 | Portability、Generality、Well-definedness |

---

## 1. 四大評估準則

| 準則 | 定義 |
|---|---|
| Readability（易讀性） | the ease with which programs can be **read and understood** |
| Writability（易寫性） | the ease with which a language can be used to **create programs** |
| Reliability（可靠度） | **conformance to specifications**（程式能依規格執行） |
| Cost（成本） | the ultimate **total cost** |

---

## 2. 易讀性（Readability）

### 2.1 整體單純度（Overall Simplicity）

**(1) 功能與結構數量適中（A manageable set of features and constructs）**
功能太多，程式設計師通常只學其中一部分，讀別人程式時容易看不懂。

**(2) 少量功能重複（Few feature multiplicity）**
同一操作有多種寫法，會增加閱讀負擔：

```c
count = count + 1;   // 四種寫法，效果相同（單獨成句時）
count += 1;
count++;
++count;
```

**(3) 最少的運算子多載（Minimal operator overloading）**
同一運算子有多種意義稱為 **operator overloading**。適度使用（如 `+` 同時用於 int 與 float）合理；濫用（例如自訂 `+` 代表陣列總和）則降低易讀性。

### 2.2 正交性（Orthogonality）

- 一小組**基本結構（primitive constructs）**能以少量方式組合，建構出語言的控制與資料結構
- **每一種可能的組合都合法**（且意義一致、沒有例外）

非正交的例子：IBM 組合語言

```asm
A   Reg1, memory_cell   ; Reg1 <- contents(Reg1) + contents(memory_cell)
AR  Reg1, Reg2          ; Reg1 <- contents(Reg1) + contents(Reg2)
```

加法依運算元種類分成兩個不同指令：`A` 只能用「暫存器 + 記憶體」，`AR` 只能用「暫存器 + 暫存器」，不能任意組合。

> 正交性的核心：**組合規則少、例外少** → 需要記的特例少 → 容易閱讀。

### 2.3 控制敘述（Control Statements）

具備熟知的控制結構（如 `while`），避免依賴 `goto` 跳來跳去。

### 2.4 資料型別與結構（Data Types and Structures）

提供足夠的機制定義資料型別與結構。

```c
timeOut = 1;      // 沒有 Boolean 型別：1 代表什麼意思不明確
timeOut = true;   // 有 Boolean 型別：語意清楚
```

### 2.5 語法考量（Syntax Considerations）

| 項目 | 說明 | 例子 |
|---|---|---|
| Identifier forms | 識別字組成規則要有彈性（長度限制過短會降低易讀性） | 允許 `studentCount` 而非只能用 `sc` |
| Special words & compound statements | 保留字與複合敘述的結束方式 | Ada 以 `end if`、`end loop` 結束；C 一律用 `}`，難分辨是哪個區塊結束 |
| Form and meaning | 結構形式應能反映意義，關鍵字要有意義 | UNIX 的 `grep` 用於搜尋文字，但名稱本身不易看出用途 |

---

## 3. 易寫性（Writability）

### 3.1 單純度與正交性（Simplicity and Orthogonality）

- 結構少、基本元素少、組合規則少 → 容易撰寫
- **正交性過高反而會傷害易寫性**：幾乎任何組合都合法時，寫錯也不會被偵測出來

### 3.2 支援抽象化（Support for Abstraction）

能定義並使用複雜的**結構**（如 struct）或**操作**（如函數），同時隱藏細節。

```c
double avg(int a[], int n);   // 定義一次，之後只需呼叫，不必重寫計算細節
```

### 3.3 表達性（Expressivity）

提供相對方便的方式描述運算。

```c
count++;             // 較方便、較短
count = count + 1;   // 效果相同
```

---

## 4. 可靠度（Reliability）

| 項目 | 說明 |
|---|---|
| 型別檢查（Type checking） | 檢查型別錯誤；**編譯期檢查**比執行期檢查成本低 |
| 例外處理（Exception handling） | 攔截執行期錯誤並採取修正措施 |
| 別名（Aliasing） | 同一記憶體位置有兩個以上不同的存取方式 → 危險 |
| 易讀與易寫性 | 語言若無法以「自然」方式表達演算法，就必須用「不自然」的寫法，可靠度隨之下降 |

別名的例子：

```cpp
int x = 10;
int &r = x;    // r 是 x 的別名（reference）
int *p = &x;   // p 也指向 x
r = 20;        // x 變成 20
*p = 30;       // x 變成 30：同一記憶體有三個名字，容易在不知情下被修改
```

---

## 5. 成本（Costs）

| 成本來源 | 說明 |
|---|---|
| Training programmers | 訓練程式設計師使用該語言 |
| Writing programs | 語言越貼近應用領域，撰寫成本越低 |
| Compiling programs | 編譯所需成本 |
| Executing programs | 執行成本；在正式環境（production）中，值得多花編譯成本做最佳化 |
| Language implementation system | 是否有免費的編譯器（如 Java） |
| Reliability | 可靠度差 → 系統失效成本高 |
| Maintaining programs | 維護成本，通常是最大宗，與易讀性高度相關 |

---

## 6. 其他評估準則（Other Evaluation Criteria）

| 準則 | 說明 |
|---|---|
| Portability（可攜性） | 程式從一個實作搬到另一個實作的容易程度 |
| Generality（通用性） | 適用於廣泛應用的程度 |
| Well-definedness | 語言官方定義的完整性與精確性 |

---

## 重點整理

### 語言特性對評估準則的影響（常見考點）

| 語言特性 | Readability | Writability | Reliability |
|---|:---:|:---:|:---:|
| Simplicity | ✓ | ✓ | ✓ |
| Orthogonality | ✓ | ✓ | ✓ |
| Control statements | ✓ | ✓ | ✓ |
| Data types | ✓ | ✓ | ✓ |
| Syntax design | ✓ | ✓ | ✓ |
| Support for abstraction | | ✓ | ✓ |
| Expressivity | | ✓ | ✓ |
| Type checking | | | ✓ |
| Exception handling | | | ✓ |
| Restricted aliasing | | | ✓ |

> 規律：Readability 的因素都會影響 Writability 與 Reliability；越往下的特性只影響 Reliability。

### 名詞速查

| 名詞 | 一句話定義 |
|---|---|
| Feature multiplicity | 同一操作有多種寫法 |
| Operator overloading | 同一運算子有多種意義 |
| Orthogonality | 少量基本結構，任意組合皆合法 |
| Aliasing | 同一記憶體有多個存取名稱 |
| Expressivity | 能方便地描述運算 |
