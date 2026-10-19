# Chapter 3 (Part 2) — The Greedy Method

> Introduction to the Design and Analysis of Algorithms: A Strategic Approach（R.C.T. Lee, S.S. Tseng, R.C. Chang, Y.T. Tsang）
> 課程投影片：Algorithms（洪宗貝 Tzung-Pei Hong）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Single-source shortest path | 求單一起點到所有點的最短路徑 |
| Dijkstra's algorithm | Greedy 解法；$O(n^2)$，且為 **optimal** |
| Merge problem | 兩個已排序串列的 linear merge，$O(m+n)$ |
| 2-way merge tree | 多個串列合併；**短的先合併** |
| Huffman code | 2-way merge tree 的應用；低頻率 → 長編碼 |
| Minimal cycle basis | Ring sum（$\oplus$）、cycle basis 的定義與求法 |
| Gaussian elimination | 檢查一個 cycle 是否為既有 cycles 的線性組合 |
| Greedy 的原則 | 好的先做；若知道解的元素個數可更省時間 |

---

## 1. 單源最短路徑問題（The Single-Source Shortest Path Problem）

### 1.1 問題

> 給定有向圖與起點 $v_0$，找出 $v_0$ 到**所有目的地**的最短路徑。

**範例圖**（有向、邊權重非負）：

| 邊 | 權重 | | 邊 | 權重 |
|---|---|---|---|---|
| $v_0 \to v_1$ | 50 | | $v_2 \to v_3$ | 15 |
| $v_0 \to v_2$ | 10 | | $v_3 \to v_1$ | 20 |
| $v_0 \to v_4$ | 45 | | $v_3 \to v_4$ | 35 |
| $v_1 \to v_2$ | 15 | | $v_4 \to v_3$ | 30 |
| $v_1 \to v_4$ | 10 | | $v_5 \to v_3$ | 3 |
| $v_2 \to v_0$ | 20 | | | |

**結果**：

| | Path | Length |
|---|---|---|
| 1 | $v_0 v_2$ | 10 |
| 2 | $v_0 v_2 v_3$ | 25 |
| 3 | $v_0 v_2 v_3 v_1$ | 45 |
| 4 | $v_0 v_4$ | 45 |

> $v_0 \to v_5$ **無路徑**（$v_5$ 只有出邊，沒有入邊）。
> 注意第 3 列：繞路的 $v_0v_2v_3v_1 = 45$ 比直接走 $v_0 \to v_1 = 50$ 還短。

### 1.2 Dijkstra's Algorithm 的想法

這是一個 **greedy method**，做法有點像 MST：把頂點分成兩個集合。

| 集合 | 意義 |
|---|---|
| $S$ | **已處理完**的點 |
| $V - S$ | **尚未處理**的點 |

每次從 $V-S$ 中挑一個**最好的 vertex** 放進 $S$。

### 1.3 與 MST 的差異

考慮 $S = \{v_0, v_1\}$，$V-S = \{v_2, v_3\}$，其中 $v_0 \to v_2 = 30$、$v_0 \to v_1 = 29$、$v_1 \to v_3 = 3$：

| | MST | Dijkstra |
|---|---|---|
| 判斷依據 | A、B 之間**單一邊**的權重 | 從 $v_0$ 出發的**整條路徑**長度 |
| 選擇 | 選 $v_1 v_3$（權重 3 最小） | 比較 $v_0v_1v_3 = 29+3 = 32$ 與 $v_0v_2 = 30$ → 選 **$v_0v_2$** |

> **關鍵**：Dijkstra 比的是「從 $v_0$ 開始」的累積距離，不是單一邊的權重。

---

## 2. Dijkstra's Algorithm

### 2.1 輸入輸出

| | 內容 |
|---|---|
| Input | 有向圖 $G = (V, E)$ 與起點 $v_0$；每條邊 $(u,v) \in E$ 有**非負**權重 $c(u,v)$；$\lvert V\rvert = n+1$（其中一個是 $v_0$） |
| Output | 對每個 $v \in V$，$v_0$ 到 $v$ 的最短路徑長度 |

### 2.2 核心變數 $L(v_i)$

每個點維護一個 $L(v_i)$，存**目前已知**從 $v_0$ 到 $v_i$ 的最小距離。

$$L(v_2) = \min\big(v_0v_2,\ v_0v_1 + v_1v_2\big) = \min\big(L(v_2),\ L(v_1) + c(v_1, v_2)\big)$$

> 例：$\min(6,\ 1+3) = 4$。接著再用 $v_2$ 去更新其他的 $L(v_i)$。

### 2.3 演算法

**Step 1：設定初始 $L(v_i)$**

```pascal
S := {v0}
For i := 1 to n do
Begin
    If (v0, vi) ∈ E then
        L(vi) := c(v0, vi)
    else
        L(vi) := ∞
End
```

**Step 2：逐一挑點並更新**

```pascal
For i := 1 to n do
Begin
    Choose u from V - S such that L(u) is the smallest
    S := S ∪ {u}                            { Put u into S }
    For all w in V - S do
        L(w) := min(L(w), L(u) + c(u, w))
End
```

### 2.4 範例追蹤

**範例圖**（6 個頂點）：

| 邊 | 權重 | | 邊 | 權重 |
|---|---|---|---|---|
| $v_0 \to v_1$ | 1 | | $v_2 \to v_3$ | 2 |
| $v_0 \to v_2$ | 6 | | $v_2 \to v_4$ | 2 |
| $v_1 \to v_2$ | 3 | | $v_3 \to v_4$ | 2 |
| $v_1 \to v_3$ | 4 | | $v_3 \to v_5$ | 3 |
| $v_1 \to v_4$ | 6 | | $v_4 \to v_5$ | 4 |

**$L(v_i)$ 的變化**（粗體為該輪選中的點）：

| $L$ | 初始 | 選 $v_1$ 後 | 選 $v_2$ 後 | 選 $v_3$ 後 | 選 $v_4$ 後 |
|---|---|---|---|---|---|
| $L(v_1)$ | **1** | 1 | 1 | 1 | 1 |
| $L(v_2)$ | 6 | **4** | 4 | 4 | 4 |
| $L(v_3)$ | ∞ | 5 | **5** | 5 | 5 |
| $L(v_4)$ | ∞ | 7 | 6 | **6** | 6 |
| $L(v_5)$ | ∞ | ∞ | ∞ | 8 | **8** |

**逐步說明**：
- 初始：$L(v_1)=1$、$L(v_2)=6$，其餘 ∞ → 最小是 $v_1$
- 選 $v_1$：$L(v_2) = \min(6,\ 1+3) = 4$；$L(v_3) = \min(\infty,\ 1+4) = 5$；$L(v_4) = \min(\infty,\ 1+6) = 7$
- 選 $v_2$：$L(v_3) = \min(5,\ 4+2) = 5$；$L(v_4) = \min(7,\ 4+2) = 6$
- 選 $v_3$：$L(v_4) = \min(6,\ 5+2) = 6$；$L(v_5) = \min(\infty,\ 5+3) = 8$
- 選 $v_4$：$L(v_5) = \min(8,\ 6+4) = 8$

**最終結果**：

| Vertex | Shortest path to $v_0$ | Length |
|---|---|---|
| $v_1$ | $v_0v_1$ | 1 |
| $v_2$ | $v_0v_1v_2$ | $1+3 = 4$ |
| $v_3$ | $v_0v_1v_3$ | $1+4 = 5$ |
| $v_4$ | $v_0v_1v_2v_4$ | $1+3+2 = 6$ |
| $v_5$ | $v_0v_1v_3v_5$ | $1+4+3 = 8$ |

> 若也要輸出**路徑本身**，可多用一個矩陣記錄前驅點（做法同前面的 MST）。

### 2.5 時間複雜度

| 輪次 | 比較與更新次數 |
|---|---|
| 第 0 個 vertex | $n$ |
| 第 1 個 vertex | $n-1$ |
| 第 2 個 vertex | $n-2$ |
| ⋮ | ⋮ |

$$n + (n-1) + (n-2) + \cdots + 1 = \frac{n(n+1)}{2} = O(n^2)$$

### 2.6 Dijkstra 是 Optimal 的

**問題的 lower bound**：每條邊都必須被檢查過 → $\Omega(m)$

Worst case：$m = \dfrac{n(n-1)}{2}$ → $\Omega(m) = \Omega(n^2)$

| 項目 | 複雜度 |
|---|---|
| 問題 lower bound | $\Omega(n^2)$ |
| Dijkstra | $O(n^2)$ |

> 兩者相等 → **Dijkstra's algorithm is optimal**（參考 Chapter 2 Case 2）。

---

## 3. 合併問題（Merge Problem）

### 3.1 問題

給定兩個**已排序**的串列 $L_1 = (a_1, \dots, a_{n_1})$、$L_2 = (b_1, \dots, b_{n_2})$，合併成一個排序好的串列。

> 例：`1, 3, 5, 7` 與 `2, 4, 6, 8, 10` → `1, 2, 3, 4, 5, 6, 7, 8, 10`

### 3.2 Linear Merge

**做法**：比較兩串列的第一個元素，**較小的輸出**並往後移一格。

| 比較 | 輸出 | $L_1$ 剩餘 | $L_2$ 剩餘 |
|---|---|---|---|
| 1 vs 2 | 1 | 3 5 7 | 2 4 6 8 10 |
| 3 vs 2 | 2 | 3 5 7 | 4 6 8 10 |
| 3 vs 4 | 3 | 5 7 | 4 6 8 10 |
| 5 vs 4 | 4 | 5 7 | 6 8 10 |
| 5 vs 6 | 5 | 7 | 6 8 10 |
| 7 vs 6 | 6 | 7 | 8 10 |
| 7 vs 8 | 7 | — | 8 10 |
| 一邊空了 | 8, 10 | | |

### 3.3 演算法

```pascal
Input:  Two sorted lists, L1 = (a1,...,an1) and L2 = (b1,...,bn2)
Output: A sorted list consisting of elements in L1 and L2

Begin
    i := 1
    j := 1
    do
        compare ai and bj
        if ai > bj then output bj and j := j + 1
        else output ai and i := i + 1
    while (i ≤ n1 and j ≤ n2)
    if i > n1 then output bj, bj+1, ..., bn2
    else output ai, ai+1, ..., an1
End
```

### 3.4 Worst Case 時間複雜度

$$O(m + n - 1) = O(m+n)$$

> 最差情況下，兩串列各剩一個元素前都要比較；最後剩下的元素直接輸出不需比較，因此是 $m+n-1$ 次。

---

## 4. 多個串列的合併：2-Way Merge

### 4.1 問題

> 例：$L_1 = 1, 3, 5$、$L_2 = 2, 8, 10$、$L_3 = 6, 7, 9$ — 怎麼合併？

一個常見的做法是 **2-way merge**（每次只合併兩個），這是一個 **greedy approach**。

三個串列有三種合併順序：

1. $(L_1 + L_2) + L_3$
2. $(L_1 + L_3) + L_2$
3. $(L_2 + L_3) + L_1$

> **哪一種比較好？**

### 4.2 關鍵範例

設 $L_1$ 有 50 個元素、$L_2$ 有 30 個、$L_3$ 有 10 個：

| 順序 | 第一次合併 | 第二次合併 | 總成本 |
|---|---|---|---|
| $(L_1+L_2) + L_3$ | $50+30-1 = 79$ | $80+10-1 = 89$ | **168** |
| $(L_2+L_3) + L_1$ | $30+10-1 = 39$ | $40+50-1 = 89$ | **128** |

> **結論：短的串列先合併（Shorter list merged first）**
> 理由：先合併的串列，其元素會被後續的合併**重複掃描**，所以要讓短的多掃、長的少掃。

### 4.3 2-Way Merge Tree 範例

6 個已排序串列，長度分別為 **2, 3, 5, 7, 11, 13**。

每次取**最小的兩棵樹**合併：

| 步驟 | 取出 | 產生 | 目前的集合 |
|---|---|---|---|
| 1 | 2, 3 | **5** | 5, 5, 7, 11, 13 |
| 2 | 5, 5 | **10** | 7, 10, 11, 13 |
| 3 | 7, 10 | **17** | 11, 13, 17 |
| 4 | 11, 13 | **24** | 17, 24 |
| 5 | 17, 24 | **41** | 41 ← 完成 |

**最終的 merge tree**：

```
              41
          ┌────┴────┐
         17        24
       ┌──┴──┐   ┌──┴──┐
      [7]   10  [11] [13]
          ┌──┴──┐
          5    [5]
        ┌─┴─┐
       [2] [3]
```

（方框為原始串列的葉節點，圓圈為合併後的內部節點。）

**總合併成本** = 所有內部節點的和 $= 5 + 10 + 17 + 24 + 41 = 97$

### 4.4 演算法

```
Input:  m sorted lists Li (i = 1..m), each Li with ni elements
Output: An optimal 2-way merge tree

Step 1. 產生 m 棵樹，每棵樹只有一個節點，權重為 ni
Step 2. 選出權重最小的兩棵樹 T1 和 T2
Step 3. 建立新樹 T，以 T1、T2 為子樹，權重 = W(T1) + W(T2)
Step 4. 用 T 取代 T1 和 T2
Step 5. 若只剩一棵樹則停止；否則回到 Step 2
```

### 4.5 時間複雜度

| 步驟 | 做法 | 複雜度 |
|---|---|---|
| 將 n 個數排序 | 用 **min-heap** | $O(n\log n)$ |
| 每個合併後的串列 $(n_i + n_j)$ 插回正確位置 | 用 **binary search** | 每次 $O(\log n)$，共 n 次 → $O(n\log n)$ |

$$\text{Total} = O(n\log n)$$

---

## 5. 2-Way Merge Tree 的正確性證明

### 5.1 預備工作（Preparation Work）

> 先證：在一棵 optimal 2-way merge tree 中，**最小的兩個 lists 一定是 brothers，且一定在最深的 level**。

（樹中每個內部節點的 weight 就是其所有 leaf 的和。）

**證明**：
- 若最深 level 的節點不是最小的 weight，把它與某個較小的交換後，**total weight（所有 internal node 的和）會變小**
- 所以最小的兩個一定在最深層
- 因此一定存在一棵最小 merge tree，使這兩個是 brothers

> ⚠️ **Note：最小 merge tree 可能不唯一。**
> 若最深層有四個 leaf，最小的兩個可以是這四個中的任兩個。

### 5.2 歸納法證明

目標：證明此演算法產生的是 **optimal**（指 total weight 最小）的 2-way merge tree。

**(1) $m = 2$**：只有兩個 list，一定成立 ✓

**(2) 假設 $m-1$ 個 lists 成立**：用此方法一定可產生 optimal 2-way merge tree

**(3) 證 $m$ 個 lists**：

1. 將 m 個 lists 排序為 $L_1, L_2, \dots, L_m$（$L_1$、$L_2$ 最小）
2. 把 $L_1$、$L_2$ merge 成 $L_1'$，得到 $L_1', L_3, L_4, \dots, L_m$ 共 $m-1$ 個
3. 由歸納假設，這 $m-1$ 個一定能形成 optimal 2-way merge tree，稱為 $T_2$
   > 注意：$L_1'$ 的權重是 $n_1+n_2$，**不見得最小**，所以不一定在最深的 level
4. 把 $T_2$ 中的 $L_1'$ split 成 $L_1$、$L_2$ 兩個節點，得到的樹即為 greedy 演算法產生的 $T_3$

$$W(T_3) = W(T_2) + n_1 + n_2$$

**證明 $T_3$ 是 optimal（反證法）**：

- 假設 $T_3$ 不是 optimal，存在更好的 $T$，即 $W(T_3) > W(T)$
- 由預備工作，$L_1$、$L_2$ 在 $T$ 中也一定是最深層的 brothers
- 把 $T_3$ 與 $T$ 中的 $L_1$、$L_2$ 都用單一節點 $(n_1+n_2)$ 置換，兩者改變的 weight 相同，皆為 $-(n_1+n_2)$
- 設 $T$ 置換後為 $T_1$，$T_3$ 置換後即為 $T_2$
- 由 $W(T_3) > W(T)$ 可推得 $W(T_2) > W(T_1)$
- 但 $W(T_2)$ 已由歸納假設是 optimal → **矛盾**

$$\Rightarrow T_3 \text{ is optimal} \quad\checkmark$$

---

## 6. 應用：Huffman Code

### 6.1 動機

> 例：要傳 A、B、C 三個字元，出現次數分別為 **10 次、9 次、2 次**。

**想法**：出現**頻率高**的字元用**較短**的 code，頻率低的用較長的 code。

**編碼**：`A: 0`、`B: 10`、`C: 11`

$$\text{總位元數} = 1 \times 10 + 2 \times 9 + 2 \times 2 = 32 \text{ bits}$$

> 這種編碼常用於**電信傳輸（telecommunication）**。

### 6.2 Huffman Algorithm = 2-Way Merge

Huffman code 就是把 2-way merge tree 用在「頻率」上：**每次合併頻率最小的兩個**。

**範例**：7 個符號

| Symbol | A | B | C | D | E | F | G |
|---|---|---|---|---|---|---|---|
| Frequency | 2 | 3 | 5 | 8 | 13 | 15 | 18 |

**建樹過程**：

| 步驟 | 取出 | 產生 | 目前集合 |
|---|---|---|---|
| 1 | 2(A), 3(B) | **5** | 5, 5(C), 8, 13, 15, 18 |
| 2 | 5, 5(C) | **10** | 8, 10, 13, 15, 18 |
| 3 | 8(D), 10 | **18** | 13, 15, 18, 18 |
| 4 | 13(E), 15(F) | **28** | 18, 18, 28 |
| 5 | 18, 18(G) | **36** | 28, 36 |
| 6 | 28, 36 | **64** | 64 ← 完成 |

**Huffman code tree**（左邊走 0、右邊走 1）：

```
                  64
          ┌───0───┴───1───┐
         28              36
      ┌─0─┴─1─┐      ┌─0─┴─1─┐
   [13:E]  [15:F]   18     [18:G]
                  ┌─0─┴─1─┐
                [8:D]    10
                      ┌─0─┴─1─┐
                      5      [5:C]
                   ┌─0─┴─1─┐
                 [2:A]  [3:B]
```

**結果**：

| Symbol | Frequency | Code | 長度 |
|---|---|---|---|
| A | 2 | `10100` | 5 |
| B | 3 | `10101` | 5 |
| C | 5 | `1011` | 4 |
| D | 8 | `100` | 3 |
| E | 13 | `00` | 2 |
| F | 15 | `01` | 2 |
| G | 18 | `11` | 2 |

> **Lower frequency → Longer code**

**總位元數**：$2(5) + 3(5) + 5(4) + 8(3) + 13(2) + 15(2) + 18(2) = 161$ bits
（也等於所有內部節點之和：$5+10+18+28+36+64 = 161$。）

---

## 7. 最小環基底問題（The Minimal Cycle Basis Problem）

### 7.1 Cycle 與 Ring Sum

給定一個圖，裡面可能有很多 cycles。

**範例圖**（頂點 a, b, c, d）：

| 邊 | 權重 |
|---|---|
| $ab$ | 6 |
| $ac$ | 4 |
| $ad$ | 7 |
| $bc$ | 4 |
| $cd$ | 5 |

三個 cycles：

| Cycle | 組成 | Weight |
|---|---|---|
| $A_1$ | {ab, bc, ca} | $6+4+4 = 14$ |
| $A_2$ | {ac, cd, da} | $4+5+7 = 16$ |
| $A_3$ | {ab, bc, cd, da} | $6+4+5+7 = 22$ |

### 7.2 Ring Sum 運算（$\oplus$）

$$A \oplus B = (A \cup B) - (A \cap B)$$

即**對稱差（symmetric difference）**，也就是對邊集合做 **exclusive-or**。

**例 1**：

$$\begin{aligned}
A_1 \oplus A_2 &= (A_1 \cup A_2) - (A_1 \cap A_2) \\
&= \{ab, bc, ca, cd, da\} - \{ca\} \\
&= \{ab, bc, cd, da\} = A_3
\end{aligned}$$

**例 2**：

$$\begin{aligned}
A_1 \oplus A_3 &= \{ab, bc, ca, cd, da\} - \{ab, bc\} \\
&= \{ca, cd, da\} = A_2
\end{aligned}$$

三者互相可推：$A_1 \oplus A_2 = A_3$、$A_1 \oplus A_3 = A_2$、$A_2 \oplus A_3 = A_1$

### 7.3 Cycle Basis 的定義

> 圖的 **cycle basis** 是一組 cycles，使得圖中**任何一個 cycle** 都可以由這組 basis 中的某些 cycles 做 $\oplus$ 運算產生。

上例中 $\{A_1, A_2\}$、$\{A_1, A_3\}$、$\{A_2, A_3\}$ **任一組**都是 cycle basis。

### 7.4 問題定義

> 給定一個**有權重**的圖，找出**總權重最小**的 cycle basis。

上例的 minimal cycle basis 為 $\{A_1, A_2\}$：

$$\text{Total weight} = 14 + 16 = 30$$

（比 $\{A_1, A_3\} = 36$、$\{A_2, A_3\} = 38$ 都小。）

---

## 8. 求 Minimal Cycle Basis 的 Greedy 演算法

```
Step 1: 決定 minimal cycle basis 的大小，記為 k
        e.g. {A1, A2} → k = 2
Step 2: 找出所有 cycles，依 weight 由小到大排序
Step 3: 一個一個把 cycle 加進 basis。檢查新加的 cycle 是否為
        basis 中既有 cycles 的組合；若是，則刪除此 cycle
Step 4: 當 basis 中有 k 個 cycles 時停止
```

### 8.1 Step 1：決定大小 k

**基於 spanning tree 的觀察**：
- 取出圖的一棵 spanning tree，有 $\lvert V\rvert - 1$ 條邊
- 把**每一條不在 spanning tree 中的邊**加回去，各會形成**一個 basic cycle**

$$\boxed{k = \lvert E\rvert - (\lvert V\rvert - 1) = \lvert E\rvert - \lvert V\rvert + 1}$$

> 這個數字又稱為圖的 **circuit rank**（迴路秩）。

### 8.2 Step 2：找出所有 cycles

**一個圖中有多少 cycles？**（Reingold, Nievergelt and Deo, 1977）

以 **complete digraph**（完全有向圖）為例，$n = 4$：

| 含幾個 node | 選法 | 每種的排列數 | 小計 |
|---|---|---|---|
| 4 個 | $C^4_4$ | $3!$（正向、反向算兩個） | $C^4_4 \times 3!$ |
| 3 個 | $C^4_3$ | $2!$ | $C^4_3 \times 2!$ |
| 2 個 | $C^4_2$ | $1!$ | $C^4_2 \times 1!$ |

$$\text{總數} = C^4_4 \times 3! + C^4_3 \times 2! + C^4_2 \times 1! = \sum_{i=2}^{4} C^4_i \times (i-1)!$$

**一般情況**（n 個 nodes）：

$$\sum_{i=2}^{n} C^n_i \times (i-1)! \ > \ (n-1)!$$

（只取 $i = n$ 那一項就已經是 $(n-1)!$。）

> **Cycle 的數量是 exponential 的。**

**Note**：
- 在**無向圖**中，不考慮只有兩個 node 的 cycle，至少要三個 node
- 實際實作時**不需要**把所有 cycle 都求出來，通常從 node 少、weight 小的慢慢往上加
- 這裡先假設全部求出，比較容易理解

### 8.3 Step 3：用 Gaussian Elimination 檢查

**問題**：新加入的 cycle 是否為 basis 中既有 cycles 的**線性組合**？
> 例：$A_3 = A_1 \oplus A_2$？

**做法**：把每個 cycle 表示成 **0/1 矩陣**的一列（row = cycle，column = edge），對列做 **exclusive-or（$\oplus$）**消去。

**範例**：

**1. 已知 $C_1$、$C_2$**

| | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ |
|---|---|---|---|---|---|
| $C_1$ | 1 | 1 | 1 | | |
| $C_2$ | | | 1 | 1 | 1 |

**2. 加入下一個 cycle $C_3$**

| | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ |
|---|---|---|---|---|---|
| $C_1$ | 1 | 1 | 1 | | |
| $C_2$ | | | 1 | 1 | 1 |
| **$C_3$** | **1** | **1** | | **1** | **1** |

**3. 對 row 1 與 row 3 做 $\oplus$**

| | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ |
|---|---|---|---|---|---|
| $C_1$ | 1 | 1 | 1 | | |
| $C_2$ | | | 1 | 1 | 1 |
| $C_3$ | | | 1 | 1 | 1 |

**4. 對 row 2 與 row 3 做 $\oplus$** → **$C_3$ 這一列全為 0（Empty）**

$$C_3 \oplus C_1 \oplus C_2 = \varnothing \quad\Rightarrow\quad C_3 = C_1 \oplus C_2$$

> 列變成全 0 代表此 cycle **可由既有的 cycles 組合而成** → **刪除它**。

---

## 9. Minimal Cycle Basis 完整範例

**圖**：$\lvert V\rvert = 6$、$\lvert E\rvert = 8$，假設**每條邊權重都是 1**

**Step 1**：

$$k = \lvert E\rvert - \lvert V\rvert + 1 = 8 - 6 + 1 = 3$$

**Step 2**：找出所有 cycles 並依 weight（= 邊數）排序

| Cycle | 組成 | Weight |
|---|---|---|
| $C_1$ | $\{e_1, e_2, e_3\}$ | 3 |
| $C_2$ | $\{e_3, e_4, e_5\}$ | 3 |
| $C_3$ | $\{e_1, e_2, e_4, e_5\}$ | 4 |
| $C_4$ | $\{e_5, e_6, e_7, e_8\}$ | 4 |
| $C_5$ | $\{e_3, e_4, e_6, e_7, e_8\}$ | 5 |
| $C_6$ | $\{e_1, e_2, e_8, e_7, e_6, e_4\}$ | 6 |

**Step 3**：依序加入並檢查

| 順序 | Cycle | 檢查 | 動作 | Basis 大小 |
|---|---|---|---|---|
| 1 | $C_1$ | — | 加入 | 1 |
| 2 | $C_2$ | 與 $C_1$ 獨立 | 加入 | 2 |
| 3 | $C_3$ | $C_1 \oplus C_2 = \{e_1,e_2,e_4,e_5\} = C_3$ | **刪除** | 2 |
| 4 | $C_4$ | 獨立 | 加入 | **3 = k → 停止** |

**結果**：Minimal cycle basis = $\{C_1, C_2, C_4\}$，總權重 $3 + 3 + 4 = 10$

---

## 10. Greedy 方法的原則

> **Principle of Greedy Approaches**
> - **好的先做**（把當下最佳的選擇先做掉）
> - 若能**事先知道解包含幾個元素**，可以更省時間（例如 cycle basis 的 $k$，湊滿就停）

---

## 重點整理

| 概念 | 重點 | 注意事項 |
|---|---|---|
| Dijkstra 的想法 | 分 $S$（已處理）與 $V-S$（未處理），每次挑 $L$ 最小的點 | 比的是從 $v_0$ 的**累積距離**，不是單一邊 |
| 與 MST 的差異 | MST 比單一邊權重；Dijkstra 比整條路徑 | 這是最容易混淆的地方 |
| 更新公式 | $L(w) = \min\big(L(w),\ L(u) + c(u,w)\big)$ | 邊權重必須**非負** |
| Dijkstra 複雜度 | $O(n^2)$ | $n + (n-1) + \cdots + 1$ |
| Dijkstra 是 optimal | 問題 lower bound $\Omega(m) = \Omega(n^2)$，與演算法相等 | 每條邊都必須被檢查 |
| Linear merge | 比較兩串列第一個元素，小的輸出 | $O(m+n-1) = O(m+n)$ |
| 2-way merge 的關鍵 | **短的串列先合併** | 先合併的會被後續重複掃描 |
| 2-way merge tree | 每次取最小的兩棵樹合併 | 總成本 = 所有內部節點之和 |
| 2-way merge 複雜度 | $O(n\log n)$ | Min-heap 排序 + binary search 插入 |
| 正確性證明 | 歸納法 + 預備工作（最小兩個在最深層且為 brothers） | 最小 merge tree **可能不唯一** |
| Huffman code | 2-way merge tree 用在頻率上 | Lower frequency → longer code |
| Ring sum | $A \oplus B = (A\cup B) - (A\cap B)$ | 即對稱差 / XOR |
| Cycle basis | 任何 cycle 都能由 basis 以 $\oplus$ 產生 | 同一張圖的 cycle basis 不唯一 |
| Cycle basis 大小 | $k = \lvert E\rvert - \lvert V\rvert + 1$ | 由 spanning tree 推得 |
| Cycle 的數量 | $\sum_{i=2}^{n} C^n_i (i-1)! > (n-1)!$ | **Exponential**；實作時不必全求 |
| 檢查線性相依 | Gaussian elimination（對列做 XOR） | 某列變全 0 → 該 cycle 可被組合，刪除 |
| Greedy 原則 | 好的先做；知道解的元素個數可提早停止 | |
