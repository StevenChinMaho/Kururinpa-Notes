# Chapter 1：Introduction to Databases（資料庫導論）

> Fundamentals of Database Systems — 課程投影片（CSIE NUK）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Basic Definitions | Data、Database、DBMS、Database System、Mini-world 的定義與關係 |
| Example of a Database | UNIVERSITY 環境中的 entity 與 relationship，以及對應的資料表 |
| Database vs. File-processing | 資料庫方法的四大核心特性 |
| Self-describing Nature | DBMS catalog 儲存 meta-data，使 DBMS 可操作任意資料庫 |
| Program-data Independence | 改變儲存結構不需改寫存取程式 |
| Data Abstraction & Views | 以 data model 隱藏儲存細節，並支援多重使用者視角 |
| Multiuser Transaction Processing | 並行存取與 concurrency control（OLTP） |
| Additional Advantages | 控制冗餘、完整性限制、授權控制、備份復原等 |
| Actors on the Scene | DBA、designer、end user、system analyst、application programmer |

---

## 1. 基本定義（Basic Definitions）

**Data**：可被記錄下來、且具有隱含意義（implicit meaning）的已知事實。

**Database**：一群互有關聯的資料（a collection of *related* data）。關鍵字是「related」——隨機湊在一起的資料不算 database。

**Mini-world（Universe of Discourse, UoD）**：真實世界中被資料庫所描述的那一部分。例如大學環境中的學生成績與歷年成績單。資料庫是 mini-world 的模型，mini-world 改變時資料庫也要跟著改變。

**DBMS（Database Management System）**：用來協助建立與維護電腦化資料庫的軟體套件／系統。

**Database System**：DBMS 軟體 + 資料庫本身；廣義上有時也把應用程式一併算入。

| 名詞 | 內容 | 一句話定義 |
|---|---|---|
| Data | 事實 | 可記錄且有意義的已知事實 |
| Database | 資料集合 | 互有關聯的資料集合 |
| DBMS | 軟體 | 建立與維護資料庫的通用軟體系統 |
| Database System | 軟體 + 資料 | DBMS ＋ 資料庫（＋應用程式） |
| Mini-world | 真實世界片段 | 資料庫所要描述的那部分現實 |

### Database System 的環境架構

投影片的架構圖由上而下分為四層：

```text
Users / Programmers
        ↓
Application Programs / Queries        ┐
        ↓                             │ DATABASE SYSTEM
┌── DBMS SOFTWARE ─────────────────┐  │
│  Software to Process             │  │
│      Queries / Programs          │  │
│            ↓                     │  │
│  Software to Access Stored Data  │  │
└──────────────────────────────────┘  │
        ↓                ↓            │
 Stored Database    Stored            │
   Definition       Database          │
   (Meta-Data)                        ┘
```

> 重點：DBMS 存取的是**兩份**東西——meta-data（資料庫的定義）與 stored database（實際資料）。這正是 self-describing 特性的來源。

---

## 2. 資料庫範例（Example of a Database）

Mini-world：大學環境的一部分。

**Entities（實體）**：STUDENT、COURSE、SECTION（COURSE 的開課班次）、DEPARTMENT、INSTRUCTOR。

**Relationships（關聯）**：

| 關聯 | 說明 |
|---|---|
| SECTION *of* COURSE | 每個 section 屬於某一門課 |
| STUDENT *take* SECTION | 學生修某個 section |
| COURSE *have prerequisite* COURSE | 課程之間的先修關係（自我關聯） |
| INSTRUCTOR *teach* SECTION | 教師授課 |
| COURSE *offered by* DEPARTMENT | 系所開課 |
| STUDENT *major in* DEPARTMENT | 學生主修系所 |

對應的資料表（後續各章節都會重複使用這組範例）：

```text
STUDENT(Name, StudentNumber, Class, Major)
    Smith, 17, 1, CS      Brown, 8, 2, CS

COURSE(CourseName, CourseNumber, CreditHours, Department)
    Intro to Computer Science, CS1310, 4, CS
    Data Structures,           CS3320, 4, CS
    Discrete Mathematics,      MATH2410, 3, MATH
    Database,                  CS3380, 3, CS

SECTION(SectionIdentifier, CourseNumber, Semester, Year, Instructor)
    85,  MATH2410, Fall,   98, King
    92,  CS1310,   Fall,   98, Anderson
    102, CS3320,   Spring, 99, Knuth
    112, MATH2410, Fall,   99, Chang
    119, CS1310,   Fall,   99, Anderson
    135, CS3380,   Fall,   99, Stone

GRADE_REPORT(StudentNumber, SectionIdentifier, Grade)
    17-112-B, 17-119-C, 8-85-A, 8-92-A, 8-102-B, 8-135-A

PREREQUISITE(CourseNumber, PrerequisiteNumber)
    CS3380 ← CS3320,  CS3380 ← MATH2410,  CS3320 ← CS1310
```

---

## 3. 資料庫方法的特性（Characteristics of the Database Approach）

以下四點是 database approach 與傳統 file-processing 最根本的差異。

### 3.1 自我描述性（Self-describing Nature）

DBMS 的 **catalog** 儲存整個資料庫的描述，這些描述稱為 **meta-data**：資料項名稱、型別、長度、儲存格式、限制等。

因為描述與資料分離且由 DBMS 自行讀取，同一套 DBMS 軟體可以operate 任何不同的資料庫。傳統 file-processing 中，檔案結構是寫死在應用程式碼裡的，程式與檔案綁死。

範例 catalog（STUDENT record）：

| Data Item Name | Starting Position in Record | Length in Characters (bytes) |
|---|---|---|
| Name | 1 | 30 |
| StudentNumber | 31 | 4 |
| Class | 35 | 4 |
| Major | 39 | 4 |

### 3.2 程式與資料獨立（Program-data Independence）

允許改變資料的儲存結構與存取操作，而不需修改存取程式。

> 情境：要在 STUDENT 中新增 Birthday 欄位。
>
> - **File-processing**：record 格式改變 → 所有讀寫該檔案的程式都要重寫、重新編譯。
> - **Database system**：只需修改 catalog 中的 STUDENT 描述，DBMS 程式與應用程式不需更動。

### 3.3 資料抽象化與多重視角（Data Abstraction & Multiple Views）

**Data abstraction**：以 **data model** 隱藏儲存細節（檔案如何配置、索引如何建立），對使用者只呈現概念層次（conceptual view）的資料庫。program-data independence 正是抽象化帶來的結果。

**Multiple views**：每個使用者可看到不同的 view，只包含他關心的資料，甚至包含原資料表中不存在的衍生資料。

| View 類型 | 說明 |
|---|---|
| Materialized（實體化） | view 的結果實際被計算並儲存下來 |
| Virtual（虛擬） | 只儲存 view 的定義，查詢時才即時導出 |

投影片範例：從同一組基底資料表導出兩個不同的 view。

```text
TRANSCRIPT view（學生成績單）
  StudentName | CourseNumber | Grade | Semester | Year | SectionId
  Smith       | CS1310       | C     | Fall     | 99   | 119
  Smith       | MATH2410     | B     | Fall     | 99   | 112
  Brown       | MATH2410     | A     | Fall     | 98   | 85
  ...

PREREQUISITES view（課程先修）
  CourseName      | CourseNumber | Prerequisites
  Database        | CS3380       | CS3320, MATH2410
  Data Structures | CS3320       | CS1310
```

### 3.4 資料共享與多使用者交易處理（Sharing & Multiuser Transaction Processing）

允許多位使用者同時存取資料庫，因此 DBMS 必須包含 **concurrency control** 軟體，確保並行更新結果仍然正確（此類應用稱為 **OLTP**，On-Line Transaction Processing）。

典型例子：航空訂位系統——多人同時搶訂同一個座位，必須保證不會超賣。

---

## 4. 其他優點（Additional Advantages）

### 4.1 控制冗餘（Controlling Redundancy）

在 file-processing 中，每個 user group 各自維護自己的檔案（例如課務組與會計室各存一份學生資料），造成兩個問題：

| 問題 | 說明 |
|---|---|
| Inconsistency（不一致） | 同一筆事實在不同檔案被更新成不同值 |
| Wasted storage（空間浪費） | 同樣的資料被重複儲存多份 |

在 database approach 中，各 user group 的資料被整合（integrated），原則上不保留冗餘。但某些情況下冗餘是有用的（例如為了查詢效能而刻意重複欄位），此時必須做 **redundancy control** 以避免不一致。

```text
Controlled redundancy – Consistent（同一 StudentNumber 對應同一姓名）
  17 Smith 112 MATH2410 B
  17 Smith 119 CS1310   C
   8 Brown  85 MATH2410 A

Uncontrolled redundancy – Inconsistent（17 卻寫成 Brown → 矛盾）
  17 Brown 112 MATH2410 B
```

### 4.2 強制完整性限制（Enforcing Integrity Constraints）

| 限制類型 | 意義 | 範例 |
|---|---|---|
| Data type constraint | 欄位值必須符合型別／值域 | Grade 只能是 A、B、C、D、E |
| Relationship constraint | 關聯必須成立（referential integrity） | 每筆 SECTION 必須對應到一筆存在的 COURSE |
| Uniqueness constraint | 欄位值不得重複 | CourseNumber 必須唯一 |

> 注意：即使所有 integrity constraints 都成立，資料仍可能是錯的。例如把學生實際的 A 誤key 成 C——格式合法，但語意錯誤。DBMS 只能擋住「不合規則」的資料，擋不住「不符事實」的資料。

### 4.3 其他系統層級優點

| 優點 | 說明 |
|---|---|
| Restricting unauthorized access | 以帳號／權限控制各使用者可存取的資料與操作 |
| Efficient query processing | 提供索引、緩衝與查詢最佳化機制 |
| Sharing of data among multiple users | 多人共用同一份整合資料 |
| Multiple user interfaces | 對不同族群提供查詢語言、程式介面、表單、選單等 |
| Representing complex relationships | 表達 entity 之間複雜的關聯結構 |
| Backup and recovery | 故障後可還原至一致狀態 |
| Enforcing standards | 統一命名、格式、文件等標準 |
| Inferencing and actions | 以規則（rule／trigger）自動推導與觸發動作，例如判定重修者（repeaters） |

### 4.4 對開發流程的影響

- **Reduced application development time**：底層存取功能由 DBMS 提供，應用只寫商業邏輯。
- **Flexibility to change data structures**：需求改變時可調整結構而不重寫整個系統。
- **Availability of up-to-date information**：更新立即對所有使用者可見。
- **Economies of scale**：集中管理資料與硬體資源，降低整體成本。

---

## 5. 資料庫系統中的角色（Actors on the Scene）

| 角色 | 職責 |
|---|---|
| Database Administrator (DBA) | 管理資料庫與 DBMS：授權、監控效能、取得資源 |
| Database Designer | 辨識資料、設計 schema，與各 user group 溝通需求 |
| End User | 實際使用資料庫查詢、更新、產生報表 |
| System Analyst | 分析使用者需求，定義系統規格 |
| Application Programmer | 依規格實作應用程式 |
| Software Engineer | 設計與實作資料庫應用系統 |

---

## 重點整理

| 概念 | 核心意義 | 關鍵字 / 注意事項 |
|---|---|---|
| Database | 互有關聯的資料集合 | 必須是 *related*，且對應某個 mini-world |
| DBMS | 建立與維護資料庫的通用軟體 | 一套 DBMS 可服務多個不同資料庫 |
| Database System | DBMS + database (+ applications) | 架構圖分為應用層、DBMS 層、儲存層 |
| Self-describing | catalog 儲存 meta-data | 資料的「描述」與「資料本身」一起存放 |
| Program-data independence | 改儲存結構不必改程式 | 加欄位只需改 catalog；file-processing 則要重寫程式 |
| Data abstraction | 以 data model 隱藏儲存細節 | 使用者看到 conceptual view |
| Multiple views | 各使用者看到不同資料子集 | materialized（已儲存）vs. virtual（僅定義） |
| Multiuser processing | 多人同時存取 | 需 concurrency control；OLTP，如訂位系統 |
| Controlling redundancy | 整合各 user group 的資料 | 冗餘造成 inconsistency 與空間浪費；必要的冗餘要做控制 |
| Integrity constraints | 限制合法資料 | type / relationship / uniqueness；合法 ≠ 正確 |
| Actors | DBA、designer、end user、analyst、programmer | DBA 管系統、designer 設計 schema |
