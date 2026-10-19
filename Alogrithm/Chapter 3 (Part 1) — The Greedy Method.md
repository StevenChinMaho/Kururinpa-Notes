# Chapter 3 (Part 1) — The Greedy Method

> Introduction to the Design and Analysis of Algorithms: A Strategic Approach（R.C.T. Lee, S.S. Tseng, R.C. Chang, Y.T. Tsang）
> 課程投影片：Algorithms（洪宗貝 Tzung-Pei Hong）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Greedy method | 每次做**區域最佳（locally optimal）**的決策 |
| 適用與不適用的例子 | Special graph 可解；multi-stage graph 不可解 |
| 為何失敗 | Greedy 不會 look-ahead |
| Minimum Spanning Tree（MST） | 總權重最小的 spanning tree |
| Kruskal's algorithm | 由小到大加邊，不形成 cycle；$O(m\log m)$ |
| Set union / find | 判斷是否形成 cycle 的資料結構 |
| Prim's algorithm | 從一點出發擴張 partial tree；$O(n^2)$ |
| 正確性證明 | 兩者皆用反證法（proof by contradiction） |
| 兩者比較 | 稀疏圖用 Kruskal，稠密圖用 Prim |

---

## 1. 貪婪法簡介（The Greedy Method）

### 1.1 引例

> 從 `7, 8, 2, 6, 4, 3` 中挑三個數，使其和最大。

**步驟**：
1. 先排序：`8, 7, 6, 4, 3, 2`
2. 每次挑最大的，挑三次：**8、7、6**

每一次挑選都是當下的最佳選擇（best），三次累加後即為全域最佳解。

### 1.2 正式描述

**Problem**：從 n 個數中挑出 k 個，使這 k 個數的和最大。

```pascal
FOR i = 1 to k
    Pick out the largest number and
    delete this number from the input
ENDFOR
```

### 1.3 Greedy Method 的定義

> 假設一個問題可以由**一連串的決策（a sequence of decisions）**解決。
> Greedy method 讓**每個決策都是區域最佳（locally optimal）**的，
> 並期望這些區域最佳解最終累加成**全域最佳解（globally optimal solution）**。

### 1.4 適用範圍

| 情況 | 結果 |
|---|---|
| 某些問題 | 用 greedy method 得到 **optimal solution** |
| 其他問題 | 用 greedy method **不是 optimal**，但答案可接受（acceptable） |

> **只有少數**最佳化問題可以用 greedy method 解決。

---

## 2. 可行的例子：Special Graph 的最短路徑

**問題**：在下列圖中找 $v_0$ 到 $v_3$ 的最短路徑。

```
       ┌─ 5 ─┐      ┌─ 10 ─┐      ┌─ 7 ─┐
 v0 ───┼─ 1 ─┼─ v1 ─┼─  9 ─┼─ v2 ─┼─ 4 ─┼─ v3
       └─ 3 ─┘      ├─  2 ─┤      └─────┘
                    └─  6 ─┘
```

此圖的特色：每一段**只有一組平行邊**連接相鄰兩點，各段之間互相獨立。

**Greedy 解法**：每段各挑最小的邊

$$1 + 2 + 4 = 7$$

> ⚠️ **此法不適用於一般圖（Not suitable for general graphs）**，因為一般圖中各段的選擇會互相影響。

---

## 3. 失敗的例子：Multi-Stage Graph 的最短路徑

**問題**：在 multi-stage graph 中找 $v_0$ 到 $v_3$ 的最短路徑。

```
Stage 0        Stage 1         Stage 2        Stage 3
                v1,1  ─────────  v2,1
   v0  ─ 3 ─    v1,2  ─────────  v2,2  ─────    v3
       ─ 1 ─    v1,3  ─────────  v2,3
       ─ 5 ─    v1,4
       ─ 7 ─
```

| 方法 | 路徑 | 長度 |
|---|---|---|
| Greedy method | $v_0 \to v_{1,2} \to v_{2,1} \to v_3$ | $1 + 9 + 13 = 23$ |
| **Optimal** | $v_0 \to v_{1,1} \to v_{2,2} \to v_3$ | $3 + 3 + 1 = 7$ |

> **Greedy method does not work.**
> 第一步貪心選了最短的邊（1），但後續被迫走很長的邊。

### 3.1 為什麼失敗？

**沒有 look-ahead（不往前看）**，就像下棋只看眼前一步。

正確的做法需要往後看完整條路徑：

$$d_{\min}(V_0, V_3) = \min \begin{cases} 3 + d_{\min}(V_{1,1}, V_3) \\ 1 + d_{\min}(V_{1,2}, V_3) \\ 5 + d_{\min}(V_{1,3}, V_3) \\ 7 + d_{\min}(V_{1,4}, V_3) \end{cases}$$

### 3.2 解法

此問題可以用 **dynamic programming**（動態規劃）解決 → **Chapter 7** 介紹。

---

## 4. 最小生成樹（Minimum Spanning Tree, MST）

### 4.1 定義

給定圖 $G = (V, E)$，為 **weighted、connected、undirected** 圖。

| 名詞 | 定義 |
|---|---|
| Spanning tree | $T = (V, S)$，其中 $S \subseteq E$；包含所有頂點且無 cycle |
| Minimum spanning tree | **總權重最小**的 spanning tree |

MST 的三個關鍵字：**Tree（無環）、Connected（連通）、Minimum span（總長最小）**。

> MST 可定義在 **Euclidean space points**（平面點集）或 **graph**（圖）上。

### 4.2 範例圖

本章一直使用的範例圖（6 個頂點、10 條邊）：

| 邊 | 權重 | | 邊 | 權重 |
|---|---|---|---|---|
| 1–2 | 10 | | 2–6 | 25 |
| 3–6 | 15 | | 1–4 | 30 |
| 4–6 | 20 | | 3–5 | 35 |
| 2–5 | 40 | | 1–5 | 45 |
| 2–3 | 50 | | 5–6 | 55 |

其 MST 為 `{1-2, 3-6, 4-6, 2-6, 3-5}`，總權重 $10 + 15 + 20 + 25 + 35 = 105$。

---

## 5. Kruskal's Algorithm

### 5.1 概念

1. **先把所有邊由小到大排序**
2. **一個一個邊加入**
3. **只要不形成 cycle 就加**，直到有 $n-1$ 條邊

### 5.2 演算法

```pascal
Input:  A weighted, connected and undirected graph G = (V, E)
Output: A minimal spanning tree for G

T := ∅                                      { initially }
While T contains less than n-1 edges do
Begin
    Choose an edge (v, w) from E with the smallest weight
    Delete (v, w) from E
    If (the adding of (v, w) to T does not create a cycle in T) then
        Add (v, w) to T
    Else
        Discard (v, w)
End
```

### 5.3 範例追蹤

以 4.2 的圖為例：

| 順序 | Edge | Cost | 動作 | 目前的 spanning forest |
|---|---|---|---|---|
| 1 | (1,2) | 10 | 加入 | {1,2} {3} {4} {5} {6} |
| 2 | (3,6) | 15 | 加入 | {1,2} {3,6} {4} {5} |
| 3 | (4,6) | 20 | 加入 | {1,2} {3,4,6} {5} |
| 4 | (2,6) | 25 | 加入 | {1,2,3,4,6} {5} |
| 5 | (1,4) | 30 | **reject** | 1 與 4 同集合 → **形成 cycle** |
| 6 | (3,5) | 35 | 加入 | {1,2,3,4,5,6} ← 完成（5 條邊） |

---

## 6. Kruskal 的實作：Spanning Forest

### 6.1 資料結構

維護一個 **spanning forest**（由多棵 partial tree 組成）：

| 資料 | 內容 |
|---|---|
| Edges | 目前已選入的邊 |
| Vertex sets | 每個集合代表一棵 partial tree |

**範例**：某階段的部分結果
- $T$ = {12, 13, 14, 56, 67, 79, 68}
- Set 1 = {1, 2, 3, 4}
- Set 2 = {5, 6, 7, 8, 9}

### 6.2 如何判斷加邊會不會形成 cycle

| 情況 | 判斷 | 動作 |
|---|---|---|
| $u, v$ 在**同一個 set** | 會形成 cycle | **Discard** |
| $u \in S_1$、$v \in S_2$（**不同 set**） | 不會形成 cycle | 加入邊，並執行 $S_1 \cup S_2$ |

**例 1**：加入 (3, 4) — 3 與 4 都在 Set 1 = {1,2,3,4} → **形成 cycle**
**例 2**：加入 (4, 5) — 4 在 Set 1、5 在 Set 2 → **不形成 cycle**，產生新的 forest

$$\{1,2,3,4\} \cup \{5,6,7,8,9\} = \{1,2,3,4,5,6,7,8,9\}$$

### 6.3 Union 怎麼做？

用**指標（pointers）**串接：

```
{ 1, 2, 3 }    { 4, 5, 6 }    →    { 1, 2, 3, 4, 5, 6 }
     ↑              ↑                        ↑
     A              B                        A
```

把 B 的指標接到 A 即可 → **$O(1)$**

### 6.4 Union 的總時間

- 每加入一條邊做一次 union，每次 $O(1)$
- 共 $n-1$ 條邊 → **$O(n)$**

> 兩棵 partial tree 不會有重複的 vertices，所以可以直接串接。

### 6.5 Find 的時間：$O(\log^* n)$

**Finding**：找出某個值所屬的集合。用樹與指標實作（原理較複雜）：

$$O(\log^* n) = \underbrace{\log\log\log\cdots\log n}_{\text{iterated log}}$$

> **例**：$n = 65536$ → $\log_2 65536 = 16$ → $\log_2 16 = 4$
> 成長極慢，實務上**近似 $O(1)$**。

---

## 7. Kruskal 的時間複雜度

| 步驟 | 做法 | 複雜度 |
|---|---|---|
| 排序所有邊 | 由小到大 | $O(m\log m)$ |
| 判斷 cycle | 每條邊兩個端點各做一次 finding：$m \times O(1) \times 2$ | $O(m)$ |
| Set union | 共 $n-1$ 次，每次 $O(1)$ | $O(n)$ |

$$\text{Total} = O(m\log m) + O(m) + O(n) = O(m\log m) \quad (\because m \ge n)$$

**Worst case**（完全圖）：$m = \dfrac{n(n-1)}{2} \approx n^2$

$$O(m\log m) = O(n^2 \log n^2) = O(n^2 \log n)$$

（符號：$n = |V|$、$m = |E|$）

---

## 8. Kruskal 的正確性證明

### 8.1 證明策略

用**反證法**：
1. 假設它產生的不是 MST
2. 推出矛盾
3. 故它產生的是 MST

先假設**每條邊權重都不同**（$|e_1| < |e_2| < \cdots < |e_m|$），證完後再處理有相同權重的情況（給相同權重的邊設定優先順序）。

### 8.2 設定

- 設 Kruskal's algorithm 產生的樹為 $T$
- 設真正的 minimum spanning tree 為 $T'$
- 令**在 $T$ 卻不在 $T'$ 的最小邊**為 $e_i$
  → 代表 $e_1, e_2, \dots, e_{i-1}$ 同時在 $T$ 與 $T'$ 中

把 $e_i$ 加到 $T'$ → $T'$ 產生一個 **cycle**，此 cycle 包含 $e_i$ 和另一條**在 $T'$ 卻不在 $T$** 的邊 $e_j$。

### 8.3 兩種情況

**Case 1：$e_i > e_j$**

- $e_1, \dots, e_{i-1}$ 兩邊都有
- 依 Kruskal 由小到大挑選，$e_j$ 比 $e_i$ 小，**$e_j$ 應該先被選進 $T$** 才對
- 但 $e_j$ 不在 $T$ → **矛盾**

**Case 2：$e_i < e_j$**

- 從 $T'$ 移除 $e_j$、加入 $e_i$，仍是 spanning tree，但**總權重更小**
- 與「$T'$ 是 minimum spanning tree」**矛盾**

### 8.4 結論

兩種情況皆矛盾 → **找不到在 $T$ 中而不在 $T'$ 中的 $e_i$**
→ $T$ 中每條邊都在 $T'$ 中 → $T = T'$
→ Kruskal 產生的就是 MST。

有相同權重的邊時，用類似的證明即可。

---

## 9. Prim's Algorithm

### 9.1 概念

另一個求 MST 的演算法：**找最小的邊形成 partial MST**，逐步擴張。

1. 找最近的 edge
2. 連到 partial tree 的最近 edge
3. 重複上述步驟

> 可從**任一個 vertex** 開始（例如 node 5）。

### 9.2 演算法

| Step | 內容 |
|---|---|
| 1 | 任挑一點 $x \in V$，令 $A = \{x\}$、$B = V - \{x\}$ |
| 2 | 選 $(u,v) \in E$，$u \in A$、$v \in B$，且 $(u,v)$ 是 A、B 之間**權重最小**的邊 |
| 3 | 把 $(u,v)$ 加入樹；$A = A \cup \{v\}$、$B = B - \{v\}$ |
| 4 | 若 $B = \varnothing$ 則停止；否則回到 Step 2 |

**時間複雜度**：$O(n^2)$，其中 $n = |V|$

### 9.3 與 Kruskal 的差異

| | Kruskal | Prim |
|---|---|---|
| 擴張方式 | 可同時有多棵 partial tree（forest） | 只有**一棵** partial tree |
| 判斷依據 | 邊的全域排序 + 不形成 cycle | A 與 B 之間的最小邊 |

---

## 10. Prim 的正確性證明

一樣用**反證法**：

- 設 Prim 求得的 spanning tree 為 $T$，真正的 MST 為 $T'$
- 令在 $T$ 卻不在 $T'$ 的**第一條**不同邊為 $\overline{ab}$
- 依 Prim 的挑選過程，$a$ 在當時的 $A$ 中、$b$ 在 $B$ 中
- $T'$ 中必有另一條跨越 A、B 的邊 $\overline{cd}$

**推論**：把 $\overline{ab}$ 加入 $T'$、移除 $\overline{cd}$ → 得到一棵**更小**的 spanning tree

- 因為 Prim 是 greedy，**小的邊會先被放** → $|\overline{ab}| \le |\overline{cd}|$
- 所以 $T'$ 不是 minimum → **矛盾**

→ $\overline{ab}$ 本來就該在 $T'$ 上，故 Prim 產生的是 MST。

---

## 11. Prim 的實作與時間複雜度

### 11.1 關鍵問題

> 如何**快速找出** A 與 B 之間的最小邊？

**想法演進**：

| 狀態 | 做法 | 成本 |
|---|---|---|
| A 只有 1 點、B 有 $n-1$ 點 | 直接找 $x$ 到 B 各點的最小值 | $(n-1)$ 步 × $O(1)$ = $O(n)$ |
| A 有 2 點（$x, y$）、B 有 $n-2$ 點 | 求 $x \to B$ 的 min、求 $y \to B$ 的 min、再取兩者的 min | $x$ 的部分**之前已算過** → 利用此資訊減少時間 |

### 11.2 兩個陣列

| 陣列 | 內容 |
|---|---|
| $C_1[i]$ | 點 $i$ **所要連過去的邊**（連到哪個點），並標記 X（已加入 A）/ Y（還在 B） |
| $C_2[i]$ | 點 $i$ 連到 A 的**目前最小邊距離** |

### 11.3 操作規則

1. **初始**：任選起點，其 $C_1$ 全設為起點、標記起點為 X，$C_2$ 全設為 ∞
2. **檢查**剛加入的 X 點到其他 node 的距離，與 $C_2$ 現值**兩者取小**
   - 若新值較小 → 更新 $C_2$，且 **$C_1$ 的 node 要跟著改**
   - 若舊值較小 → $C_1$ 的 node 不變
3. 在 **Y 標記**的點中找 $C_2$ 最小者（用一個變數記錄 min node）
4. 將該點 $C_1$ 的 **Y 改為 X**（表示此 node 剛被加入），$C_2$ 設為 ∞
5. 以新改成 X 的 node 為準，回到步驟 2

> 連到自己的距離設為 ∞。

### 11.4 完整追蹤範例

**範例圖**（5 個頂點）：

| 邊 | 權重 | | 邊 | 權重 |
|---|---|---|---|---|
| 1–3 | 4 | | 2–3 | 7 |
| 1–5 | 2 | | 3–5 | 7 |
| 1–2 | 16 | | 4–5 | 9 |

**初始**：假設先選 vertex 3

| i | $C_1$ | 標記 | $C_2$ |
|---|---|---|---|
| 1 | 3 | Y | ∞ |
| 2 | 3 | Y | ∞ |
| 3 | 3 | **X** | ∞ |
| 4 | 3 | Y | ∞ |
| 5 | 3 | Y | ∞ |

**Round 1**：檢查 3 到各點的距離（3-1=4, 3-2=7, 3-4=無邊, 3-5=7）

| i | $C_1$ | 標記 | $C_2$ | |
|---|---|---|---|---|
| 1 | 3 | Y | **4** | ← **min，選 node 1 來連** |
| 2 | 3 | Y | 7 | |
| 3 | 3 | X | ∞ | 自己 |
| 4 | 3 | Y | ∞ | |
| 5 | 3 | Y | 7 | |

→ 加入邊 **1–3**；$C_1[1]$ 的 Y 改為 X，$C_2[1] = \infty$

**Round 2**：以新加入的 node 1 為準，與現值取小（1-2=16, 1-5=2）

| i | 原 $C_2$ | 1 到 i | 取小 | $C_1$ 變化 | |
|---|---|---|---|---|---|
| 2 | 7 | 16 | 7 | 不變（仍是 3） | |
| 4 | ∞ | ∞ | ∞ | 不變 | |
| 5 | 7 | **2** | **2** | **改為 1** | ← min，選 node 5 |

→ 加入邊 **5–1**；$C_1[5] = 1$ 且 Y 改為 X，$C_2[5] = \infty$

**Round 3**：以 node 5 為準（5-4=9，5-2 無邊）

| i | 原 $C_2$ | 5 到 i | 取小 | $C_1$ 變化 | |
|---|---|---|---|---|---|
| 2 | **7** | ∞ | **7** | 不變（仍是 3） | ← min，選 node 2 |
| 4 | ∞ | 9 | 9 | **改為 5** | |

→ 加入邊 **2–3**；$C_1[2]$ 的 Y 改為 X，$C_2[2] = \infty$

**Round 4**：以 node 2 為準（2-4 無邊）

| i | 原 $C_2$ | 2 到 i | 取小 | |
|---|---|---|---|---|
| 4 | **9** | ∞ | **9** | ← min，選 node 4 |

→ 加入邊 **4–5**；全部標記為 X，結束

**最終結果**

| i | $C_1$ | 標記 | $C_2$ |
|---|---|---|---|
| 1 | 3 | X | ∞ |
| 2 | 3 | X | ∞ |
| 3 | 3 | X | ∞ |
| 4 | 5 | X | 9 |
| 5 | 1 | X | ∞ |

**MST 的邊**：`1-3`、`2-3`、`4-5`、`5-1`，總權重 $4 + 7 + 9 + 2 = 22$

（完整的程式碼實作見課本。）

### 11.5 時間複雜度

- 每加入一個 vertex，要處理 $C_1$ 與 $C_2$ 兩個陣列：$O(n) \times 2 = O(n)$
- 共 n 個 vertices：$n \times O(n) = O(n^2)$

$$\boxed{O(n^2)}\quad \text{（average case 與 worst case 皆同）}$$

---

## 12. 兩種 MST 演算法的比較

| 演算法 | 時間複雜度 |
|---|---|
| Prim's algorithm | $O(n^2)$ |
| Kruskal's algorithm | $O(m\log m)$ |

| 圖的性質 | 較佳者 | 理由 |
|---|---|---|
| **稀疏圖**（$m \ll n^2$） | **Kruskal** | $m\log m$ 遠小於 $n^2$ |
| **稠密圖**（$m \approx n^2$） | **Prim** | $m\log m \approx n^2\log n > n^2$ |

---

## 重點整理

| 概念 | 重點 | 注意事項 |
|---|---|---|
| Greedy method | 每個決策都做區域最佳選擇 | 只有少數最佳化問題適用 |
| Special graph 最短路徑 | 每段各挑最小 → $1+2+4=7$ | 不適用於一般圖 |
| Multi-stage graph | Greedy 得 23，optimal 為 7 | Greedy 失敗，因為沒有 look-ahead |
| 失敗的解法 | Dynamic programming | Chapter 7 介紹 |
| MST 定義 | 總權重最小的 spanning tree | Tree + Connected + Minimum span |
| Kruskal | 邊由小到大加入，不形成 cycle | 可同時有多棵 partial tree |
| 判斷 cycle | 兩端點是否在同一 set | 同 set → discard；不同 set → union |
| Union | 用指標串接 | 每次 $O(1)$，共 $O(n)$ |
| Find | 用樹與指標 | $O(\log^* n)$，近似 $O(1)$ |
| Kruskal 複雜度 | $O(m\log m)$ | 排序主導；worst case $O(n^2\log n)$ |
| Prim | 從任一點出發，每次挑 A、B 間最小邊 | 只維護一棵 partial tree |
| Prim 實作 | $C_1$ 記連向哪個點、$C_2$ 記最小距離 | 更新 $C_2$ 時 $C_1$ 要跟著改 |
| Prim 複雜度 | $O(n^2)$ | Average 與 worst case 相同 |
| 正確性證明 | 兩者皆用反證法 | Kruskal 分 $e_i > e_j$、$e_i < e_j$ 兩種情況 |
| 如何選擇 | $m \ll n^2$ 用 Kruskal；$m \approx n^2$ 用 Prim | |
