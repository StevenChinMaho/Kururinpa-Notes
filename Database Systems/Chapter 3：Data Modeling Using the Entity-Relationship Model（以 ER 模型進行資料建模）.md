# Chapter 3：Data Modeling Using the Entity-Relationship Model（以 ER 模型進行資料建模）

> Fundamentals of Database Systems — 課程投影片（CSIE NUK）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Database Design Process | 需求分析 → 概念設計 → 邏輯設計 → 實體設計 |
| COMPANY 範例 | 貫穿全章的範例資料庫 |
| Entities & Attributes | 實體與屬性；屬性的六種分類 |
| Entity Type / Key / Entity Set | 實體型別、鍵值屬性、實體集合 |
| Relationships | 關聯型別、degree、role、recursive relationship、關聯屬性 |
| Structural Constraints | Cardinality ratio 與 participation constraint |
| Weak Entity Types | 沒有 key 的實體型別與 identifying relationship |
| ER Diagram Notation | 標準符號與 (min, max) 替代記法 |
| ER Conceptual Design | 由初始設計逐步精煉（refinement）成 ER 圖 |
| Higher Degree Relationships | Ternary 與 binary 的差異與轉換 |

---

## 1. 資料庫設計流程（Database Design Process）

設計分為兩條主線：

| 主線 | 關注對象 | 本章範圍 |
|---|---|---|
| **Database design** | Conceptual schema（DBMS 與儲存層） | ✔ 本章重點 |
| **Applications design** | 存取資料庫的程式與介面 | 屬於 software engineering |

### 1.1 各階段

| 階段 | 工作內容 | 產出 |
|---|---|---|
| Requirements collection & analysis | 設計者訪談潛在使用者 | Data requirements、functional requirements |
| Functional analysis | 定義使用者操作（查詢、更新） | High-level transaction specification |
| **Conceptual design** | 以高階概念模型（如 ER model）精簡描述 entity types、relationships、constraints | Conceptual schema |
| Logical design（data model mapping） | 將概念模型轉換成商用 DBMS 的實作模型（如 relational model） | Logical schema |
| Physical design | 指定內部儲存結構、access path、file organization | Internal schema |

> Conceptual schema 也是與非技術使用者溝通的依據；若某些 functional requirement 無法在 schema 上表達，就回頭修改 conceptual schema。
>
> 分界：conceptual design 以上屬於 **DBMS-independent**；logical design 以下屬於 **DBMS-specific**。

### 1.2 流程圖（Database application system design）

```text
                   Miniworld
                       ↓
        Requirements Collection & Analysis
          ↙                              ↘
Functional Requirements            Database Requirements
        ↓                                 ↓
 Functional Analysis              Conceptual Design
        ↓                                 ↓
High-level Transaction Spec.      Conceptual Schema
        │                  ── DBMS-independent ──
        │                  ── DBMS-specific ──
        │                         Logical Design
        │                                 ↓
Application Program Design  ←     Logical Schema
        ↓                                 ↓
Transaction Implementation  ←     Physical Design → Internal Schema
        ↓
Application Programs
```

---

## 2. 範例：COMPANY 資料庫

需求描述：

- 公司分為多個 **department**，每個 department 有名稱、編號、一位 manager（含上任日期），並可能有多個地點。
- 每個 department 控管（controls）多個 **project**。
- 需記錄所有 **employee** 及其在各 project 的工時。
- 需記錄每位 employee 的 **dependent**（眷屬）。

完整 ER 圖見第 8 節。

---

## 3. 實體與屬性（Entities and Attributes）

**Entity**：mini-world 中具體的物件或事物，例如 EMPLOYEE John Smith。

**Attribute**：描述 entity 的性質，例如 EMPLOYEE 的 Name、SSN、Address、Sex、BirthDate。每個 entity 在每個 attribute 上各有一個值，例如 `Name = 'John Smith'`。

```text
e1 (EMPLOYEE)                        c1 (COMPANY)
  Name      = John Smith               Name         = Sunco Oil
  Address   = 2311 Kirby, Houston...   Headquarters = Houston
  Age       = 55                       President    = John Smith
  HomePhone = 713-749-2630
```

### 3.1 屬性的分類

| 分類 | 說明 | 範例 |
|---|---|---|
| **Simple**（atomic） | 單一不可再分的值 | SSN |
| **Composite** | 由多個子成分組成，可形成階層 | Address |
| **Single-valued** | 每個 entity 只有一個值（多數屬性） | BirthDate |
| **Multi-valued** | 每個 entity 可有多個值 | Expert、Color、Locations |
| **Stored** | 實際儲存的屬性 | BirthDate |
| **Derived** | 可由其他屬性推導出來 | Age（由 BirthDate 推得） |
| **Complex** | Composite 與 multi-valued 任意巢狀組合 | AddressPhone |

Composite attribute 的階層：

```text
Address
├── StreetAddress
│   ├── Number
│   ├── Street
│   └── ApartmentNumber
├── City
├── State
└── Zip
```

Complex attribute 記法：`{ }` 表示 multi-valued，`( )` 表示 composite。

```text
{AddressPhone( {Phone(AreaCode, PhoneNumber)},
               Address(StreetAddress(Number, Street, ApartmentNumber),
                       City, State, Zip) )}
```

---

## 4. Entity Type、Key Attribute 與 Entity Set

**Entity type**：具有相同基本屬性的 entity 歸成一類，例如 EMPLOYEE type。

**Key attribute**：每個 entity 在該屬性上的值都必須**唯一**，例如 EMPLOYEE 的 SSN。

- Key attribute 可以是 composite，例如 CAR 的 Registration(RegistrationNumber, State)。
- 一個 entity type 可以有多個 key attribute，例如 CAR 同時有 VehicleID 與 Registration。

**Entity set**（又稱 extension）：某個 entity type 在資料庫中目前所儲存的 entity 集合。習慣上 entity type 與 entity set 用同一個名稱（如 CAR）。

| | Entity Type | Entity Set |
|---|---|---|
| 性質 | 型別（描述、intension） | 集合（當下內容、extension） |
| 類比 | Schema | Database state |

```text
CAR
Registration(RegistrationNumber, State), VehicleID, Make, Model, Year, {Color}

car1: ((ABC 123, TEXAS),    TK629,  Ford Mustang,     convertible, 1998, {red, black})
car2: ((ABC 123, NEW YORK), WP9872, Nissan Maxima,    4-door,      1999, {blue})
car3: ((VSY 720, TEXAS),    TD729,  Chrysler LeBaron, 4-door,      1995, {white, blue})
```

> 注意 car1 與 car2 的 RegistrationNumber 相同（ABC 123），但 State 不同——composite key 是以**整體組合**判斷唯一性。

### 4.1 Entity type 的圖示規則

| 元素 | 畫法 |
|---|---|
| Entity type | 矩形 |
| Attribute | 橢圓，以線連到所屬 entity type |
| Composite attribute 的成分 | 連到代表該 composite attribute 的橢圓 |
| Key attribute | 名稱加底線 |
| Multi-valued attribute | 雙橢圓 |

CAR 範例：Registration（底線，下接 State、Number）、Vehicle_id（底線）、Year、Model、Make、Color（雙橢圓）。

---

## 5. 關聯（Relationships）

### 5.1 基本概念

**Relationship**：以特定意義連結兩個以上不同的 entity，例如 EMPLOYEE John Smith *works on* ProductX PROJECT。

**Relationship type**：同類 relationship 歸成一型，例如 WORKS_FOR。每個 relationship instance（r1, r2, …）連接參與各 entity type 中的各一個 entity。

**Degree**：參與的 entity type 數量。

| Degree | 名稱 | 範例 |
|---|---|---|
| 2 | Binary | WORKS_FOR（EMPLOYEE, DEPARTMENT） |
| 3 | Ternary | SUPPLY（SUPPLIER, PART, PROJECT） |

**Relationship 可以用 attribute 表示**：WORKS_FOR 可以看成 EMPLOYEE 的 Department 屬性，或 DEPARTMENT 的 Employees 屬性（multi-valued）。這正是第 8 節 refinement 的反向操作。

### 5.2 Recursive Relationship 與 Role

**Recursive relationship**：同一個 entity type 以不同 **role** 參與同一個 relationship。

例：SUPERVISION 連結一位 EMPLOYEE（role：**supervisee**）與另一位 EMPLOYEE（role：**supervisor**）。instance 圖中每條連線標 1（supervisor）或 2（supervisee）以區分角色。

### 5.3 Relationship 的屬性

Relationship type 也可以有屬性，例如 WORKS_ON 的 **Hours**（每週工時）——它既不屬於 employee 也不屬於 project，而是屬於「某員工在某專案」這個組合。

---

## 6. 結構限制（Structural Constraints）

### 6.1 Cardinality Ratio（binary relationship）

指定一個 entity 最多可以參與多少個 relationship instance。

| 比例 | 範例 | 意義 |
|---|---|---|
| 1:1 | MANAGES（EMPLOYEE : DEPARTMENT） | 一人最多管一個部門；一個部門只有一位 manager |
| N:1 / 1:N | WORKS_FOR（EMPLOYEE : DEPARTMENT） | 多名員工屬於同一部門；一人只屬一個部門 |
| M:N | WORKS_ON（EMPLOYEE : PROJECT） | 一人可參與多專案；一專案有多名員工 |

### 6.2 Participation Constraint（對每個參與的 entity type）

指定一個 entity 的存在是否依賴於它與另一個 entity 的關聯。

| 類型 | 意義 | 圖示 | 範例 |
|---|---|---|---|
| **Total**（existence dependency） | 每個 entity 都**必須**參與 | 雙線 | EMPLOYEE 在 WORKS_FOR（每位員工都屬於某部門） |
| **Partial** | 只有**部分** entity 參與 | 單線 | EMPLOYEE 在 MANAGES（不是每位員工都是 manager） |

> 同一個 relationship 的兩端可以不同：MANAGES 中 EMPLOYEE 是 partial，DEPARTMENT 是 total（每個部門都要有 manager）。
>
> Cardinality 管「**最多**幾個」，participation 管「**最少**有沒有」。

---

## 7. 弱實體型別（Weak Entity Types）

| | Regular（Strong）Entity Type | Weak Entity Type |
|---|---|---|
| Key attribute | 有 | 沒有 |
| 識別方式 | 自身 key | 透過 owner（identifying entity type）+ partial key |
| 參與限制 | 不限 | 對 identifying relationship **一定是 total** |
| 圖示 | 矩形 | 雙框矩形；identifying relationship 為雙框菱形 |

範例：DEPENDENT 以「眷屬的 first name + birthdate」加上「所屬的那位 EMPLOYEE」來識別。

- DEPENDENT 是 weak entity type
- EMPLOYEE 是它的 identifying（owner）entity type
- DEPENDENTS_OF 是 identifying relationship type
- Partial key（又稱 discriminator）：Name（圖中以虛底線表示）

> 直覺：兩位員工可能各有一個叫 Alice 的小孩，單靠 Name 無法區分，必須連同 owner 一起看。

---

## 8. ER 圖記號總整理（Notation Summary）

| 符號 | 意義 |
|---|---|
| 矩形 | Entity type |
| 雙框矩形 | Weak entity type |
| 菱形 | Relationship type |
| 雙框菱形 | Identifying relationship type |
| 橢圓 | Attribute |
| 橢圓 + 底線 | Key attribute |
| 雙橢圓 | Multi-valued attribute |
| 橢圓連出多個子橢圓 | Composite attribute |
| 虛線橢圓 | Derived attribute |
| 單線連接 E 與 R | Partial participation |
| 雙線連接 E 與 R | Total participation |
| 線上標 1、N、M | Cardinality ratio |
| 線上標 (min, max) | Structural constraint 替代記法 |

### 8.1 (min, max) 替代記法

在 entity type E 參與 relationship type R 的每條線上標 (min, max)：E 中的每個 entity 至少參與 min 個、至多參與 max 個 R 的 instance。

| 規則 | 內容 |
|---|---|
| 預設（無限制） | min = 0，max = n |
| 合法範圍 | 0 ≤ min ≤ max，max ≥ 1 |
| min = 0 | Partial participation |
| min > 0 | Total participation |

```text
EMPLOYEE —(0,1)— <MANAGES>   —(1,1)— DEPARTMENT
EMPLOYEE —(1,1)— <WORKS_FOR> —(1,N)— DEPARTMENT
```

> **讀法**：(min, max) 寫在 entity type 旁邊，意思是「**這個** entity 參與幾次」。
>
> - EMPLOYEE 旁的 (0,1)：每位員工管 0 或 1 個部門。
> - DEPARTMENT 旁的 (1,N)：每個部門有 1 到 N 位員工。
>
> 與 1:N 記法**方向相反**：1:N 記法中 WORKS_FOR 的 N 標在 EMPLOYEE 那側，(min,max) 記法中 N 則出現在 DEPARTMENT 那側。這是考試常見陷阱。

### 8.2 COMPANY 完整 ER 圖

```text
EMPLOYEE  ══N══ <WORKS_FOR>  ──1══ DEPARTMENT
EMPLOYEE  ──1── <MANAGES>    ──1══ DEPARTMENT     (Start_date)
DEPARTMENT──1── <CONTROLS>   ══N══ PROJECT
EMPLOYEE  ══M══ <WORKS_ON>   ══N══ PROJECT        (Hours)
EMPLOYEE  ──1── <SUPERVISION>──N── EMPLOYEE       (supervisor / supervisee)
EMPLOYEE  ──1── <<DEPENDENTS_OF>> ══N══ [[DEPENDENT]]

══ 表示 total participation，── 表示 partial
```

屬性：

| Entity | Attributes |
|---|---|
| EMPLOYEE | Name(Fname, Minit, Lname)、<u>Ssn</u>、Bdate、Address、Salary、Sex |
| DEPARTMENT | <u>Name</u>、<u>Number</u>、{Locations}、Number_of_employees（derived） |
| PROJECT | <u>Name</u>、<u>Number</u>、Location |
| DEPENDENT | Name（partial key）、Sex、Birth_date、Relationship |

---

## 9. ER 概念設計（ER Conceptual Design）

### 9.1 設計範式（Design Paradigm）

1. **Initial design**：找出所有 entity types 及其 attributes。
2. **Iterative refinement**：
   - 參考到其他 entity 的屬性 → 精煉成 **relationship**
   - 出現在多個 entity type 中的屬性 → 可獨立成新的 **entity type**（例：STUDENT、INSTRUCTOR、COURSE 都有 Department）
   - 也可以反向操作（把 entity type 降回屬性）

### 9.2 COMPANY 範例：Initial Design

```text
DEPARTMENT: Name, Number, {Locations}, Manager, ManagerStartDate
PROJECT:    Name, Number, Location, ControllingDepartment
EMPLOYEE:   Name(FName, MInit, LName), SSN, Sex, Address, Salary,
            BirthDate, Department, Supervisor, {WorksOn(Project, Hours)}
DEPENDENT:  Employee, DependentName, Sex, BirthDate, Relationship
```

### 9.3 Refinement：屬性 → Relationship

| 原屬性 | 精煉後的 relationship | Cardinality |
|---|---|---|
| DEPARTMENT.Manager | MANAGES | 1:1（EMPLOYEE : DEPARTMENT） |
| EMPLOYEE.Department | WORKS_FOR | 1:N（DEPARTMENT : EMPLOYEE） |
| PROJECT.ControllingDepartment | CONTROLS | 1:N（DEPARTMENT : PROJECT） |
| EMPLOYEE.Supervisor | SUPERVISION | 1:N（EMPLOYEE : EMPLOYEE） |
| EMPLOYEE.WorksOn | WORKS_ON | M:N（EMPLOYEE : PROJECT） |
| DEPENDENT.Employee | DEPENDENTS_OF | 1:N（EMPLOYEE : DEPENDENT） |

ManagerStartDate 變成 MANAGES 的屬性（Start_date）；WorksOn 裡的 Hours 變成 WORKS_ON 的屬性。

> 同一組 entity type 之間可以存在**多個** relationship type：EMPLOYEE 與 DEPARTMENT 之間同時有 MANAGES 與 WORKS_FOR。

---

## 10. 高階關聯（Higher Degree Relationships）

### 10.1 Ternary ≠ 三個 Binary

> **A ternary relationship type ≠ three binary relationship types**

以 SUPPLY(SUPPLIER, PART, PROJECT) 為例：

| 設計 | 表達的資訊 |
|---|---|
| (a) Ternary SUPPLY（s, j, p） | 供應商 s 供應零件 p 給專案 j；可附屬性 Quantity、supply date |
| (b) 三個 binary：SUPPLIES（s, j）、CAN_SUPPLY（s, p）、USES（j, p） | 只知道 s 供貨給 j、s 能供應 p、j 會用 p |

(s, p)、(j, p)、(s, j) 三組同時成立，**不能推出** (s, j, p) 成立——s 可能供應 p 給別的專案，而 j 的 p 來自別的供應商。一般而言，ternary 所攜帶的資訊比三個 binary 多（例如 Quantity 只能掛在三方組合上）。

### 10.2 只允許 binary 時的替代表示

若 data model 只允許 binary relationship，ternary 必須改成：

- 一個 **weak entity type**（如 SUPPLY），**沒有 partial key**
- 以**三個 identifying relationship**（SS、SP、SPJ）分別連到 SUPPLIER、PART、PROJECT
- 各 identifying relationship 為 1:N，weak entity 端為 total participation

```text
SUPPLIER —1— <<SS>> —N═ [[SUPPLY (Quantity)]] ═N— <<SPJ>> —1— PROJECT
                              ║N
                           <<SP>>
                              │1
                            PART
```

### 10.3 第二個範例：OFFERS(INSTRUCTOR, COURSE, SEMESTER)

| Relationship | 參與者 | 意義 |
|---|---|---|
| OFFERS（ternary） | i, c, s | 教師 i 在學期 s 開授課程 c |
| CAN_TEACH | i, c | 教師 i 有能力教課程 c |
| TAUGHT_DURING | i, s | 教師 i 在學期 s 有授課 |
| OFFERED_DURING | c, s | 課程 c 在學期 s 有開 |

```text
(i, c, s)  ⇒  (i, c), (i, s), (c, s)       ✔ 可推得
(i, c), (i, s), (c, s)  ⇏  (i, c, s)       ✘ 無法推得
```

> 何時用 binary、何時用 n-ary 常常不易判斷；關鍵在於資訊是否依附在「三方同時成立」的組合上。

### 10.4 Ternary 上的限制

Ternary（n-ary）relationship 同樣可以指定 cardinality ratio、participation constraint（partial / total），以及 (min, max) 記法。

---

## 重點整理

| 概念 | 核心意義 | 關鍵字 / 注意事項 |
|---|---|---|
| Conceptual design | 以 ER model 描述需求 | DBMS-independent；logical design 之後才 DBMS-specific |
| Composite vs. Simple | 可否再分解 | Address 可形成階層 |
| Multi-valued | 一個 entity 多個值 | 雙橢圓；記法 `{ }` |
| Derived | 可由其他屬性推得 | 虛線橢圓；Age ← BirthDate |
| Key attribute | 值唯一 | 底線；可 composite；可有多個 |
| Entity type vs. set | 型別 vs. 目前內容 | 同名；對應 schema vs. state |
| Degree | 參與的 entity type 數 | binary、ternary |
| Recursive relationship | 同 entity type 以不同 role 參與 | SUPERVISION：supervisor / supervisee |
| Cardinality ratio | 最多參與幾次 | 1:1、1:N、M:N |
| Participation | 是否必須參與 | Total（雙線、existence dependency）vs. partial |
| (min, max) | min = 0 partial；min > 0 total | 寫在 entity 旁、方向與 1:N 記法相反 |
| Weak entity type | 無 key，靠 owner 識別 | 雙框；identifying relationship 必為 total；有 partial key |
| Refinement | 參照型屬性 → relationship | Manager → MANAGES 等六個 |
| Ternary vs. binary | 三個 binary 推不出 ternary | 僅支援 binary 時改用無 partial key 的 weak entity + 3 個 identifying relationship |
