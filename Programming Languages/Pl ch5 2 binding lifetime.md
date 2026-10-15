# 5.4 綁定的概念：型別綁定、儲存綁定與生命期（The Concept of Binding）

> Programming Languages — Chapter 5: Names, Bindings, Type Checking, and Scopes（NUK 資工系 余亞儒教授講義，Slides 5-14 ~ 5-39）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Binding 與 binding time | 屬性與實體建立關聯的時機 |
| 各種 binding time | design、implementation、compile、load、link、run time |
| Static vs. dynamic binding | 執行前固定 vs. 執行中產生或改變 |
| Static type binding | explicit / implicit declaration、type inference |
| Dynamic type binding | 以指派決定型別（JavaScript、PHP） |
| Storage binding | allocation 與 deallocation |
| Lifetime 四類變數 | static、stack-dynamic、explicit heap-dynamic、implicit heap-dynamic |

---

## 1. 綁定（Binding）

### 1.1 定義

**Binding** 是一種關聯（association），可以是：

- 兩個東西之間
- 一個**屬性**與一個**實體**之間（如變數與其型別）
- 一個**運算**與一個**符號**之間（如 `+` 與加法）

**Binding time**：binding 發生（或某個實作決定被做出）的時間。

### 1.2 可能的 Binding Times

| Binding time | 例子 |
|---|---|
| Language design time | 運算子符號綁定到運算（`*` 代表乘法） |
| Language implementation time | 浮點數型別綁定到機器相關的表示法（如 little / big endian） |
| Compile time | Java 中變數綁定到某個資料型別 |
| Load time | FORTRAN 77 變數（或 C 的 static 變數）綁定到記憶體單元 |
| Link time | 呼叫的函式庫副程式綁定到其程式碼 |
| Run time | 非 static 的區域變數綁定到記憶體單元 |

> 時間順序：**design → implementation → compile → link → load → run**

### 1.3 範例一：`count = count + 5;`

| 項目 | Binding time |
|---|---|
| `count` 的型別 | compile time |
| `count` 可能的值的集合 | language implementation time |
| `+` 的意義 | compile time（運算元型別決定後才知道是整數還是浮點數加法） |
| 字面常數 `5` 的內部表示法 | language implementation time |
| `count` 的值 | run time |

### 1.4 範例二：C 指令 `y = x + 100;`（講義例題）

| 項目 | Binding time | 理由 |
|---|---|---|
| a. `=` 為指定運算子 | 語言設計時 | |
| b. `+` 為加法運算子 | 語言設計時 | |
| c. `+` 為**整數**加法 | **compile time** | 要等運算元型別決定，而型別在編譯時決定 |
| d. `100` 表示整數 100 | 語言設計時 | |
| e. 整數 100 的**內部表示法** | **語言實作時** | 涉及機器相關的實作 |
| f. 100 為整數 | 語言設計時 | 符號代表的意義 |
| g. x、y 的資料型態 | compile time | |
| h. x、y 的值 | run time | |
| i. x、y 的位址 | run time | |
| j. x、y **可以宣告的型態** | 語言設計時 | 語言提供哪些型別是設計時決定 |

> 判斷技巧：**「符號代表什麼」→ 設計時**；**「在機器上怎麼表示」→ 實作時**；**「這個變數是什麼型別、`+` 是哪種加法」→ 編譯時**；**「值、位址」→ 執行時**。

### 1.5 Static 與 Dynamic Binding

| 類型 | 定義 |
|---|---|
| **Static** binding | 在**執行前**第一次發生，且整個執行過程中**不會改變** |
| **Dynamic** binding | 在**執行中**第一次發生，或在執行中**可以改變** |

---

## 2. 型別綁定（Type Binding）

變數在被引用前必須先綁定到某個資料型別。兩個問題：

1. 何時綁定？→ static 或 dynamic
2. 若是 static，型別如何指定？→ explicit 或 implicit declaration

### 2.1 Static Type Binding

| 方式 | 定義 | 例子 |
|---|---|---|
| **Explicit declaration** | 用程式敘述宣告變數型別 | `int x;` |
| **Implicit declaration** | 用預設機制決定型別（變數第一次出現時） | FORTRAN、PL/I、BASIC、Perl |

Implicit declaration：

- 優點：**writability**
- 缺點：**reliability**（Perl 的問題較小）

**Fortran 的規則：**

| 名稱開頭字母 | 隱含型別 |
|---|---|
| `I`、`J`、`K`、`L`、`M`、`N` | Integer |
| 其他 | Real |

**Perl 的規則：用名稱前綴符號決定**

| 前綴 | 類型 |
|---|---|
| `$` | scalar，可存字串或數值 |
| `@` | array |
| `%` | hash（索引是**字串**，不是整數） |

```perl
my $x = "abc";        # scalar 存字串
my $x = 123;          # scalar 存整數
my $x = 4.56;         # scalar 存浮點數
$hash{'i1'} = "aaa";  # hash 以字串 'i1' 當索引
```

**Type inference（靜態）：** 由情境（context）推斷型別，例如 C# 的 `var`：

```csharp
var sum   = 0;       // int
var total = 0.0;     // double
var name  = "Fred";  // string
```

### 2.2 Dynamic Type Binding

- JavaScript、PHP
- 型別由**指派敘述**決定

```javascript
list = [2, 4.33, 6, 8];   // list 現在是 array
list = 17;                // list 變成 integer
```

| | 內容 |
|---|---|
| 優點 | **flexibility**：可寫出 generic program units |
| 缺點 1 | **較不可靠**：編譯期無法偵測型別錯誤 |
| 缺點 2 | **實作成本高**：需要執行期型別檢查 |

**Generic program unit 範例：**

```c
// dynamic type binding：一份就能處理任何型別
swap(a, b) { c = a; a = b; b = c; }

// static type binding：每種型別要各寫一份
swap(int a, int b)     { int c = a;   a = b; b = c; }
swap(float a, float b) { float c = a; a = b; b = c; }
swap(char a, char b)   { char c = a;  a = b; b = c; }
```

**錯誤無法被偵測的例子：**

```javascript
// i、x 目前存數值，y 目前存 array
i = x;   // 原本想寫這個
i = y;   // 打錯字：不會報錯，i 直接變成 array
```

### 2.3 Type Inference（ML、Miranda、Haskell）

型別不由指派決定，而是由**引用的情境**推斷：

```ml
fun circumf(r) = 3.14159 * r * r;   (* 與實數相乘 → r 與回傳值是 real *)
fun times10(x) = 10 * x;            (* 與整數相乘 → x 與回傳值是 int *)
```

> 講義將此頁標為「Type Inference (Dynamic)」，但 ML、Haskell 的型別推斷是在**編譯期**完成，屬於 static type binding（Sebesta 課本亦同）。如果考題問 ML 屬於哪種，建議答「靜態型別推斷」並註明依據。

---

## 3. 儲存綁定與生命期（Storage Bindings & Lifetime）

### 3.1 Storage Binding

| 動作 | 說明 |
|---|---|
| **Allocation** | 從可用記憶體池中取出一個單元，指派給變數 |
| **Deallocation** | 把單元放回記憶體池 |

**Lifetime**：變數綁定到某個記憶體單元的期間，也就是**從 allocation 到 deallocation** 的時間。

依 lifetime 分成四類：

1. Static
2. Stack-dynamic
3. Explicit heap-dynamic
4. Implicit heap-dynamic

### 3.2 Static Variables

- **執行開始前**就綁定到記憶體單元，整個執行過程都綁定在**同一個**單元
- 例：所有 FORTRAN 77 變數、C 的 `static` 變數與全域變數

| | 內容 |
|---|---|
| 優點 | **效率**（direct addressing、無執行期負擔）；支援 **history-sensitive** 副程式 |
| 缺點 | **彈性降低**（**不支援遞迴**）；記憶體**無法共用** |

> History-sensitive：副程式結束後，變數的值仍會保留到下次呼叫。

### 3.3 Stack-Dynamic Variables

- **執行到宣告敘述時**（declaration is elaborated）才建立儲存綁定
- 若為 scalar，**除了 address 以外**的屬性都是靜態綁定
- 例：C 副程式與 Java method 中的區域變數

| | 內容 |
|---|---|
| 優點 | **支援遞迴**；**節省記憶體**（不同副程式可共用 stack 空間） |
| 缺點 | allocation / deallocation 的負擔；副程式**無法 history sensitive**；**間接定址**較慢 |

組合語言中，區域變數以 EBP 為基準間接存取：

```c
void MySub() {
    int x = 10;   // [EBP-4]
    int y = 20;   // [EBP-8]
}
```

### 3.4 C 範例：判斷變數類別

```c
#include <stdio.h>

int sum;                     // static：執行前即配置，程式結束才釋放

int fun1(int a, float b) {   // a、b：stack-dynamic
    static int cnt;          // static：離開 fun1 後值仍保留（history-sensitive）
    int i, j;                // stack-dynamic：執行到宣告時才配置，fun1 結束時回收
    ...
}
```

### 3.5 Explicit Heap-Dynamic Variables

- 由程式設計師下的**明確指令**配置與釋放，在執行時生效
- 只能透過 **pointer 或 reference** 存取（沒有名稱）
- 例：C++ 的 `new` / `delete` 產生的動態物件、Java 的所有物件

```cpp
int *intnode;
...
intnode = new int;      // allocated
...
delete intnode;         // deallocated
```

| | 內容 |
|---|---|
| 優點 | 提供**動態儲存管理** |
| 缺點 | **沒效率且不可靠**（dangling pointer、memory leak） |

### 3.6 Implicit Heap-Dynamic Variables

- 由**指派敘述**觸發配置與釋放
- 例：APL 的所有變數；Perl、JavaScript 的所有字串與陣列

```javascript
highs = [74, 84, 86, 90, 71];   // 指派時自動配置 highs 的記憶體
```

| | 內容 |
|---|---|
| 優點 | **flexibility** |
| 缺點 | **沒效率**（所有屬性都是動態的）；**失去錯誤偵測能力** |

---

## 重點整理

### 四類變數比較

| 類別 | 配置時機 | 配置方式 | 例子 | 優點 | 缺點 |
|---|---|---|---|---|---|
| Static | 執行前 | 自動 | C `static`、全域、FORTRAN 77 | 效率、history-sensitive | 不支援遞迴、無法共用 |
| Stack-dynamic | 執行到宣告時 | 自動（stack） | C/Java 區域變數 | 支援遞迴、省記憶體 | 配置負擔、間接定址、無法 history-sensitive |
| Explicit heap-dynamic | 執行時，程式設計師下指令 | `new` / `delete` | C++ 動態物件、Java 物件 | 動態儲存管理 | 沒效率、不可靠 |
| Implicit heap-dynamic | 執行時，指派觸發 | 自動（heap） | APL、JS/Perl 字串與陣列 | 彈性 | 沒效率、失去錯誤偵測 |

### 型別綁定比較

| 方式 | 時機 | 例子 | 優點 | 缺點 |
|---|---|---|---|---|
| Explicit declaration | static | C、Java | 可靠 | 要寫宣告 |
| Implicit declaration | static | FORTRAN、Perl | writability | reliability |
| Type inference | static | C# `var`、ML、Haskell | 少寫型別 | |
| Dynamic type binding | dynamic | JavaScript、PHP | 彈性、generic | 不可靠、實作成本高 |
