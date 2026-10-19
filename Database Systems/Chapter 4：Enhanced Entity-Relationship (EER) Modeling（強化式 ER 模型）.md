# Chapter 4：Enhanced Entity-Relationship (EER) Modeling（強化式 ER 模型）

> Fundamentals of Database Systems — 課程投影片（CSIE NUK）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| EER Model | 在基本 ER 之上加入類別階層概念的延伸模型 |
| Subclass / Superclass | 子類別與超類別，及其 IS-A 關係 |
| Type Inheritance | 子類別繼承超類別的屬性與關聯 |
| Specialization | 由超類別往下定義子類別（top-down） |
| Generalization | 由多個 entity type 往上抽出超類別（bottom-up） |
| Constraints | Predicate-defined、attribute-defined、disjointness、completeness |
| Hierarchy vs. Lattice | 單一繼承 vs. 多重繼承（shared subclass） |
| UNION Types（Category） | 多個超類別的聯集子類別 |
| Category vs. Shared Subclass | 聯集 vs. 交集 |
| UNIVERSITY EER 範例 | 完整的 EER schema 設計流程 |

---

## 1. EER 模型概念（EER Model Concepts）

**EER**＝Enhanced ER（或 Extended ER）。包含基本 ER 的所有建模概念，另外加上：

- subclass / superclass
- specialization / generalization
- category（UNION type）
- attribute 與 relationship inheritance

### 1.1 Subclass 與 Superclass

許多 entity type 底下還可再分成若干子群組。例如 EMPLOYEE 可分成 SECRETARY、ENGINEER、MANAGER、TECHNICIAN。

```text
                EMPLOYEE          ← superclass（超類別）
                   │
        ┌──────┬───┴───┬─────────┐
   SECRETARY ENGINEER MANAGER TECHNICIAN   ← subclasses（子類別）
```

### 1.2 Class/Subclass（IS-A / IS-AN）關係

superclass 與其任一 subclass 之間的關係，例如 EMPLOYEE/MANAGER。

| 性質 | 說明 |
|---|---|
| 成員關係 | 身為 subclass 成員的 entity，**必定**也是 superclass 的成員 |
| 同一實體 | subclass 的 entity 與 superclass 中的那個 entity 是**同一個**，只是扮演特定角色 |
| 範例 | SECRETARY 'Joan Logano' 同時就是 EMPLOYEE 'Joan Logano' |

> 投影片的 instance 圖顯示：EMPLOYEE 集合中的 e1…e5 分別被連到 SECRETARY、ENGINEER、TECHNICIAN 三個子集合中的對應成員；子類別中的每個點在超類別中都找得到來源，反之不然。

### 1.3 型別繼承（Type Inheritance）

subclass 的 entity 繼承：

1. 作為 superclass 成員時的**所有屬性**
2. superclass 參與的**所有關聯**

同時 subclass 可以有自己**特有的屬性**與**特有的關聯**。例如 SECRETARY 有 TypingSpeed、TECHNICIAN 有 TGrade、ENGINEER 有 EngType。

---

## 2. Specialization 與 Generalization

### 2.1 Specialization（特殊化）

依某個**區分特徵（distinguishing characteristic）**定義出一組子類別的過程。同一個 superclass 可以有**多組不同的 specialization**：

| Specialization | 依據 | 子類別 |
|---|---|---|
| 依 job type | JobType | {SECRETARY, ENGINEER, TECHNICIAN} |
| 依 method of pay | 付薪方式 | {SALARIED_EMPLOYEE, HOURLY_EMPLOYEE} |
| 單一子類別 | — | {MANAGER} |

EMPLOYEE 的三組 specialization 同時存在於同一張 EER 圖中：

```text
                    EMPLOYEE
   (Name(FName,MInit,LName), Ssn, BirthDate, Address)
      │                │                    │
     (d)              ∪                    (d)
   ┌──┼──┐             │              ┌─────┴─────┐
SECRETARY TECHNICIAN ENGINEER     SALARIED_   HOURLY_
(TypingSpeed)(TGrade)(EngType)    EMPLOYEE    EMPLOYEE
                     MANAGER      (Salary)    (PayScale)
                        │                         │
                   <MANAGES>               <BELONGS_TO>
                        │                         │
                     PROJECT                TRADE_UNION
```

### 2.2 使用 subclass 的理由

| 理由 | 說明 |
|---|---|
| **Specific attributes** | 某些屬性只適用於 superclass 的部分 entity（TypingSpeed 只對祕書有意義） |
| **Specific relationships** | 某些關聯只有特定子類別的成員才參與（只有 MANAGER 參與 MANAGES） |

### 2.3 Generalization（一般化）

找出數個 entity type 的**共同特徵**，抽出成單一 superclass 的過程。可視為 specialization 的**反向操作**。

範例：CAR 與 TRUCK 一般化為 VEHICLE。

```text
(a) 一般化前：
CAR (VehicleId, LicensePlateNo, Price, MaxSpeed, NoOfPassengers)
TRUCK(VehicleId, LicensePlateNo, Price, NoOfAxles, Tonnage)

(b) 一般化後：
            VEHICLE (VehicleId, LicensePlateNo, Price)   ← 共同屬性上移
                 │
                (d)
            ┌────┴────┐
          CAR        TRUCK
   (MaxSpeed,      (NoOfAxles,
    NoOfPassengers)  Tonnage)                            ← 特有屬性留在子類別
```

> Specialization 與 generalization 的**結果圖形完全相同**，差別只在設計時的**思考方向**（由上而下 vs. 由下而上）。

---

## 3. Specialization / Generalization 的限制

### 3.1 Predicate-defined（Condition-defined）Subclass

在識別子類別所用的屬性值上加一個條件，成員必須滿足該 predicate 才屬於該子類別。

例：SECRETARY 的 defining predicate 為 `JobType = 'Secretary'`。圖中把條件標在連往子類別的那條線上。

### 3.2 Attribute-defined Specialization

若一組 subclass 的成員條件都建立在**同一個屬性**上，則該屬性稱為此 specialization 的 **defining attribute**，標在 superclass 連到圓圈的那條線旁。

```text
       EMPLOYEE (…, JobType)
            │
        Job Type                 ← defining attribute
            │
           (d)
   "Secretary" │ "Technician" │ "Engineer"      ← defining predicates
   SECRETARY   TECHNICIAN      ENGINEER
```

若子類別的成員由使用者逐一指定（而非由條件決定），則稱為 **user-defined** subclass。

### 3.3 Disjointness Constraint（互斥限制）

| 符號 | 名稱 | 意義 | 範例 |
|---|---|---|---|
| **d** | Disjoint | 一個 entity 最多屬於**一個**子類別 | {HOURLY_EMPLOYEE, SALARIED_EMPLOYEE} |
| **o** | Overlapping | 子類別的成員集合**可以重疊** | PART → {MANUFACTURED_PART, PURCHASED_PART}（某零件可能既自製又外購） |

### 3.4 Completeness Constraint（完整性限制）

| 類型 | 圖示 | 意義 |
|---|---|---|
| **Total specialization** | superclass 到圓圈用**雙線** | superclass 的每個 entity 都**必須**屬於某個子類別 |
| **Partial specialization** | 單線 | 允許 entity **不屬於任何**子類別 |

兩組限制互相獨立，可組合出四種情形：

| 組合 | 意義 |
|---|---|
| Disjoint, Total | 每個 entity 恰好屬於一個子類別（完整分割） |
| Disjoint, Partial | 最多屬於一個，也可以都不屬於 |
| Overlapping, Total | 至少屬於一個，可以同時屬於多個 |
| Overlapping, Partial | 最寬鬆，無任何強制 |

> PART 的例子是 **overlapping + total**（雙線 + o）：每個零件至少是自製或外購其中之一，但也可能兩者皆是。

---

## 4. Specialization Hierarchy 與 Lattice

子類別本身還可以再往下特殊化，形成階層或網格：

| | Specialization Hierarchy | Specialization Lattice |
|---|---|---|
| 結構 | 樹狀 | 有向無環圖 |
| 限制 | 每個 subclass 只參與**一個** class/subclass 關係（只有一個 superclass） | 一個 subclass 可參與**多個** class/subclass 關係（多個 superclass） |
| 繼承 | 單一繼承 | **多重繼承（multiple inheritance）** |

**Shared subclass**：擁有一個以上 superclass 的子類別，會造成多重繼承。

例：ENGINEERING_MANAGER 同時是 ENGINEER、MANAGER 與 SALARIED_EMPLOYEE 的子類別。

```text
                    EMPLOYEE
     ┌──────(d)──────┬────┬──────(d)──────┐
SECRETARY TECHNICIAN ENGINEER MANAGER  SALARIED_  HOURLY_
                        │       │      EMPLOYEE  EMPLOYEE
                        └───────┼─────────┘
                                │
                     ENGINEERING_MANAGER        ← shared subclass
```

> 規則：shared subclass 中**重複繼承到的屬性只保留一份**（例如從三條路徑都繼承到 Ssn，只算一次）。

---

## 5. 在概念建模中運用 Specialization / Generalization

| 方法 | 流程 | 範例 |
|---|---|---|
| **Top-down conceptual refinement** | 從一個 entity type 出發，逐次特殊化定義子類別 | PERSON → {EMPLOYEE, ALUMNUS, STUDENT} → {STAFF, FACULTY, STUDENT_ASSISTANT} → {GRADUATE_STUDENT, UNDERGRADUATE_STUDENT} |
| **Bottom-up conceptual synthesis** | 從多個 entity type 出發，逐次一般化定義超類別 | {STAFF, FACULTY, ALUMNUS} → EMPLOYEE |
| **Hybrid** | 兩者混合使用 | 實務上最常見 |

投影片的 university lattice 範例：PERSON 以 **o（overlapping）** 特殊化為 EMPLOYEE、ALUMNUS、STUDENT（一個人可以同時是校友與職員）；EMPLOYEE 再以 d 分為 STAFF、FACULTY、STUDENT_ASSISTANT；STUDENT 以 d 分為 GRADUATE_STUDENT、UNDERGRADUATE_STUDENT；STUDENT_ASSISTANT 同時是 EMPLOYEE 與 STUDENT 的 shared subclass，再往下分為 RESEARCH_ASSISTANT 與 TEACHING_ASSISTANT。

---

## 6. UNION 型別與 Category

### 6.1 定義

**Category（UNION type）**：用來建模一個子類別具有**多個超類別**的情形，該子類別是這些超類別**聯集的子集**。以 **∪** 符號表示（set union operation）。

例：車輛的擁有者可能是 person、bank 或 company。

```text
   PERSON        BANK        COMPANY
      │            │            │
      └────────────∪────────────┘
                   │
                 OWNER           ← category
                   │ M
               <OWNS>  (LienOrRegular, PurchaseDate)
                   │ N
           REGISTERED_VEHICLE
```

### 6.2 Category 的性質

| 性質 | 說明 |
|---|---|
| Total / Partial | category 可以是 total（超類別的所有 entity 都屬於它）或 partial |
| Defining predicate | 可在每條連線上標 c₁、c₂ 指定哪些 entity 成為成員（如 ACCOUNT_HOLDER 範例） |
| 超類別的 key | 可以**不同**（OWNER：PERSON 用 SSN、COMPANY 用 CName、BANK 用 BName）；也可以**相同**（REGISTERED_VEHICLE：CAR 與 TRUCK 都用 VehicleId） |
| 屬性繼承 | **選擇性繼承（selective inheritance）**——entity 只繼承它實際所屬的那個超類別的屬性 |

### 6.3 Category vs. Shared Subclass

這是本章最容易混淆的一組概念：

| | **Category（UNION type）** | **Shared Subclass** |
|---|---|---|
| 集合運算 | 超類別的**聯集（∪）**之子集 | 超類別的**交集（∩）**之子集 |
| 成員條件 | 只需存在於**其中一個**超類別 | 必須**同時**存在於**所有**超類別 |
| 屬性繼承 | 選擇性繼承（只繼承所屬那一個） | 完全繼承（繼承全部，重複者只取一份） |
| 符號 | ∪ | 多條線直接連到各 superclass |
| 範例 | OWNER 可以是 COMPANY **或** BANK **或** PERSON | ENGINEERING_MANAGER 必須是 ENGINEER **且** MANAGER **且** SALARIED_EMPLOYEE |

### 6.4 Category vs. 一般化超類別

| 情形 | 可否互換 |
|---|---|
| **Total** category | ✔ 可以表示成 specialization／generalization（例：PROPERTY 的 BUILDING／LOT 範例，(a) 用 total category、(b) 用 generalization 兩者等價） |
| **Partial** category | ✘ **不能**表示成 partial specialization／generalization（例：REGISTERED_VEHICLE category 與 VEHICLE 一般化超類別不等價） |

> 原因：generalization 中每個子類別的 entity **一定**是超類別的成員；partial category 則允許超類別中有些 entity 不屬於該 category，而且 category 的超類別彼此是獨立的 entity type。

---

## 7. EER Schema 完整範例：UNIVERSITY 資料庫

### 7.1 需求

資料庫需記錄：

- 學生及其主修、歷年成績（transcript）、選課註冊
- 學校的課程開設
- 教師與研究生主持／參與的研究計畫（sponsored research projects）

### 7.2 Initial Design

| 類型 | 內容 |
|---|---|
| Entity types | STUDENT、COURSE、FACULTY、GRADUATE_STUDENT、GRANT（project）、DEPARTMENT（主修）、SECTION（開課與註冊）、COLLEGE |
| Superclass/subclass | STUDENT／GRAD_STUDENT；SECTION／CURRENT_SECTION（適用學季制大學） |
| Generalization | PERSON：{FACULTY, STUDENT} |
| Category（UNION） | INSTRUCTOR_RESEARCHER：{FACULTY, GRAD_STUDENT} |

> INSTRUCTOR_RESEARCHER 是 category 的典型用例：授課／主持研究的人**可能是**教師，**也可能是**研究生，兩者是不同的 entity type。

### 7.3 Iterative Refinement：關聯型別

| 關聯 | 參與者 |
|---|---|
| REGISTERED | STUDENT — CURRENT_SECTION |
| MAJOR / MINOR | STUDENT — DEPARTMENT |
| TRANSCRIPT | STUDENT — SECTION（屬性 Grade） |
| BELONGS | FACULTY — DEPARTMENT |
| ADVISOR | FACULTY — GRAD_STUDENT |
| COMMITTEE | FACULTY — GRAD_STUDENT |
| PI（principal investigator） | FACULTY — GRANT |
| TEACH | INSTRUCTOR_RESEARCHER — SECTION |
| SUPPORT | GRANT — INSTRUCTOR_RESEARCHER（屬性 Start、Time、End） |
| CS | COURSE — SECTION |
| DC | DEPARTMENT — COURSE |
| CD | COLLEGE — DEPARTMENT |
| CHAIRS | FACULTY — DEPARTMENT |

最後再補上各關聯的 structural constraints（cardinality ratio 與 participation）。

> 注意同一組 entity type 之間可以有多個關聯：FACULTY 與 GRAD_STUDENT 之間同時有 ADVISOR（1:N）與 COMMITTEE（M:N）。

---

## 重點整理

| 概念 | 核心意義 | 符號 / 注意事項 |
|---|---|---|
| Subclass / Superclass | 子類別成員必定是超類別成員 | IS-A 關係；同一實體的不同角色 |
| Type inheritance | 繼承超類別的屬性**與關聯** | 子類別可有自己特有的屬性與關聯 |
| Specialization | 由上而下定義子類別 | 依 distinguishing characteristic；一個超類別可有多組 |
| Generalization | 由下而上抽出超類別 | specialization 的反向；結果圖形相同 |
| Predicate-defined | 以屬性值條件決定成員 | 條件標在線上 |
| Attribute-defined | 同一組子類別都用同一屬性判定 | 該屬性＝defining attribute |
| Disjoint (d) | 最多屬於一個子類別 | vs. overlapping (o) 可重疊 |
| Total / Partial | 是否每個 entity 都要屬於某子類別 | 雙線＝total |
| Hierarchy vs. Lattice | 單一 superclass vs. 多個 superclass | lattice → 多重繼承 |
| Shared subclass | 交集（∩）：必須同時屬於所有超類別 | ENGINEERING_MANAGER；重複屬性只取一份 |
| Category（∪） | 聯集：只需屬於其中一個超類別 | OWNER；**選擇性**屬性繼承 |
| Total vs. partial category | total 可改寫成 generalization | partial category **不能**改寫 |
