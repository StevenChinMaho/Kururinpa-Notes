# 段考全範圍攻略：Ch4 系統 ODE / 相平面 ＋ Ch11 Fourier 分析

> Advanced Engineering Mathematics (Kreyszig, 10th Edition) — Ch4 & Ch11

---

## 總覽：兩章在考什麼

| 章節 | 核心問題 | 一句話判斷法 |
|---|---|---|
| Ch4 | 給一個（或一組）一階線性 ODE 系統 $\mathbf{y}' = A\mathbf{y}$，求解 / 分類相圖行為 | 看到 **矩陣 + 微分** 或 **電路 RLC 聯立** → Ch4 |
| Ch11 | 給一個週期函數或非週期函數，展開成三角級數 / 積分 / 變換 | 看到 **週期函數圖形**、$\int_{-\pi}^{\pi}$、$\int_{-\infty}^{\infty}$ → Ch11 |

兩章解題的共通骨架都是：**先分類題型 → 套公式骨架 → 處理特殊情形（重根、邊界、对称性）**。

---

# Part A. Ch4 — Systems of ODEs, Phase Plane, Qualitative Methods

## A0. 整章題型總表

| 小節 | 題型 | 判斷依據 | 核心工具 |
|---|---|---|---|
| 4-1 | 一階系統求齊次解 / 高階 ODE 轉系統 | 看到 $LI'' + RI' + \frac{I}{C}$ 或 $y'' = \cdots$ 要你 "convert to system" | 特徵值 $\det(A-\lambda I)=0$ |
| 4-3 | 求一般解（含初始條件） | $\mathbf{y}' = A\mathbf{y}$，可能附 $y_1(0), y_2(0)$ | 特徵值＋特徵向量，再代入初值解 $c_1, c_2$ |
| 4-4 | 判別臨界點種類（在原點） | 系統本身是線性 $\mathbf{y}'=A\mathbf{y}$，要你說明 critical point 的「型態」 | discriminant：$p,q,\Delta$ |
| 4-5 | 非線性系統的臨界點分類（Linearization） | 系統含 $y_1^2, \cos y_1, y_1y_2$ 等非線性項 | 先求臨界點 → 平移到原點 → 忽略高階項 → 套 4-4 |
| 4-6 | 非齊次系統求特解（驅動項為 $e^{at}, t, \sin t$） | $\mathbf{y}' = A\mathbf{y} + \mathbf{g}(t)$，$\mathbf{g}(t) \neq 0$ | Checking table method（類比 Ch2 待定係數法）|

> 口訣：**4-1/4-3 是「解」，4-4/4-5 是「看」，4-6 是「加料」。**

---

## A1. 如何把問題轉成系統 $\mathbf{y}' = A\mathbf{y}$

### 情境 1：高階 ODE → 系統
$$y'' + ay' + by = 0$$
令 $y_1 = y,\ y_2 = y' = y_1'$，則 $y_2' = y'' = -ay_2 - by_1$

$$\mathbf{y}' = \begin{bmatrix} 0 & 1 \\ -b & -a \end{bmatrix}\mathbf{y}$$

### 情境 2：RLC 電路網路 → 系統
電路題（4-1 的 Prob.7、4-3 的 Prob.19/17/19）共通做法：

1. 對每個迴路寫 $LI' + RI + \frac{1}{C}\int I\,dt = E(t)$（或其等價的迴路電壓方程）
2. **務必先定義電流正方向**（順時針為正、逆時針為負），否則符號會整題錯
3. 對含 $\int I\,dt$ 的項先微分一次，消掉積分符號，整理成 $I_1', I_2'$ 的聯立式
4. 寫成矩陣形式 $\mathbf{I}' = A\mathbf{I} (+ \mathbf{g}(t))$

> 範例（Ch4-1 Prob.19、Ch4-3 Prob.17/19 都是同一張電路圖 Fig.99 的變化題）：
> $$\frac{1}{C}\int I_1\,dt + R(I_1-I_2) = 0,\quad LI_2' + R(I_2-I_1)=0$$
> 代入數值整理後得到 $\mathbf{I}' = A\mathbf{I}$，再走特徵值流程。

---

## A2. 求齊次解 $\mathbf{y}^{(h)}$：特徵值法（4-1、4-3 共用）

### 標準流程

1. 設 $\mathbf{y} = \mathbf{x}e^{\lambda t}$，代入得 $(A-\lambda I)\mathbf{x} = 0$
2. 解特徵方程 $\det(A-\lambda I) = 0$（二階系統會得到 $\lambda^2 - p\lambda + q = 0$ 的形式）
3. 對每個 $\lambda$ 解出對應特徵向量 $\mathbf{x}$
4. 寫出 $\mathbf{y} = c_1 \mathbf{x}^{(1)} e^{\lambda_1 t} + c_2 \mathbf{x}^{(2)} e^{\lambda_2 t}$
5. 若題目給初始條件，代入 $t=0$ 解 $c_1, c_2$

### 三種特徵值情況對照

| 特徵值型態 | 範例（本次範圍出現過） | 解的形式 |
|---|---|---|
| 相異實根 $\lambda_1 \neq \lambda_2$ | Ch4-3 Prob.1, 5, 11, 15 | $\mathbf{y} = c_1\mathbf{x}^{(1)}e^{\lambda_1 t} + c_2\mathbf{x}^{(2)}e^{\lambda_2 t}$ |
| 共軛複根 $\lambda = \alpha \pm i\beta$ | Ch4-6 Prob.13（$\lambda = \pm 2i$） | 特徵向量也是共軛複數，可代複數係數直接寫，或轉成 $\cos,\sin$ 實數形式 |
| 重根（少見但要會） | — | 需要 generalized eigenvector，本範圍未出現，但要知道存在 |

> **解題小心點**：特徵向量代入驗證時，建議直接代回 $(A-\lambda I)\mathbf{x}=0$ 檢查是否為零向量，避免代錯符號（Ch4-3 Prob.11、15 都是分數係數，最容易算錯）。

---

## A3. 求非齊次特解 $\mathbf{y}^{(p)}$：Checking Table Method（4-6）

> 這是 Ch2 待定係數法的「向量版」，**強烈建議先複習 Ch2 checking table**。

### 判斷驅動項 $\mathbf{g}(t)$ 該怎麼設 $\mathbf{y}^{(p)}$

| $\mathbf{g}(t)$ 形式 | 設 $\mathbf{y}^{(p)}$ 形式 | 範例 |
|---|---|---|
| 常數向量 $e^{at}$ 型（$a$ 不是特徵值） | $\mathbf{y}^{(p)} = \begin{bmatrix}c_1\\c_2\end{bmatrix}e^{at}$ | Ch4-6 Prob.3：$\mathbf{g}=e^{2t}$ 型 |
| 一次多項式 $t$ | $\mathbf{y}^{(p)} = \begin{bmatrix}a_1\\b_1\end{bmatrix}t + \begin{bmatrix}a_2\\b_2\end{bmatrix}$ | Ch4-6 Prob.5 |
| 三角函數 $\sin t, \cos t$ | $\mathbf{y}^{(p)} = \begin{bmatrix}a_1\\b_1\end{bmatrix}\sin t + \begin{bmatrix}a_2\\b_2\end{bmatrix}\cos t$ | Ch4-6 Prob.13 |
| 週期方波 / 三角波（Fourier 級數形式） | $\mathbf{y}^{(p)} = K_0 + \sum K_n\cos nt + M_n\sin nt$（**跨章整合題**，見 Ch11-3） | Ch4-6 衍生 + Ch11-3 Prob.11/17/19 |

### 解題步驟

1. 代入 $\mathbf{y}^{(p)\prime} = A\mathbf{y}^{(p)} + \mathbf{g}(t)$
2. 比較係數，列出線性方程組求未知係數
3. **若 $\mathbf{g}(t)$ 含週期項（sin/cos 多項），記得分別比較每個 $\cos nt$ 與 $\sin nt$ 項的係數**，不要混在一起解
4. 答案 = $\mathbf{y}^{(h)} + \mathbf{y}^{(p)}$

> 易錯點：Ch4-6 Prob.13 是複數特徵值＋三角函數驅動項的組合，初始條件代入時要小心複數運算（$2i \times c_1$ 之類）。

---

## A4. 臨界點分類（4-4）：discriminant 判別法

### 公式骨架

對 $\mathbf{y}' = A\mathbf{y} = \begin{bmatrix}a_{11}&a_{12}\\a_{21}&a_{22}\end{bmatrix}\mathbf{y}$：

$$p = a_{11}+a_{22} \ (\text{trace}),\quad q = \det(A),\quad \Delta = p^2 - 4q$$

### 判別表（**整張考卷最重要的一張表**）

| 條件 | 臨界點種類 | 穩定性 |
|---|---|---|
| $q < 0$ | **Saddle point（鞍點）** | 不穩定（unstable） |
| $q > 0,\ \Delta > 0$ | **Node（節點）** | $p<0$ 穩定／$p>0$ 不穩定 |
| $q > 0,\ \Delta = 0$ | Node（臨界節點） | 同上看 $p$ 符號 |
| $q > 0,\ \Delta < 0,\ p \neq 0$ | **Spiral point（螺旋點）** | $p<0$ 穩定／$p>0$ 不穩定 |
| $q > 0,\ \Delta < 0,\ p = 0$ | **Center（中心點）** | 穩定（stable，但非漸近穩定） |

> 圖像記憶（書中圖示，橫軸 $p$、縱軸 $q$）：
> - $q<0$（橫軸下方）一律是 saddle
> - $q>0$ 且 $p=0$（縱軸正向）→ center
> - $q>0$ 且偏離 $p=0$，看 $\Delta$ 落在 node／spiral 哪個區塊

### 第一步：先求臨界點

$$\frac{dy_2}{dy_1} = \frac{a_{21}y_1+a_{22}y_2}{a_{11}y_1+a_{12}y_2} = \frac{0}{0}\ (\text{undetermined})$$

解聯立 $\Rightarrow$ 通常得到 $(0,0)$（線性系統）或多個點（非線性系統，見 A5）。

---

## A5. 非線性系統的臨界點分類（4-5）：Linearization 流程

這是 **本範圍最容易在考試丟分** 的一節，因為步驟多、容易漏掉「移回原點」。完整流程：

### Step 1：寫出 $\mathbf{y}' = \mathbf{f}(\mathbf{y}) = A\mathbf{y} + \mathbf{h}(\mathbf{y})$

把線性項（$A\mathbf{y}$）與非線性項（$\mathbf{h}(\mathbf{y})$：含 $y_1^2, y_2^2, \cos, \sin$ 等）分開寫。

### Step 2：求所有臨界點

令 $\frac{dy_2}{dy_1} = \frac{0}{0}$，解出**所有**滿足 $f_1=0, f_2=0$ 的點（可能不只一個！）

### Step 3：對每個臨界點分別判斷

- **若臨界點是 $(0,0)$**：直接忽略 $\mathbf{h}(\mathbf{y})$（因為在原點附近高階項可忽略），套 A4 的 $p,q,\Delta$ 判別法
- **若臨界點是 $(a,b) \neq (0,0)$**：必須先做 **平移（translation）**：
  $$\tilde{y}_1 = y_1 - a,\quad \tilde{y}_2 = y_2 - b$$
  代回原方程，展開、化簡到只留線性項（高階項再次被忽略），得到新系統 $\tilde{\mathbf{y}}' = \tilde{A}\tilde{\mathbf{y}}$，再對 $\tilde{A}$ 套 A4 判別法

> **核心心法**：「對非原點的臨界點，永遠是『移過去、丟掉非線性項、再判斷』，原系統的臨界點種類 = 平移後系統在原點的種類。」

### 易錯點整理

| 易錯情境 | 範例 | 提醒 |
|---|---|---|
| 三角函數線性化要用 Taylor 展開 | Ch4-5 Prob.11（$\cos 2y$） | 善用 $\cos x = \sin(\frac{\pi}{2}-x)$ 轉換後，在新變數=0處取 Taylor 一階項 |
| 多個臨界點時，**每個點的穩定性可能不同** | Ch4-5 Prob.5、7、9、11 | 不要只算一個點就交卷；通常一個是 center/spiral，另一個是 saddle |
| 參數化的臨界點（含 $n\pi$） | Ch4-5 Prob.11 | 要分 $n$ 為奇數／偶數兩種情況分別討論 |

---

## A6. Ch4 解題判斷流程圖（文字版）

```
看到 Ch4 題目
│
├─ 題目是高階 ODE 或電路？ → 先轉換成 y' = Ay (+ g(t)) 系統  [A1]
│
├─ 系統是否含非線性項 (y², cos, sin of yᵢ)？
│   ├─ 是 → 走 4-5 流程：求所有臨界點 → 逐點線性化 → 套判別表  [A5 + A4]
│   └─ 否 → 系統本身線性 y' = Ay
│       │
│       ├─ 題目要求「求解」(solve / general solution)？
│       │   ├─ g(t) = 0        → 只需 A2（特徵值法）
│       │   └─ g(t) ≠ 0        → A2 求 y_h，再用 A3 checking table 求 y_p [4-6]
│       │
│       └─ 題目要求「分類臨界點 / 穩定性 / 軌跡型態」？
│           → 直接套 A4 判別表 (p, q, Δ)
```

---

# Part B. Ch11 — Fourier Analysis

## B0. 整章題型總表（**最關鍵的分類依據是「定義域」**）

| 小節 | 題型 | 判斷依據 | 核心工具 |
|---|---|---|---|
| 11.1–11.2 | Fourier 級數（週期 $2\pi$ 或 $2L$） | 函數**週期性**給出，定義在有限區間並重複 | $a_0, a_n, b_n$ 三條公式 |
| 11.3 | Fourier 級數應用：ODE 強迫振動 | $y'' + \omega^2 y = r(t)$，$r(t)$ 是週期函數 | 先把 $r(t)$ 展開成 Fourier 級數，再逐項比較係數求解 |
| 11.4 | 均方誤差 / Parseval 等式 | 題目問「誤差」或「求 $\sum \frac{1}{n^2}$ 之類的級數和」 | Parseval's identity |
| 11.7 | Fourier 積分（非週期函數，連續頻譜） | 函數**只定義一次、不重複**，且要證明 $\int_0^\infty (\cdots)\,dw = f(x)$ 的形式 | $A(w), B(w)$ 積分公式 |
| 11.8 | Fourier 餘弦/正弦變換 | 題目明確要 "find the cosine/sine transform $\hat f_c(w)$" | $\hat f_c(w), \hat f_s(w)$ 公式 |
| 11.9 | Fourier 變換（複數型）/ DFT / FFT | 題目要複數變換 $\hat f(w)$，或給離散訊號向量、矩陣 $F_N$ | $\hat f(w)=\frac{1}{\sqrt{2\pi}}\int f(x)e^{-iwx}dx$；DFT 矩陣 $F_N=[w^{nk}]$ |

> **第一道判斷關卡**：函數是「週期性重複」還是「只活一次（非週期）」？
> - 週期 → 11.1-11.4（級數）
> - 非週期、定義在 $(-\infty,\infty)$ 或半線 → 11.7-11.9（積分/變換）

---

## B1. Fourier 級數公式骨架（11.1 / 11.2）

### 週期 $2\pi$（11.1）

$$a_0 = \frac{1}{2\pi}\int_{-\pi}^{\pi} f(x)\,dx,\quad a_n = \frac{1}{\pi}\int_{-\pi}^{\pi} f(x)\cos nx\,dx,\quad b_n = \frac{1}{\pi}\int_{-\pi}^{\pi} f(x)\sin nx\,dx$$

### 週期 $2L$（11.2，一般化版本）

$$a_0 = \frac{1}{2L}\int_{-L}^{L} f(x)\,dx,\quad a_n = \frac{1}{L}\int_{-L}^{L} f(x)\cos\frac{n\pi x}{L}dx,\quad b_n = \frac{1}{L}\int_{-L}^{L} f(x)\sin\frac{n\pi x}{L}dx$$

> 兩組公式結構完全一樣，只是把 $\pi \to L$、$nx \to \frac{n\pi x}{L}$。**先確認題目的週期是多少**，決定用哪一組。

### 對稱性判斷表（**省一半計算量的關鍵**）

| $f(x)$ 對稱性 | 結果 | 原因 |
|---|---|---|
| 偶函數（even） | $b_n = 0$，只需算 $a_0, a_n$ | $f(x)\sin nx$ 為奇函數，對稱區間積分為 0 |
| 奇函數（odd） | $a_0 = a_n = 0$，只需算 $b_n$ | $f(x)\cos nx$ 為奇函數 |
| 無明顯對稱 | 三個係數都要算（分段積分） | Ch11-1 Prob.13、Ch11-1 Prob.19 |

> 範例：Ch11-1 Prob.21、Ch11-2 Prob.9/17 都先判斷奇偶性再下手，省去大半積分計算。

### 積分技巧對照表

| 被積函數型態 | 方法 | 公式 |
|---|---|---|
| $x\cos nx,\ x\sin nx,\ x^2\cos nx$ 等多項式×三角函數 | **分部積分（integration by parts）**，反覆使用 | $\int x\cos nx\,dx = \frac{x\sin nx}{n}+\frac{\cos nx}{n^2}$ |
| $\sin\alpha x \cos\beta x,\ \sin\alpha x\sin\beta x,\ \cos\alpha x\cos\beta x$（兩個不同頻率三角函數相乘） | **Product-to-Sum 公式** | $\sin\alpha x\cos\beta x = \frac{\sin(\alpha+\beta)x+\sin(\alpha-\beta)x}{2}$（另外兩式類似） |

> **重要陷阱**：用 Product-to-Sum 時，若分母出現 $1-n$ 或 $\alpha-\beta$，**務必另外檢查 $n=1$（或 $\alpha=\beta$）的特殊情形**，因為原本的代入公式在那裡會除以零！（見 Ch11-2 Prob.29(a)(b) 對 $a_1, b_1$ 的單獨處理）

---

## B2. Fourier 正弦 / 餘弦級數（半域展開）

當題目只給 $0<x<L$ 的函數，**要你自己決定延伸成奇函數或偶函數**：

| 假設 | 用途 | 公式 |
|---|---|---|
| 視為偶函數延伸 | Fourier **cosine** series | $a_0=\frac{1}{L}\int_0^L f\,dx,\ a_n=\frac{2}{L}\int_0^L f\cos\frac{n\pi x}{L}dx$ |
| 視為奇函數延伸 | Fourier **sine** series | $b_n=\frac{2}{L}\int_0^L f\sin\frac{n\pi x}{L}dx$ |

> 同一個函數，題目會要求兩種延伸都做一次（見 Ch11-2 Prob.23），**答案會完全不同**，因為延伸方式不同。

---

## B3. Fourier 級數應用：強迫振動（11.3）

### 題型識別

$$y'' + \omega^2 y = r(t),\quad r(t)\text{ 是週期函數（或分段給出但具週期性）}$$

### 解題流程（**這是大題，分數通常較高**）

1. **先求齊次解**：特徵方程 $\lambda^2+\omega^2=0 \Rightarrow \lambda=\pm\omega i$
   $$y_h = A\cos\omega t + B\sin\omega t$$
2. **若 $r(t)$ 是簡單三角函數**（如 $\sin t$）：直接設 $y_p = K\cos t + M\sin t$，比較係數（注意：若 $\omega=1$ 會共振，分母為 0，題目通常會避開或另外討論）
3. **若 $r(t)$ 是週期方波/三角波等分段函數**：
   - Step A：先把 $r(t)$ 展開成 Fourier 級數（套 B1）
   - Step B：設 $y_p = K_0 + \sum (K_n\cos nt + M_n \sin nt)$
   - Step C：代入 ODE，**逐個頻率比較係數**：
     $$(\omega^2-n^2)K_n = (\text{對應的 } a_n),\quad (\omega^2-n^2)M_n = (\text{對應的 } b_n)$$
   - 若系統含一次導數項（如 RLC 電路 $I''+10I'+10I=\cdots$），$\cos nt$ 與 $\sin nt$ 項會**耦合**（$K_n$ 與 $M_n$ 互相牽涉），需聯立解兩個方程

### RLC 電路版本（Ch11-3 Prob.17/19）

$$LI'' + RI' + \frac{I}{C} = E'(t)$$

> 注意：這裡用的是 $E'(t)$（**電壓的微分**），不是 $E(t)$ 本身！先對 $E(t)$ 微分，再展開 Fourier 級數。

---

## B4. 均方誤差與 Parseval's Identity（11.4）

### 兩個題型

**(a) 求 Minimum Square Error $E^*$：**
$$E^* = \int_{-\pi}^{\pi} f^2\,dx - \pi\left[2a_0^2+\sum_{n=1}^{\infty}(a_n^2+b_n^2)\right]$$
流程：先求出 $a_0,a_n,b_n$（即第一步 Fourier 展開），代公式即可，最後留下 $\sum$ 用 $n$ 值代入逐項計算。

**(b) 求級數和（如 $\sum \frac{1}{n^2}=\frac{\pi^2}{6}$ 之類）：**

利用 **Parseval's identity**：
$$2a_0^2+\sum_{n=1}^{\infty}(a_n^2+b_n^2) = \frac{1}{\pi}\int_{-\pi}^{\pi} f^2\,dx$$

> 解題核心：**先找到一個熟悉的 Fourier 級數**（題目通常會提示「參考 Example 1」或某個習題），代入其係數 $a_n, b_n$，再用上式反推目標級數和。

| 範例 | 用到的展開 | 結果 |
|---|---|---|
| Ch11-4 Prob.11：求 $\sum\frac{1}{n^2}\ (n=1,3,5\ldots)$ | 方波 $f(x)=\pm1$ 的 Fourier sine 展開 | $\frac{\pi^2}{8}$ |
| Ch11-4 Prob.12：求 $\sum\frac{1}{n^4}$ | $f(x)=x^2$ 的 Fourier cosine 展開 | $\frac{\pi^4}{90}$ |

---

## B5. Fourier 積分（11.7）：非週期函數的「連續版」級數

### 公式骨架

$$f(x) = \int_0^{\infty} \big[A(w)\cos wx + B(w)\sin wx\big]\,dw$$
$$A(w) = \frac{1}{\pi}\int_{-\infty}^{\infty} f(v)\cos wv\,dv,\quad B(w)=\frac{1}{\pi}\int_{-\infty}^{\infty} f(v)\sin wv\,dv$$

### 判斷該用哪個積分

| $f(x)$ 性質 | 用哪個公式 | 為什麼 |
|---|---|---|
| 偶函數，或定義在 $(0,\infty)$、自然延伸為偶 | **Fourier cosine integral**：$f(x)=\int_0^\infty A(w)\cos wx\,dw,\ A(w)=\frac{2}{\pi}\int_0^\infty f(v)\cos wv\,dv$ | 只剩 cos 項，sin 項自動為 0 |
| 奇函數 | **Fourier sine integral**：同理換成 $B(w)$ 與 sin | — |
| 一般函數（不對稱） | 用完整版 $A(w), B(w)$ 都要算 | 例：Ch11-7 Prob.1（$f(v)=\pi e^{-v}$，只在 $v>0$ 定義，非偶非奇） |

### 解題技巧

- 遇到 $\int_0^\infty e^{-v}\cos wv\,dv$ 或 $\int_0^\infty e^{-v}\sin wv\,dv$ 這類**指數×三角函數積分**，標準做法是**兩次分部積分後解方程**（積分會繞回自己，視為未知數解出）：
  $$\int_0^\infty e^{-v}\cos wv\,dv = \frac{1}{1+w^2}$$
- 遇到 $\int \sin\alpha v\cos\beta v\,dv$（如 Ch11-7 Prob.11），用 Product-to-Sum，**同樣要檢查 $\alpha=\beta$ 的特殊情形**（B1 提過的陷阱再次出現）

---

## B6. Fourier 餘弦 / 正弦變換（11.8）

### 公式

$$\hat{f}_c(w) = \sqrt{\frac{2}{\pi}}\int_0^{\infty} f(x)\cos wx\,dx,\qquad \hat{f}_s(w) = \sqrt{\frac{2}{\pi}}\int_0^{\infty} f(x)\sin wx\,dx$$

> 與 11.7 的 $A(w), B(w)$ **本質相同，只差一個 $\sqrt{\pi/2}$ 倍的常數因子**——這是這節最容易混淆但也最容易拿分的地方：**先確認題目是要 $A(w)/B(w)$（11.7 格式）還是 $\hat f_c/\hat f_s$（11.8 格式，多了 $\sqrt{\pi/2}$）**。

### 解題流程與 11.7 完全相同：

1. 確認 $f(x)$ 只在哪個區間非零（題目通常是分段函數）
2. 代入積分公式，分段積分（指數函數記得用分部積分解循環方程，如 B5）
3. 整理成單一分式表達式

---

## B7. 複數 Fourier 變換與 DFT/FFT（11.9）

### 連續版：Fourier Transform

$$\hat{f}(w) = \frac{1}{\sqrt{2\pi}}\int_{-\infty}^{\infty} f(x)e^{-iwx}\,dx$$

解題核心：
- 拆成 $e^{-iwx} = \cos wx - i\sin wx$（Euler's formula），或直接對指數積分（視題目給的 $f(x)$ 形式選擇）
- 含 $|x|$ 的函數（如 Ch11-9 Prob.9）要先**分段拆掉絕對值**（$-1<x<0$ 與 $0<x<1$ 分開積分）

### 離散版：DFT 與 Fourier 矩陣

$$\hat{f} = F_N f,\qquad F_N = [w^{nk}],\quad w = w_N = e^{-\frac{2\pi i}{N}}$$

#### 解題流程（純線性代數操作，不需要積分）

1. 算出 $N$ 對應的 $w$（**先化簡 $w$ 的具體數值**，例如 $N=4 \Rightarrow w=-i$；$N=2 \Rightarrow w=-1$）
2. 建出 $F_N$ 矩陣，每個元素 $w^{nk}$（$n,k=0,1,\ldots,N-1$）
3. 矩陣乘向量 $f$，得到 $\hat f$ 的展開式

| $N$ | $w=e^{-2\pi i/N}$ 化簡 | 範例 |
|---|---|---|
| 2 | $w=-1$ | Ch11-9 Prob.21 |
| 4 | $w=-i$ | Ch11-9 Prob.19 |

> $F_N$ 矩陣是對稱且每行/列有規律性（$w^{nk}$ 的指數會超過 $N$ 時，用 $w^N=1$ 化簡），**手算 $N=4$ 以下完全可行**，這是 FFT 加速前的「直接硬算版」，要先搞懂這個才能理解 FFT 為什麼要做 butterfly 分解（複雜度 $O(N^2)\to O(N\log N)$）。

---

## B8. Ch11 解題判斷流程圖（文字版）

```
看到 Ch11 題目
│
├─ 函數是「週期性給定」的嗎？
│   │
│   ├─ 是 → Fourier 級數系列
│   │   ├─ 題目只給半域 (0,L)，要你自選奇偶延伸？ → B2（sine/cosine series）
│   │   ├─ 純粹要展開成級數 (a₀,aₙ,bₙ)？        → B1
│   │   ├─ 題目是 y''+ω²y=r(t) 或 RLC 電路？     → B3（先展開 r(t)，再逐頻比較係數）
│   │   └─ 題目問「誤差」或「求 Σ 1/n^k 級數和」？ → B4（Parseval's identity）
│   │
│   └─ 否（非週期，定義域是 (-∞,∞) 或單次給定）
│       │
│       ├─ 題目要證明 "∫...dw = f(x)" 的等式？        → B5（Fourier integral, A(w)/B(w)）
│       ├─ 題目明確寫 "find the cosine/sine transform"？ → B6（$\hat f_c, \hat f_s$，注意 √(2/π) 係數）
│       └─ 題目要複數變換、或給離散訊號向量/矩陣？      → B7（$\hat f(w)$ 積分版 或 DFT 矩陣版）
```

---

# Part C. 跨章整合重點（Ch4 ＋ Ch11 一起考的可能考法）

| 整合情境 | 對應範例 | 解法要點 |
|---|---|---|
| ODE 強迫振動的驅動力是週期方波 | Ch4-6（一般化）＋ Ch11-3 Prob.11/17/19 | 先用 Ch11 公式把驅動力展開成 Fourier 級數，再回到 Ch4-6 的「設 $y_p$ 並逐頻比較係數」流程 |
| RLC 電路：穩態電流 + 週期電壓源 | Ch4 電路建模 ＋ Ch11-3 Prob.17/19 | 建系統方程是 Ch4 技巧，求穩態解是 Ch11-3 技巧 |

> 這類題目分數通常最高，因為要兩章工具都會才能拿到全部分數。**建議把 A1（電路建模）、A3（checking table）、B1（Fourier 展開）、B3（逐頻比較係數）四塊一起複習。**

---

## 總複習檢查清單

### Ch4
- [ ] 會把高階 ODE / RLC 電路轉成矩陣系統
- [ ] 會用特徵值法求齊次解（含複數特徵值情形）
- [ ] 會用 checking table 求非齊次特解（含 $e^{at}, t, \sin t$ 多種驅動項）
- [ ] 記得 $p,q,\Delta$ 判別表，能秒判 saddle/node/spiral/center
- [ ] 非線性系統：會求所有臨界點、會做平移線性化、每個點分開判斷

### Ch11
- [ ] 看到函數先判斷「週期 vs 非週期」「奇 vs 偶」
- [ ] 熟練分部積分（多項式×三角函數）與 Product-to-Sum 公式
- [ ] 記得 Product-to-Sum 分母為 0 時要單獨檢查（$n=1$ 或 $\alpha=\beta$）
- [ ] 會用半域函數做 sine/cosine 級數展開
- [ ] 強迫振動題會展開 $r(t)$ 並逐頻比較係數（含一次導數耦合的情形）
- [ ] Parseval's identity 用法：由已知級數展開反推 $\sum 1/n^k$
- [ ] 分清楚 11.7（$A(w),B(w)$）與 11.8（$\hat f_c,\hat f_s$，多 $\sqrt{2/\pi}$ 係數）的差異
- [ ] DFT：會算 $w=e^{-2\pi i/N}$ 化簡值，會建 $F_N$ 矩陣
