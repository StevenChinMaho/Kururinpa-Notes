# 工程數學 Ch1–Ch2 段考全範圍攻略

> Engineering Mathematics（工程數學）｜ODE 解題策略總整理

---

## 全章題型一覽

| 題型 | 出現章節 | 核心判斷關鍵字 | 主要工具 |
|---|---|---|---|
| 直接積分 | Ch1-1 | $y' = f(x)$，右側僅含 $x$ | 直接積分 |
| 分離變數 | Ch1-1, Ch1-3 | $y' = g(x)h(y)$ | 分離變數法 |
| 正合方程式 | Ch1-4 | $M\,dx + N\,dy = 0$ | 檢驗 $\partial M/\partial y = \partial N/\partial x$ |
| 積分因子 | Ch1-4 | 正合條件不成立 | 找 $\mu(x)$ 或 $\mu(y)$ |
| 一階線性 ODE | Ch1-5 | $y' + p(x)y = r(x)$ 標準形 | Integrating Factor $M(x)$ |
| 齊次代換 | Ch1-3 | $y' = f(y/x)$ 結構 | 令 $u = y/x$ |
| Bernoulli 方程 | Ch1-5 | $y' + p(x)y = g(x)y^n$，$n\neq 0,1$ | 令 $u = y^{1-n}$ 線性化 |
| 正交軌跡 | Ch1-6 | "Find orthogonal trajectories" | $\tilde{y}' = -1/y'$ |
| 降階法 | Ch2-1 | 二階 ODE 缺 $x$ 或缺 $y$ | 令 $u = y'$ |
| 特徵方程 | Ch2-2, Ch2-5 | 常係數/Euler-Cauchy 齊次 ODE | $\lambda^2 + a\lambda + b = 0$ 或 $m^2+...=0$ |
| Wronskian 驗證 | Ch2-6 | 驗證線性獨立 | $W(y_1,y_2)\neq 0$ |
| 未定係數法 | Ch2-7 | 非齊次，$r(x)$ 為指數/多項式/三角 | 查表選 $y_p$ |
| 參數變分法 | Ch2-10 | 非齊次，$r(x)$ 形式複雜 | $y_p = -y_1\int\frac{y_2 r}{W}dx + y_2\int\frac{y_1 r}{W}dx$ |

---

## Ch1 — 一階 ODE

---

### 1. 直接積分法

**判斷條件**：方程式可以寫成 $y' = f(x)$，右側純粹只含 $x$。

**解法**：直接對兩邊積分。

$$y = \int f(x)\,dx + C$$

**Example（Ch1-1, Q1）**：$y' + 2\sin 2\pi x = 0$
$$y' = -2\sin 2\pi x \implies y = \cos 2\pi x + C$$

> ✦ 若遇到 $\int e^{ax}\cos bx\,dx$ 或 $\int e^{ax}\sin bx\,dx$，需**分部積分兩次**，把同一積分移項到同側解出。

---

### 2. 分離變數法（Separation of Variables）

**判斷條件**：可以改寫成 $g(y)\,dy = f(x)\,dx$ 的形式，即左側純含 $y$，右側純含 $x$。

**步驟**：
1. 整理讓 $y$ 相關都在左，$x$ 相關都在右
2. 兩邊各自積分
3. 套用初始條件求特解（若題目給 IVP）

**Example（Ch1-3, Q5）**：$yy' + 36x = 0$
$$y\,dy = -36x\,dx \implies \frac{1}{2}y^2 = -18x^2 + C \implies 36x^2 + y^2 = C$$

> ✦ 分離後若出現 $\int \frac{1}{y(y+1)}dy$，用**分式拆項（Partial Fractions）**處理：$\frac{1}{y(y+1)} = \frac{1}{y} - \frac{1}{y+1}$

---

### 3. 齊次方程式代換（Homogeneous Substitution）

**判斷條件**：方程式可改寫成 $y' = f\!\left(\dfrac{y}{x}\right)$ 的形式（右側 $x, y$ 的次數相同）。

**步驟**：
1. 令 $u = y/x$，則 $y = ux$，$y' = u'x + u$
2. 代入後 $u$ 可以用分離變數解出
3. 最後將 $u$ 換回 $y/x$

**Example（Ch1-3, Q17）**：$xy' = y + 3x^4\cos^2\!\left(\dfrac{y}{x}\right)$
$$u'x = 3x^3\cos^2 u \implies \int\sec^2 u\,du = \int 3x^2\,dx \implies \tan\frac{y}{x} = x^3 + C$$

---

### 4. 正合方程式（Exact Equation）

**判斷條件**：方程式為 $M\,dx + N\,dy = 0$，且

$$\frac{\partial M}{\partial y} = \frac{\partial N}{\partial x}$$

**步驟**：直接對 $M$ 積分（含 $x$）或對 $N$ 積分（含 $y$），取聯集得到 $F(x,y) = C$。

**Example（Ch1-4, Q1）**：$2xy\,dx + x^2\,dy = 0$
$$\frac{\partial(2xy)}{\partial y} = 2x = \frac{\partial(x^2)}{\partial x} \implies F = x^2y = C$$

---

### 5. 積分因子法（Integrating Factor）

**判斷條件**：正合條件 $\partial M/\partial y \neq \partial N/\partial x$，方程式不正合。

**步驟（找 $\mu(x)$）**：
1. 令 $\mu$ 僅含 $x$（令 $\partial\mu/\partial y = 0$）
2. 由正合條件推導出：$\dfrac{d\mu}{dx} = \dfrac{\partial_y M - \partial_x N}{N}\mu$（若商僅含 $x$）
3. 解這個一階 ODE 得到 $\mu(x) = e^{\int \ldots dx}$
4. 將原式乘以 $\mu$，再驗證正合後積分

**Example（Ch1-4, Q5）**：$\mu(x) = x^{-2}$，乘以後化為正合，解得 $x - y^2/x = C$

---

### 6. 一階線性 ODE（Linear First-Order ODE）

**標準形式**：$y' + p(x)y = r(x)$

**步驟**：
1. 求 Integrating Factor：$M(x) = e^{\int p(x)dx}$
2. 兩邊乘以 $M(x)$，左側合併為 $[M(x)y]'$
3. 對右側 $M(x)\cdot r(x)$ 積分
4. 解出 $y$

$$y = \frac{1}{M(x)}\left[\int M(x)\,r(x)\,dx + C\right]$$

> ✦ 若 $r(x)$ 在右側（非零），這是**非齊次**線性 ODE；若 $r(x)=0$ 則直接分離變數。

**Example（Ch1-5, Q9）**：$y' + y\sin x = e^{\cos x}$
$$M(x) = e^{-\cos x} \implies [M(x)y]' = 1 \implies y = e^{\cos x}(x + C)$$

---

### 7. Bernoulli 方程

**標準形式**：$y' + p(x)y = g(x)y^n$，$n\neq 0, 1$

**步驟**：
1. 令 $u = y^{1-n}$，則 $u' = (1-n)y^{-n}y'$
2. 原式兩邊乘以 $(1-n)y^{-n}$，化為關於 $u$ 的**線性 ODE**
3. 用一階線性 ODE 的方法解 $u$，最後換回 $y$

**Example（Ch1-5, Q23）**：$y' + xy = xy^{-1}$，令 $u = y^2$
$$u' + 2xu = 2x \implies u = 1 + Ce^{-x^2} \implies y^2 = 1 + Ce^{-x^2}$$

---

### 8. 正交軌跡（Orthogonal Trajectories）

**判斷條件**：題目要求找與已知曲線族**互相垂直**的曲線族。

**步驟**：
1. 對已知曲線 $F(x,y)=c$ 微分，求出斜率 $y'$
2. 正交軌跡斜率為 $\tilde{y}' = -1/y'$（負倒數）
3. 解新的 ODE 得到正交軌跡族

**Example（Ch1-6, Q11）**：$x^2+y^2=c$，斜率 $y'=-x/y$
$$\tilde{y}' = \frac{y}{x} \implies \tilde{y} = Cx \text{（一族過原點的直線）}$$

---

## Ch2 — 二階線性 ODE

---

### 9. 降階法（Reduction of Order）

**判斷條件**：二階 ODE，但可消去 $x$（方程式中無顯式 $x$）或已知一個特解 $y_1$。

**兩種常見形式**：

| 情況 | 代換 |
|---|---|
| ODE 缺 $x$（$F(y, y', y'')=0$） | 令 $u = y'$，$y'' = u\frac{du}{dy}$ |
| 已知 $y_1$，求 $y_2$ | 令 $y = uy_1$，代入化簡 |

**Example（Ch2-1, Q5）**：$yy'' = 3y'^2$，令 $u=y'$，$y''=u\frac{du}{dy}$
$$\frac{du}{u} = \frac{3dy}{y} \implies u = Cy^3 \implies y^2 = \frac{1}{C_3 x + C_4}$$

**Example（Ch2-1, Q9）**：已知 $y_1 = x^3$，令 $y = ux^3$，展開代入後化簡得 $y = Cx^3\ln|x| + C_2 x^3$

---

### 10. 常係數齊次 ODE — 特徵方程（Characteristic Equation）

**標準形式**：$y'' + ay' + by = 0$

**步驟**：令 $y = e^{\lambda x}$，代入得特徵方程 $\lambda^2 + a\lambda + b = 0$

| 根的類型 | 條件 | 通解 |
|---|---|---|
| 相異實根 $\lambda_1 \neq \lambda_2$ | $\Delta > 0$ | $y = c_1 e^{\lambda_1 x} + c_2 e^{\lambda_2 x}$ |
| 重根 $\lambda = \lambda_0$（double root） | $\Delta = 0$ | $y = (c_1 + c_2 x)e^{\lambda_0 x}$ |
| 複數根 $\lambda = \alpha \pm \beta i$ | $\Delta < 0$ | $y = e^{\alpha x}(A\cos\beta x + B\sin\beta x)$ |

---

### 11. Euler-Cauchy 方程（Ch2-5）

**標準形式**：$x^2 y'' + axy' + by = 0$

**步驟**：令 $y = x^m$，$y' = mx^{m-1}$，$y'' = m(m-1)x^{m-2}$，代入得 **Indicial Equation**：

$$m^2 + (a-1)m + b = 0$$

| 根的類型 | 通解 |
|---|---|
| 相異實根 $m_1 \neq m_2$ | $y = c_1 x^{m_1} + c_2 x^{m_2}$ |
| 重根 $m_0$ | $y = (c_1 + c_2 \ln x)x^{m_0}$ |
| 複數根 $\frac{1}{2} \pm \beta i$ | $y = \sqrt{x}(A\cos(\beta\ln x) + B\sin(\beta\ln x))$ |

> ✦ Euler-Cauchy 形式與常係數類比：$x$ 的冪次對應常係數的指數根，$\ln x$ 對應重根時的 $x$。

---

### 12. Wronskian 線性獨立驗證（Ch2-6）

**用途**：確認 $y_1, y_2$ 是否線性獨立，可作為一組基底（basis）。

$$W(y_1, y_2) = \begin{vmatrix} y_1 & y_2 \\ y_1' & y_2' \end{vmatrix} = y_1 y_2' - y_2 y_1'$$

若 $W \neq 0$，則 $y_1, y_2$ 線性獨立。

**Example（Ch2-6, Q9）**：$W(\cos 5x, \sin 5x) = \cos^2 5x + \sin^2 5x = 1 \neq 0$ ✔

---

### 13. 未定係數法（Method of Undetermined Coefficients）— Ch2-7

**使用前提**：非齊次項 $r(x)$ 屬於以下類型才能用此法。

#### $y_p$ 選取表

| $r(x)$ 的形式 | 選擇的 $y_p$ |
|---|---|
| $ke^{\gamma x}$ | $Ce^{\gamma x}$ |
| $kx^n$ | $K_n x^n + K_{n-1}x^{n-1} + \cdots + K_0$ |
| $k\cos\omega x$ 或 $k\sin\omega x$ | $K\cos\omega x + M\sin\omega x$ |
| $ke^{\alpha x}\cos\omega x$ 或 $ke^{\alpha x}\sin\omega x$ | $e^{\alpha x}(K\cos\omega x + M\sin\omega x)$ |

#### 三種情境

**Form 1**：$r(x)$ 單一項 → 直接查表選 $y_p$

**Form 2**：$y_p$ 的選擇已被 $y_h$ 使用 → 乘以 $x$（重根時乘以 $x^2$）

| $y_h$ 情況 | 修正方式 |
|---|---|
| $y_h$ 含 $e^{\gamma x}$（單根） | $y_p \to Cxe^{\gamma x}$ |
| $y_h$ 含 $e^{\gamma x}$ 和 $xe^{\gamma x}$（雙重根） | $y_p \to Cx^2 e^{\gamma x}$ |

**Form 3**：$r(x)$ 是多項之和 → $y_p$ 為各項對應選擇之和

$$\text{ex: }r(x) = 3e^{3x} - 5x^2 \implies y_p = Ce^{3x} + K_2x^2 + K_1x + K_0$$

**完整流程**：
1. 解齊次 ODE，得 $y_h$
2. 查表選 $y_p$（注意 Form 2 衝突問題）
3. 將 $y_p$, $y_p'$, $y_p''$ 代回，比較係數求未知數
4. $y = y_h + y_p$，代初始條件求 $c_1, c_2$

---

### 14. 參數變分法（Variation of Parameters）— Ch2-10

**使用時機**：$r(x)$ 形式複雜、無法查表，或 Euler-Cauchy 的非齊次版本。

**公式（$y''$ 係數必須為 1！）**：

$$y_p = -y_1 \int \frac{y_2 r(x)}{W}\,dx + y_2 \int \frac{y_1 r(x)}{W}\,dx$$

其中 $W = W(y_1, y_2)$ 為 Wronskian。

**步驟**：
1. 解齊次，求得基底 $y_1, y_2$ 及 Wronskian $W$
2. **確認 $y''$ 係數為 1**（若不是先除以最高次係數）
3. 代入公式，計算兩個積分
4. 合併 $y = y_h + y_p$

**Example（Ch2-10, Q3）**：$x^2 y'' - xy' - 3y = x^2$
- 先除以 $x^2$：$y'' - \frac{y'}{x} - \frac{3y}{x^2} = 1$，故 $r(x) = 1$
- $y_1 = x^3$, $y_2 = x^{-1}$, $W = -4x$
- $y_p = -\frac{x^2}{3}$，最終 $y = c_1 x^3 + c_2 x^{-1} - \frac{x^2}{3}$

---

## 判斷流程總整理

```
拿到一階 ODE
├── 形如 y' = f(x) 只含 x？ → 直接積分
├── 可分離？y' = g(x)h(y) → 分離變數
├── y' = f(y/x) 齊次結構？ → 令 u = y/x 代換
├── 形如 Mdx + Ndy = 0？
│   ├── ∂M/∂y = ∂N/∂x → 正合，直接積分
│   └── 不相等 → 找積分因子 μ(x) 或 μ(y)
├── 標準線性形式 y' + p(x)y = r(x)？ → Integrating Factor
└── Bernoulli？y' + py = gy^n → 令 u = y^(1-n) 線性化

拿到二階 ODE
├── 缺 x 或已知 y₁？ → 降階法（令 u = y'）
├── 常係數齊次？y'' + ay' + by = 0 → 特徵方程
├── Euler-Cauchy？x²y'' + axy' + by = 0 → 令 y = xᵐ
├── 非齊次 y'' + ay' + by = r(x)？
│   ├── r(x) 是指數/多項式/三角？ → 未定係數法
│   └── r(x) 形式複雜？ → 參數變分法
└── 驗證解的獨立性 → Wronskian
```

---

## 重點整理

| 方法 | 適用形式 | 核心操作 | 注意事項 |
|---|---|---|---|
| 直接積分 | $y' = f(x)$ | $\int f(x)dx$ | 分部積分兩次技巧 |
| 分離變數 | $y' = g(x)h(y)$ | $\frac{dy}{h(y)} = g(x)dx$ | 分式拆項常用 |
| 齊次代換 | $y' = f(y/x)$ | $u = y/x$，$y' = u'x+u$ | 代回換成 $y$ |
| 正合 | $\partial_y M = \partial_x N$ | $F(x,y)=C$ | 積分取聯集 |
| 積分因子 | 不正合 | 找 $\mu(x)$ 使之正合 | 商需純 $x$ 才能用 $\mu(x)$ |
| 線性 ODE | $y' + py = r$ | $M(x) = e^{\int p\,dx}$ | $[My]' = Mr$ |
| Bernoulli | $y' + py = gy^n$ | $u = y^{1-n}$ | 乘以 $(1-n)y^{-n}$ |
| 正交軌跡 | 曲線族 | $\tilde{y}' = -1/y'$ | 斜率取負倒數 |
| 降階 | 缺 $x$ 或已知 $y_1$ | $u=y'$，$y'' = u\frac{du}{dy}$ | 結果含兩個任意常數 |
| 特徵方程 | 常係數齊次 | $\lambda^2+a\lambda+b=0$ | 三種根類型對應三種通解 |
| Euler-Cauchy | $x^2y''+axy'+by=0$ | $y=x^m$，Indicial Eq. | 重根加 $\ln x$ 項 |
| 未定係數 | 常係數非齊次 | 查表選 $y_p$ | 衝突時乘 $x$ 或 $x^2$ |
| 參數變分 | 任意非齊次 | $y_p = -y_1\int\frac{y_2 r}{W}+y_2\int\frac{y_1 r}{W}$ | $y''$ 係數必須先化為 1 |
