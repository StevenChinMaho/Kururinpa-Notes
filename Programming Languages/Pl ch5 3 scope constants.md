# 5.5–5.9 範圍、參考環境與具名常數（Scope, Referencing Environments & Named Constants）

> Programming Languages — Chapter 5: Names, Bindings, Type Checking, and Scopes（NUK 資工系 余亞儒教授講義，Slides 5-40 ~ 5-64）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Scope 定義 | 變數可見的敘述範圍；nonlocal 變數 |
| Static scope | 依程式碼結構決定，執行前即可確定 |
| Static ancestor / parent | 外層範圍與最近的外層範圍 |
| Hidden variables | 內層同名變數遮蔽外層變數 |
| Blocks、宣告順序 | 在程式單元中建立靜態範圍 |
| Global scope | C 的 declaration（`extern`）vs. definition |
| Static scoping 的問題 | 常導致過多全域變數 |
| Dynamic scope | 依呼叫順序決定，只能在執行時確定 |
| Scope vs. Lifetime | 兩者相關但不同 |
| Referencing environment | 某敘述可見的所有名稱 |
| Named constants、初始化 | 只綁定一次值的變數 |

---

## 1. 範圍（Scope）

- **Scope**：變數**可見（visible）**的敘述範圍
- **Nonlocal variables**：在某程式單元中**可見但不是在該處宣告**的變數
- 語言的 **scope rules** 決定名稱的引用如何對應到變數，分為 **static** 與 **dynamic**

---

## 2. 靜態範圍（Static Scope）

### 2.1 定義與搜尋方式

- 變數的範圍可在**執行前**靜態決定
- 要把名稱對應到變數，必須找到它的宣告
- **搜尋順序**：先找**區域**宣告，再往**越來越大的外層範圍**找，直到找到為止

| 術語 | 定義 |
|---|---|
| **Static ancestors** | 包住某範圍的所有外層靜態範圍 |
| **Static parent** | 最近的 static ancestor |

### 2.2 Ada 範例

```ada
procedure Big is
    X : Integer;              -- Big 的 X
    procedure Sub1 is
        X : Integer;          -- Sub1 的 X
    begin -- of Sub1
        ...
    end; -- of Sub1
    procedure Sub2 is
    begin -- of Sub2
        ... X ...             -- 哪一個 X？
    end; -- of Sub2
begin -- of Big
    ...
end; -- of Big
```

- Sub2 自己沒有宣告 X → 往外找 static parent **Big** → 使用 **Big 的 X**
- Big 是 Sub2 的 static ancestor，也是 static parent
- Sub1 與 Sub2 是並列的，Sub2 **看不到** Sub1 的 X

### 2.3 C 範例：全域與區域同名

```c
/* aa.c */
#include <stdio.h>
int x = 1;                          // 全域 x 的 definition
void aa_test() {
    printf("aa_test():%d\n", x);    // 使用全域 x
}

/* main.c */
#include <stdio.h>
#include <stdlib.h>
extern int x;                       // 全域 x 的 declaration

void test1() {
    int x = 11;                     // 區域 x，遮蔽全域 x
    printf("test1():%d\n", x);
}
void test2() {
    x = 12;                         // 沒有區域宣告 → 修改全域 x
    printf("test2():%d\n", x);
}
int main(int argc, char** argv) {
    printf("main():%d\n", x);       // 全域 x = 1
    aa_test();                      // 全域 x = 1
    test1();                        // test1 的區域 x = 11
    aa_test();                      // 全域 x 仍為 1
    test2();                        // 全域 x 改成 12
    aa_test();                      // 全域 x = 12
    printf("main():%d\n", x);       // 全域 x = 12
    return EXIT_SUCCESS;
}
```

輸出：`1, 1, 11, 1, 12, 12, 12`

### 2.4 隱藏變數（Hidden Variables）

- 內層有**更近的同名變數**時，外層變數會被隱藏
  - 例：Big 的 X 在 Sub1 中被隱藏
  - Java 與 C# 中，區塊內宣告與外層區域變數同名是**不合法**的
- C++ 與 Ada 可以存取被隱藏的變數：

| 語言 | 寫法 |
|---|---|
| Ada | `unit.name`（如 `Big.X`） |
| C++ | `class_name::name`（全域變數用 `::name`） |

### 2.5 區塊（Blocks）

- 在程式單元**內部**建立靜態範圍的方法，源自 **ALGOL 60**

```c
for (...) {
    int index;      // index 的 scope 只在這個 block 內
    ...
}
```

```ada
declare LCL : FLOAT;
begin
    ...             -- LCL 的 scope
end
```

### 2.6 宣告順序（Declaration Order）

有些語言允許宣告出現在任何敘述可以出現的地方：

| 語言 | 變數 scope |
|---|---|
| C99、C++、Java | 從**宣告處到 block 結尾** |
| C# | **整個 block**（但仍必須先宣告才能使用） |

### 2.7 全域範圍（Global Scope）

- 有些語言中，在所有函數外定義的變數成為**全域變數**
- C 與 C++ 的全域資料分成兩種：

| | 是否配置記憶體 | 寫法 |
|---|---|---|
| **Declaration** | **否**，只指定型別等屬性 | `extern int flag;` |
| **Definition** | **是** | `int flag;` |

```c
/* module1.c */  int flag;          // definition：只出現一次
/* module2.c */  extern int flag;   // declaration
/* module3.c */  extern int flag;   // declaration
```

> 用 `extern` 宣告可以避免 `flag` 被重複定義。

---

## 3. 靜態範圍的評價（Evaluation of Static Scoping）

### 3.1 範例程式結構

MAIN 呼叫 A 與 B；A 呼叫 C 與 D；B 呼叫 E。巢狀結構如下：

```text
MAIN
├── A
│   ├── C
│   └── D
└── B
    └── E
```

### 3.2 資料可見性（Data Accessibility）

| 資料 \ 誰能存取 | MAIN | A | B | C | D | E |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| MAIN's data | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| A's local | | ✓ | | ✓ | ✓ | |
| B's local | | | ✓ | | | ✓ |
| C's local | | | | ✓ | | |
| D's local | | | | | ✓ | |
| E's local | | | | | | ✓ |

> 規則：每個單元看得到**自己**與**所有外層（static ancestors）**的資料。

### 3.3 Call Graph 的問題

| | 說明 |
|---|---|
| Desired call graph | MAIN→A、MAIN→B、A→C、A→D、B→E |
| Potential call graph | 巢狀結構允許更多呼叫（如 E 可以呼叫 A、C 可以呼叫 D 等） |

- 程式設計師可能**誤呼叫**原本不該被呼叫的副程式
- **資料存取過多**：例如 MAIN 中宣告的變數對所有副程式都是全域的

### 3.4 規格變更時的問題

假設規格變更，**E 必須存取 D 中的資料**：

| 解法 | 問題 |
|---|---|
| 把 E 移進 D 裡面 | E 就無法再存取 B 的變數（但 E 可能需要） |
| 把 E 需要的資料從 D 移到 MAIN | **所有**程序都能存取這些資料 |

> 結論：**Static scoping 常會助長大量全域變數。**

---

## 4. 動態範圍（Dynamic Scope）

- 依據程式單元的**呼叫順序**決定，而非程式碼的文字配置（**temporal** vs. **spatial**）
- 對變數的引用沿著**呼叫鏈往回搜尋**，找到最近的宣告
- 範圍**只能在執行時**決定

### 4.1 範例（同 2.2 的 Ada 程式）

Sub2 中引用 `X`：

| 呼叫順序 | Dynamic scope 下的 X | Static scope 下的 X |
|---|---|---|
| Big → Sub1 → Sub2 | **Sub1 的 X**（呼叫鏈上最近的是 Sub1） | Big 的 X |
| Big → Sub2 | **Big 的 X** | Big 的 X |

> Static scope 的答案永遠固定；dynamic scope 的答案**取決於誰呼叫了 Sub2**。

### 4.2 評價

| | 內容 |
|---|---|
| 優點 | **方便**（被呼叫者自動看得到呼叫者的變數） |
| 缺點 | **易讀性差**（看程式碼無法判斷變數指向哪個宣告） |

---

## 5. Scope 與 Lifetime

兩者有時密切相關，但是**不同的概念**：

| | 意義 | 性質 |
|---|---|---|
| Scope | 空間上：哪些敘述**看得到** | 文字（textual）概念 |
| Lifetime | 時間上：何時**綁定到記憶體** | 時間（temporal）概念 |

```c
int fun1(int a, float b) {
    static int cnt;   // scope 是 local（只有 fun1 看得到）
                      // lifetime 是 global（整個程式執行期間都存在）
    int i, j;
    ...
}
```

---

## 6. 參考環境（Referencing Environments）

- **Referencing environment**：某敘述中**所有可見名稱**的集合
- **Active subprogram**：已開始執行但尚未結束的副程式

| 範圍規則 | Referencing environment |
|---|---|
| Static scope | 區域變數 + **所有外層範圍**中可見的變數 |
| Dynamic scope | 區域變數 + **所有 active 副程式**中可見的變數 |

### 6.1 Ada 範例（Static Scope）

```ada
procedure Example is
    A, B : Integer;
    procedure Sub1 is
        X, Y : Integer;
    begin
        ...                -- (1)
    end;
    procedure Sub2 is
        X : Integer;
        procedure Sub3 is
            X : Integer;
        begin
            ...            -- (2)
        end;
    begin
        ...                -- (3)
    end;
begin
    ...                    -- (4)
end;
```

| 位置 | Referencing environment |
|---|---|
| (1) Sub1 中 | Sub1 的 X、Y；Example 的 A、B |
| (2) Sub3 中 | **Sub3 的 X**（Sub2 的 X 被隱藏）；Example 的 A、B |
| (3) Sub2 中 | Sub2 的 X；Example 的 A、B |
| (4) Example 中 | Example 的 A、B |

---

## 7. 具名常數（Named Constants）

- 只綁定**一次**值的變數
- 優點：**readability** 與 **reliability**
- 用來**參數化**程式：改一處就能全部生效

```java
final int len = 100;                 // 只要改這裡

int[] intList    = new int[len];     // 取代 new int[100]
String[] strList = new String[len];  // 取代 new String[100]
for (index = 0; index < len; index++) { ... }
for (index = 0; index < len; index++) { ... }
```

### 7.1 Named Constant 的綁定

| 綁定方式 | 說明 |
|---|---|
| Static | 稱為 **manifest constants** |
| Dynamic | 執行時才計算值 |

| 語言 | 允許的值 |
|---|---|
| FORTRAN 90 | 只能是**常數值運算式** |
| Ada、C++、Java | **任何**運算式 |

### 7.2 變數初始化（Variable Initialization）

- 變數綁定到記憶體的**同時**綁定一個值，稱為 **initialization**
- 通常在定義敘述中完成

```java
int sum = 0;
```

---

## 8. 第 5 章總結（Summary）

| 主題 | 結論 |
|---|---|
| 名稱的設計議題 | 大小寫、名稱與特殊字的關係 |
| 變數 | 由六個屬性描述：name、address、value、type、lifetime、scope |
| Binding | 屬性與程式實體的關聯 |
| Scalar 變數分類 | static、stack-dynamic、explicit heap-dynamic、implicit heap-dynamic |
| Strong typing | 能偵測**所有**型別錯誤 |

> 章節目錄列出 Type Checking、Strong Typing、Type Compatibility，但這份講義只在 Summary 提到 strong typing 的定義，沒有獨立投影片。如果小考會考到，可能需要看課本 Ch5（或 Ch6）對應小節。

---

## 重點整理

| 概念 | 重點 | 注意事項 |
|---|---|---|
| Scope | 變數可見的範圍 | nonlocal：可見但不在本處宣告 |
| Static scope | 依文字結構，執行前決定；由內往外找 | static parent = 最近的外層 |
| Hidden variable | 內層同名變數遮蔽外層 | Ada `unit.name`、C++ `::name` |
| Block | 程式單元內建立 scope | 源自 ALGOL 60 |
| 宣告順序 | C99/C++/Java：宣告處到 block 尾；C#：整個 block | |
| `extern` | declaration，不配置記憶體 | 避免重複定義 |
| Static scoping 缺點 | 助長全域變數、可能誤呼叫 | |
| Dynamic scope | 依呼叫鏈，執行時決定 | 方便但易讀性差 |
| Scope vs. Lifetime | 空間 vs. 時間 | C `static` 區域變數：scope local、lifetime global |
| Referencing env. | 某敘述所有可見名稱 | static：外層範圍；dynamic：active 副程式 |
| Named constant | 只綁定一次值 | static 綁定 = manifest constant |
| Initialization | 配置記憶體時同時綁定值 | |
| Strong typing | 偵測所有型別錯誤 | 講義僅在 Summary 提及 |
