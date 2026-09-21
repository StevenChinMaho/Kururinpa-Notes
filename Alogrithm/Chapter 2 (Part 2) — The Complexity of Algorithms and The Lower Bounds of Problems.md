# Chapter 2 (Part 2) — The Complexity of Algorithms and The Lower Bounds of Problems

> Introduction to the Design and Analysis of Algorithms: A Strategic Approach（R.C.T. Lee, S.S. Tseng, R.C. Chang, Y.T. Tsang）
> 課程投影片：Algorithms（洪宗貝 Tzung-Pei Hong）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Straight selection sort | 每次挑最小值就定位；分析 comparison 與 flag change |
| Flag change 平均次數 | 以 $P_n(k)$ → $X_n$ → $A(n)$ 推導出 $O(n\log n)$ |
| Harmonic number | $H_n = 1 + \frac12 + \cdots + \frac1n \approx \ln n$ |
| Quick sort | 雙指標實作；Best / Average $O(n\log n)$、Worst $O(n^2)$ |
| Rank finding（1-D / 2-D） | 以 divide-and-conquer 在 $O(n\log^2 n)$ 求 2-D rank |
| $O$、$\Omega$、$\Theta$ | at most / at least / exact |
| 問題的 lower bound | 如何取得、與最佳演算法的三種關係、optimal algorithm |

---

## 1. 直接選擇排序（Straight Selection Sort）

### 1.1 概念

每次挑出剩餘元素中**最小**的，放到正確位置，並與該位置原本的數**交換（exchange）**。

以 `7 5 1 4 3` 為例：

| 回合 | 結果 | 比較次數 |
|---|---|---|
| 1 | `1 5 7 4 3`（1 與 7 交換） | 4 |
| 2 | `1 3 7 4 5`（3 與 5 交換） | 3 |
| 3 | `1 3 4 7 5`（4 與 7 交換） | 2 |
| 4 | `1 3 4 5 7`（5 與 7 交換） | 1 |
| | | **共 10 次** $= \frac{n(n-1)}{2}$ |

### 1.2 實作：用 flag 記錄最小值位置

`f`（flag）記錄**目前看到的最小值位置**。
例：`7 5 1 4 3`，$j = 1$ 時：

| k | 比較 | f |
|---|---|---|
| 起始 | — | 1（值 7） |
| 2 | 5 < 7 | 2 |
| 3 | 1 < 5 | 3 |
| 4 | 4 < 1？否 | 3 |
| 5 | 3 < 1？否 | 3 |

→ 第 3 個位置最小，將位置 $f$ 與位置 $j$ 的值互換。

### 1.3 演算法

```pascal
For j := 1 to n-1 do             { 找第 j 小的元素就定位 }
Begin
    f := j                       { 不算 flag change }
    For k := j+1 to n do
        If ak < af then f := k   { comparison；成立時為 flag change }
    aj ↔ af                      { 交換 }
End
```

### 1.4 兩種操作

| 操作 | 是否隨資料變動 | 計算方式 |
|---|---|---|
| Comparison（`ak < af`） | **不隨資料變**，永遠 $\frac{n(n-1)}{2}$ 次 | $O(n^2)$ |
| Flag change | **隨資料變** | 只算 `f := k`，**不算** `f := j` |

---

## 2. Flag Change 的分析

### 2.1 定義 $f(a_1, \dots, a_n)$

$f(a_1, a_2, \dots, a_n)$：在 $a_1 \sim a_n$ 中找最小值所需的 flag change 次數。

**n = 2**（2 種排列）

| 排列 | flag change |
|---|---|
| (1, 2) | 0 |
| (2, 1) | 1 |

**n = 3**（6 種排列）

| 排列 | flag change | 說明 |
|---|---|---|
| (1, 2, 3) | 0 | |
| (1, 3, 2) | 0 | |
| (2, 1, 3) | 1 | |
| (2, 3, 1) | 1 | f = 1 變 f = 3 |
| (3, 1, 2) | 1 | |
| (3, 2, 1) | 2 | f = 1 → 2 → 3 |

### 2.2 遞迴關係

最後一個元素 $a_n$ 只有在它是**全體最小值**時才會造成一次 flag change：

$$f(a_1, \dots, a_n) = \begin{cases} 1 + f(a_1, \dots, a_{n-1}) & \text{if } a_n = 1 \\ f(a_1, \dots, a_{n-1}) & \text{if } a_n \ne 1 \end{cases}$$

### 2.3 三個要求的量

| 符號 | 意義 |
|---|---|
| $P_n(k)$ | 在 n 個元素中找最小值，**恰好**發生 k 次 flag change 的機率 |
| $X_n$ | 在 n 個元素中找**一個**最小值的平均 flag change 次數 |
| $A(n)$ | 整個 selection sort（n 個元素）的平均 flag change 次數，即**所求答案** |

> **解題步驟**：求 $P_n(k)$ → 求 $X_n$ → 求 $A(n)$

**n = 3 的例子**：

$$P_3(0) = \frac{2}{6},\quad P_3(1) = \frac{3}{6},\quad P_3(2) = \frac{1}{6}$$

$$X_3 = \sum_{k=0}^{n-1} k P_n(k) = 0 \times \frac26 + 1 \times \frac36 + 2 \times \frac16 = \frac56$$

### 2.4 求 $P_n(k)$

由 2.2 的遞迴關係：

$$P_n(k) = P(a_n = 1)\,P_{n-1}(k-1) + P(a_n \ne 1)\,P_{n-1}(k)$$

若每種排列機率相同，則 1 出現在每個位置的機率相同：

$$P(a_n = 1) = \frac1n,\qquad P(a_n \ne 1) = \frac{n-1}{n}$$

$$\boxed{P_n(k) = \frac1n P_{n-1}(k-1) + \frac{n-1}{n} P_{n-1}(k)}$$

**初始條件**

| 條件 | 值 | 原因 |
|---|---|---|
| $n = 1$，$k = 0$ | $P_1(0) = 1$ | 只有一個元素，不用換 flag |
| $n = 1$，$k \ne 0$ | $P_1(k) = 0$ | |
| $k = n$ | $P_n(n) = 0$ | 最多只能改變 $n-1$ 次 flag（例：3, 2, 1） |

**n = 2**

$$P_2(1) = \frac12 P_1(0) + \frac12 P_1(1) = \frac12 \cdot 1 + \frac12 \cdot 0 = \frac12$$

$$P_2(0) = 1 - P_2(1) = \frac12$$

（也可直接從 (1,2)、(2,1) 看出。）

$$X_2 = 0 \times \frac12 + 1 \times \frac12 = \frac12 = \left(\frac11 + \frac12\right) - 1$$

**n = 3**

$$P_3(1) = \frac13 P_2(0) + \frac23 P_2(1) = \frac13 \cdot \frac12 + \frac23 \cdot \frac12 = \frac36$$

$$P_3(2) = \frac13 P_2(1) + \frac23 P_2(2) = \frac13 \cdot \frac12 + \frac23 \cdot 0 = \frac16$$

$$P_3(0) = 1 - P_3(1) - P_3(2) = \frac26$$

$$X_3 = \frac36 + \frac26 = \frac12 + \frac13 = \left(\frac11 + \frac12 + \frac13\right) - 1$$

### 2.5 求 $X_n$

觀察規律：

$$X_n = \sum_{k=1}^{n-1} k P_n(k) = \frac12 + \frac13 + \cdots + \frac1n = H_n - 1$$

其中 $H_n = 1 + \frac12 + \cdots + \frac1n$ 為第 n 個 **harmonic number**。

#### 證明（數學歸納法）

**(1) n = 2**：$X_2 = H_2 - 1 = \frac12$，前面已證 ✓

**(2) 假設 n = m 成立**（$m \ge 2$）：$X_m = \sum_{k} k P_m(k) = H_m - 1$

**(3) 證 n = m + 1**：代入 $P_{m+1}(k) = \frac{1}{m+1} P_m(k-1) + \frac{m}{m+1} P_m(k)$

$$X_{m+1} = \sum_{k=1}^{m} k P_{m+1}(k) = \frac{1}{m+1}\sum_{k} k\,P_m(k-1) + \frac{m}{m+1}\sum_{k} k\,P_m(k)$$

- 第一項：令 $j = k-1$，$\sum_k k P_m(k-1) = \sum_j (j+1) P_m(j) = X_m + \underbrace{\sum_j P_m(j)}_{=1}$
- 第二項：$\sum_k k P_m(k) = X_m$

$$X_{m+1} = \frac{X_m + 1}{m+1} + \frac{m X_m}{m+1} = X_m + \frac{1}{m+1} = H_m - 1 + \frac{1}{m+1} = H_{m+1} - 1 \checkmark$$

> 投影片的做法是把展開式中「每斜的兩項合在一起」，整理成
> $\frac{1}{m+1}\big[P_m(0) + \cdots + P_m(m-1)\big] + \big[1P_m(1) + \cdots + (m-1)P_m(m-1)\big] = \frac{1}{m+1} \cdot 1 + (H_m - 1)$，結果相同。

### 2.6 求 $A(n)$

每一回合找出最小值後，剩下 $n-1$ 個元素繼續做：

$$A(n) = X_n + A(n-1)$$

> 例：`6 3 2 4 1` → 第一回合後 `1 3 2 4 6`；剩下的 4 個元素對應 $A(4)$。

展開：

$$\begin{aligned}
A(n) &= (H_n - 1) + A(n-1) \\
&= (H_n - 1) + (H_{n-1} - 1) + \cdots + (H_2 - 1) + \underbrace{A(1)}_{=0} \\
&= \left(\sum_{i=2}^{n} H_i\right) - (n-1)
\end{aligned}$$

### 2.7 求 $\sum H_i$

$$\begin{aligned}
\sum_{i=1}^{n} H_i &= H_n + H_{n-1} + \cdots + H_1 \\
&= H_n + \left(H_n - \tfrac1n\right) + \left(H_n - \tfrac1n - \tfrac{1}{n-1}\right) + \cdots + \left(H_n - \tfrac1n - \cdots - \tfrac12\right) \\
&= nH_n - \left[\frac{n-1}{n} + \frac{n-2}{n-1} + \cdots + \frac12\right] \\
&= nH_n - \left[\left(1 - \tfrac1n\right) + \left(1 - \tfrac{1}{n-1}\right) + \cdots + \left(1 - \tfrac12\right)\right] \\
&= nH_n - (n-1) + \left[\tfrac1n + \tfrac{1}{n-1} + \cdots + \tfrac12\right] \\
&= nH_n - n + H_n = (n+1)H_n - n
\end{aligned}$$

### 2.8 結果

$$\sum_{i=2}^{n} H_i = (n+1)H_n - n - H_1 = (n+1)H_n - n - 1$$

$$A(n) = (n+1)H_n - n - 1 - (n-1) = \boxed{(n+1)H_n - 2n}$$

當 n 夠大，$H_n \approx \log_e n$，因此 $A(n) = O(n \log n)$。

### 2.9 Selection Sort 小結

| 情況 | Comparison | Flag change |
|---|---|---|
| Best | $O(n^2)$ | $O(1)$ |
| Worst | $O(n^2)$ | $O(n^2)$ |
| Average | $O(n^2)$ | $O(n\log n)$ |

> Flag change 只算 `f := k`，不算 `f := j`。

---

## 3. 調和數（Harmonic Number）

$$H_n = 1 + \frac12 + \frac13 + \cdots + \frac1n$$

[Knuth 1986]：

$$H_n = \ln n + \gamma + \frac{1}{2n} - \frac{1}{12n^2} + \frac{1}{120n^4} - \varepsilon,\quad 0 < \varepsilon < \frac{1}{252n^6}$$

$$\gamma = 0.5772156649\ldots \text{（Euler's constant）}$$

> $H_n = O(\log n)$

---

## 4. 快速排序（Quick Sort）

### 4.1 概念

Divide-and-conquer：以第一個元素為基準分割，左邊都比它小、右邊都比它大，再遞迴處理兩邊。

```
3  6  1  4  5  2        以 3 為準
        ↓
1  2  [3]  6  4  5
 ↓           ↓
quick sort  quick sort
```

> 關鍵問題：如何有效判斷並搬移資料到 3 的左邊或右邊？→ 用雙指標實作。

### 4.2 雙指標實作

1. 以第一個元素為基準：$x = 3$
2. **j 從右往左**掃，直到遇到**小於** $x$ 的，與 $a_i$ 交換
3. **i 從左往右**掃，直到遇到**大於** $x$ 的，與 $a_j$ 交換
4. 重複 2、3，直到 $i \ge j$

**追蹤**：`3 6 1 4 5 2`，$x = 3$

| 動作 | 結果 | 說明 |
|---|---|---|
| 起始 | `3 6 1 4 5 2` | i 指 3，j 指 2 |
| j 往左 | `2 6 1 4 5 3` | 2 < 3，與 $a_i$ 交換 |
| i 往右 | `2 3 1 4 5 6` | 6 > 3，與 $a_j$ 交換 |
| j 往左 | `2 1 3 4 5 6` | 5、4 ≥ 3 略過，1 < 3，與 $a_i$ 交換 |
| i 往右 | 停止 | $i \ge j$，3 已就定位 |

一輪掃描為 $O(n)$，之後遞迴處理左右兩段。

### 4.3 演算法 QuickSort(f, l)

```pascal
Input:  af, af+1, ..., al
Output: The sorted sequence of af, af+1, ..., al

If f > l then Return
X := af                     { 第一個元素，用來分割 }
i := f
j := l
While i < j do
Begin
    While aj ≥ X do j := j - 1     { j 往左找小於 X 的 }
    ai ↔ aj
    While ai ≤ X do i := i + 1     { i 往右找大於 X 的 }
    ai ↔ aj
End
Quicksort(f, j-1)                  { 遞迴處理左半 }
Quicksort(j+1, l)                  { 遞迴處理右半 }
```

### 4.4 Best Case：$O(n\log n)$

每次都分成**大小幾乎相等**的兩半：

| Round | 子串列大小 | 該輪比較次數 |
|---|---|---|
| 1 | $n$ | $n$ |
| 2 | $n/2$ × 2 | $(n/2) \times 2 = n$ |
| 3 | $n/4$ × 4 | $n$ |
| ⋮ | ⋮ | ⋮ |

共 $\log n$ 輪，每輪約 $n$ 次比較（忽略用來分割的元素）→ $O(n\log n)$

### 4.5 Worst Case：$O(n^2)$

每一輪用來分割的元素**恰為最小或最大值**（例：`1 2 3 4 5` 已排序）：

$$(n-1) + (n-2) + \cdots + 1 = \frac{n(n-1)}{2} = O(n^2)$$

### 4.6 Average Case：$O(n\log n)$

#### 建立遞迴式

假設 n 個元素分割後變成 $s$ 與 $n-s$ 兩段（含分割元素），$s$ 從 1 到 n 機率相同：

$$T(n) = \operatorname*{Avg}_{1 \le s \le n}\big(T(s) + T(n-s)\big) + cn$$

其中 $cn$ 為此 round 的掃描時間，邊界條件 $T(0) = 0$。

$$\begin{aligned}
T(n) &= \frac1n \sum_{s=1}^{n}\big(T(s) + T(n-s)\big) + cn \\
&= \frac1n\big(T(1) + T(n-1) + T(2) + T(n-2) + \cdots + T(n) + T(0)\big) + cn \\
&= \frac1n\big(2T(1) + 2T(2) + \cdots + 2T(n-1) + T(n)\big) + cn
\end{aligned}$$

兩邊乘 n，再移項：

$$nT(n) = 2T(1) + \cdots + 2T(n-1) + T(n) + cn^2$$

$$(n-1)T(n) = 2T(1) + 2T(2) + \cdots + 2T(n-1) + cn^2 \quad \cdots (1)$$

#### 消去累加項

n 用 n−1 代入：

$$(n-2)T(n-1) = 2T(1) + \cdots + 2T(n-2) + c(n-1)^2 \quad \cdots (2)$$

(1) − (2)，利用 $n^2 - (n-1)^2 = 2n - 1$：

$$(n-1)T(n) - (n-2)T(n-1) = 2T(n-1) + c(2n-1)$$

$$(n-1)T(n) - nT(n-1) = c(2n-1)$$

兩邊乘 $\dfrac{1}{(n-1)n}$，並利用 $\dfrac{2n-1}{(n-1)n} = \dfrac{n + (n-1)}{(n-1)n} = \dfrac1n + \dfrac{1}{n-1}$：

$$\frac{T(n)}{n} = \frac{T(n-1)}{n-1} + c\left(\frac1n + \frac{1}{n-1}\right)$$

#### 遞迴展開

$$\begin{aligned}
\frac{T(n)}{n} &= c\left(\frac1n + \frac{1}{n-1}\right) + c\left(\frac{1}{n-1} + \frac{1}{n-2}\right) + \cdots + c\left(\frac12 + 1\right) + \underbrace{T(1)}_{=0} \\
&= c\left(\frac1n + \frac{1}{n-1} + \cdots + \frac12\right) + c\left(\frac{1}{n-1} + \frac{1}{n-2} + \cdots + 1\right) \\
&= c(H_n - 1) + cH_{n-1}
\end{aligned}$$

（除了 $\frac1n$ 和 $1$ 之外，每一項都出現兩次。）

#### 結果

利用 $cH_{n-1} = cH_n - \frac{c}{n}$：

$$\frac{T(n)}{n} = c\left(2H_n - \frac1n - 1\right)$$

$$\boxed{T(n) = 2cnH_n - c(n+1) = O(n\log n)}$$

### 4.7 Quick Sort 小結

| 情況 | 複雜度 | 發生條件 |
|---|---|---|
| Best | $O(n\log n)$ | 每次均分 |
| Average | $O(n\log n)$ | 隨機資料 |
| Worst | $O(n^2)$ | 每次選到最小或最大（如已排序） |

> 其他演算法如 **heap sort**，worst case 也只有 $O(n\log n)$。

---

## 5. 找秩問題（Finding Ranks）

### 5.1 1-D Domination

| 名詞 | 定義 |
|---|---|
| Dominate | 若 $a_i > a_j$，則 $a_i$ dominates $a_j$ |
| Rank of $a_i$ | 被 $a_i$ dominate 的元素個數 |

例：`3 5 1 7`
- 3 dominates 1；7 dominates 1, 3, 5

| 元素 | 1 | 3 | 5 | 7 |
|---|---|---|---|---|
| Rank | 0 | 1 | 2 | 3 |

**求法**：直接排序，排序後的位置（從 0 開始）即為 rank → $O(n\log n)$

### 5.2 2-D Domination

**定義**：令 $A = (a_1, a_2)$、$B = (b_1, b_2)$

$$A \text{ dominates } B \iff a_1 > b_1 \text{ and } a_2 > b_2$$

> **每個維度都要成立**。若只有一個維度較大，兩點為 **incomparable**。

**Rank of x**：集合 S 中被 x dominate 的點數。

例（投影片中的五個點 A~E）：
- B、C、D dominate A
- D dominates A、B、C
- B、E incomparable

| 點 | A | B | C | D | E |
|---|---|---|---|---|---|
| Rank | 0 | 1 | 1 | 3 | 0 |

### 5.3 暴力法（Straight-Forward）

比較所有點對：每個點檢查其他點是否被它 dominate，每點 $O(n)$、共 n 點 → $O(n^2)$

### 5.4 Divide-and-Conquer 2-D Ranking

**Step 1**：以中位數垂直線 L 將點分成左半 A、右半 B（兩邊點數相同）
**Step 2**：遞迴求出 A 中各點、B 中各點的 rank
**Step 3**：將 A、B 中的點依 **y 值**排序，更新 B 中各點的 rank

> **為什麼只更新 B？**
> - A 的點 x 座標都比 B 小，**不可能** dominate B 的點 → A 的 rank 不變
> - B 的點 x 座標都比 A 大，只要 **y 值也比較大**就 dominate 該 A 點
> - 所以 B 中每點的最終 rank = 在 B 中的 rank + **A 中 y 值比它小的點數**

投影片例子：A 側 4 點，rank 為 0, 0, 1, 1；B 側各點更新如下

| B 中的點（由下往上） | 在 B 中的 rank | A 中 y 值較小的點數 | 最終 rank |
|---|---|---|---|
| 1 | 0 | 1 | 1 |
| 2 | 0 | 2 | 2 |
| 3 | 1 | 3 | 4 |
| 4 | 2 | 3 | 5 |
| 5 | 2 | 4 | 6 |

### 5.5 時間複雜度

| 步驟 | 做法 | 複雜度 |
|---|---|---|
| Step 1：找中位數 | 直觀做法：先排序 $O(n\log n)$，再取第 $n/2$ 個 $O(1)$ | $O(n\log n)$；**Chapter 6** 有 $O(n)$ 做法 |
| Step 3：更新 rank | 依 y 值排序 $O(n\log n)$ + 由左到右掃描 $O(n)$ | $O(n\log n)$ |

**遞迴式**（取 Step 1 為 $O(n)$）：

$$\begin{aligned}
T(n) &= O(n) + 2T\left(\frac n2\right) + O(n\log n) \\
&\le 2T\left(\frac n2\right) + cn\log n \\
&\le 4T\left(\frac n4\right) + cn\log\frac n2 + cn\log n \quad \left(\text{因 } 2 \times c \times \tfrac n2 \log \tfrac n2\right) \\
&\le nT(1) + c\left(n\log n + n\log\frac n2 + n\log\frac n4 + \cdots + n\log 2\right)
\end{aligned}$$

括號內有 $\log n$ 項，是**等差級數**（以 $\log_2$ 計）：

$$\log n + \log\frac n2 + \log\frac n4 + \cdots + \log 2 = \log n + (\log n - 1) + (\log n - 2) + \cdots + 1 = \frac{\log n(\log n + 1)}{2}$$

$$T(n) = nT(1) + \frac{cn\log n(\log n + \log 2)}{2} = O(n\log^2 n)$$

### 5.6 小結

| 項目 | 複雜度 |
|---|---|
| Sorting | $O(n\log n)$ |
| Finding medians | $O(n)$ |
| Worst case / Average case | $O(n\log^2 n)$ |

> 遠優於 exhaustive search 的 $O(n^2)$。

---

## 6. 漸近符號：$O$、$\Omega$、$\Theta$

### 6.1 定義

| 符號 | 定義 | 意義 |
|---|---|---|
| $f(n) = O(g(n))$ | $\exists\, c, n_0$，$\lvert f(n)\rvert \le c\lvert g(n)\rvert,\ \forall n \ge n_0$ | **At most**（上限） |
| $f(n) = \Omega(g(n))$ | $\exists\, c, n_0$，$\lvert f(n)\rvert \ge c\lvert g(n)\rvert,\ \forall n \ge n_0$ | **At least**（下限） |
| $f(n) = \Theta(g(n))$ | $\exists\, c_1, c_2, n_0$，$c_1\lvert g(n)\rvert \le \lvert f(n)\rvert \le c_2\lvert g(n)\rvert,\ \forall n \ge n_0$ | **Exact**（至少又至多，即剛好） |

### 6.2 例子

**$\Omega$ 的例子**：

$$f(n) = n^3 + n \ge n^3 \ (n > 1) \Rightarrow f(n) = \Omega(n^3)$$
$$\text{且 } n^3 + n \ge n \Rightarrow f(n) = \Omega(n)$$

> $\Omega$ 不只一個，**一般找最大的**（對比：$O$ 一般找最小的）。

**綜合例子**：$f(n) = 3n^2 + 2$

| 寫法 | 是否成立 |
|---|---|
| $O(n^2)$、$O(n^3)$ | ✓ |
| $\Omega(n^2)$、$\Omega(n)$ | ✓ |
| $\Theta(n^2)$ | ✓（唯一精確的） |

---

## 7. 問題的下限（Lower Bound of a Problem）

### 7.1 定義

> **Lower bound of a problem**：任何能解決此問題的演算法，**至少**需要的時間複雜度。

用來衡量問題的難度（例如 NP-complete），以 $\Omega$ 表示。分為：
- Worst case lower bound
- Average case lower bound

### 7.2 Lower bound 不唯一

以排序為例：$\Omega(1)$、$\Omega(n)$、$\Omega(n\log n)$ **都是** lower bound，其中 $\Omega(1)$、$\Omega(n)$ 是 trivial 的。

**如何取得？**

| 方法 | 可行性 |
|---|---|
| 嘗試所有演算法 | 不可能 |
| 理論證明（theoretical proving） | ✓ |

### 7.3 逐步提高 lower bound

先得到一個，再想辦法慢慢提高。以排序為例：

| Lower bound | 理由 |
|---|---|
| $\Omega(1)$ | 顯然，至少要讀一筆資料 |
| $\Omega(n)$ | 一定要掃過 n 個元素 |
| $\Omega(n\log n)$ | 之後證明 |
| $\Omega(n^2)$？ | **不可能** |

### 7.4 為什麼排序不可能是 $\Omega(n^2)$？

- Lower bound 是**任何**演算法至少需要的時間
- 已知 heap sort 的 **worst case** 為 $O(n\log n)$
- 所以真正的 lower bound $\le O(n\log n)$

> ⚠️ 不能用 quick sort 的 worst case $O(n^2)$ 來推論，因為 lower bound 看的是**最好的**演算法。

### 7.5 Lower Bound 與最佳演算法的三種關係

| Case | Lower bound | 最佳演算法 | 結論 |
|---|---|---|---|
| 1 | $\Omega(n\log n)$ | $O(n^2)$ | 有落差，三種可能：找到更高的 lower bound、找到更好的演算法、兩者都可改進 |
| 2 | $\Omega(n\log n)$ | $O(n\log n)$ | 演算法為 **optimal** |
| 3 | $\Omega(n\log n)$ | $O(n)$ | **錯誤**：演算法比 lower bound 還快，代表 lower bound 證明有誤 |

```
Case 1                Case 2                Case 3（不可能）
───── Best O(n²)      ═════ Best = LB       ───── LB Ω(n log n)
  ↕ 差距                                     ───── Best O(n)  ✗
───── LB Ω(n log n)
```

> Case 2 的 optimal 指的是**效率（efficiency）**，不是準確度（accuracy）。

### 7.6 Optimal Algorithm

> 演算法為 **optimal** ⟺ 其時間複雜度 **=** 問題的 lower bound
> 此時 lower bound 與演算法**都無法再改進**。

剩下的難題：**如何取得 lower bound**——這很困難。

---

## 重點整理

| 主題 | 重點 | 注意事項 |
|---|---|---|
| Selection sort comparison | 永遠 $\frac{n(n-1)}{2}$，$O(n^2)$ | 不隨資料變 |
| Selection sort flag change | Best $O(1)$、Worst $O(n^2)$、Average $O(n\log n)$ | 只算 `f := k` |
| $P_n(k)$ 遞迴 | $P_n(k) = \frac1n P_{n-1}(k-1) + \frac{n-1}{n}P_{n-1}(k)$ | $P_1(0)=1$；$P_n(n) = 0$ |
| $X_n$ | $H_n - 1$ | 歸納法證明 |
| $A(n)$ | $A(n) = X_n + A(n-1) = (n+1)H_n - 2n$ | 用到 $\sum_{i=1}^n H_i = (n+1)H_n - n$ |
| Harmonic number | $H_n \approx \ln n + \gamma$ | $H_n = O(\log n)$ |
| Quick sort 實作 | j 往左找 < x、i 往右找 > x，交換直到 $i \ge j$ | 一輪掃描 $O(n)$ |
| Quick sort 複雜度 | Best / Average $O(n\log n)$、Worst $O(n^2)$ | Worst：每次選到最小或最大 |
| Quick sort average 推導 | $\frac{T(n)}{n} = \frac{T(n-1)}{n-1} + c(\frac1n + \frac{1}{n-1})$ | 結果 $T(n) = 2cnH_n - c(n+1)$ |
| 1-D rank | 排序後的位置 | $O(n\log n)$ |
| 2-D dominate | 兩個維度都要較大 | 否則 incomparable |
| 2-D ranking（D&C） | 中位線分割 → 遞迴 → 依 y 排序更新 B | $O(n\log^2 n)$，優於暴力 $O(n^2)$ |
| $O$ / $\Omega$ / $\Theta$ | At most / At least / Exact | $O$ 取最小、$\Omega$ 取最大 |
| 問題的 lower bound | 任何演算法至少需要的時間 | 不唯一；需理論證明 |
| 排序的 lower bound | $\Omega(n\log n)$ | 不可能是 $\Omega(n^2)$（heap sort 反例） |
| Optimal algorithm | 時間複雜度 = lower bound | Lower bound 高於已知演算法 → 證明有誤 |
