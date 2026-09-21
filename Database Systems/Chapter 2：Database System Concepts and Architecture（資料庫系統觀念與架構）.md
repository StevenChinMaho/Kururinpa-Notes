# Chapter 2：Database System Concepts and Architecture（資料庫系統觀念與架構）

> Fundamentals of Database Systems — 課程投影片（CSIE NUK）

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Data Models | 描述資料庫結構、限制與操作的一組概念 |
| 資料模型演進 | Relational、Network、Hierarchical、Object-oriented、Object-relational、NoSQL |
| Schema vs. Instance | 資料庫的「描述」與某一時刻的「實際內容」 |
| Three-Schema Architecture | External、Conceptual、Internal 三個層次與其間的 mapping |
| Data Independence | Logical 與 Physical 兩種資料獨立性 |
| DBMS Languages | DDL、SDL、VDL、DML（嵌入式 vs. 獨立式） |
| Database System Environment | DBMS 元件模組、utilities、tools、通訊設施 |
| DBMS Architectures | Centralized、Client-Server（two-tier、three-tier） |
| Classification of DBMSs | 依 data model、使用者數、站台數分類 |

---

## 1. 資料模型（Data Models）

### 1.1 定義

**Data model**：用來描述資料庫**結構（structure）**以及資料庫應遵守之**限制（constraints）**的一組概念。大多數 data model 也包含一組**基本操作（operations）**，用來指定查詢（retrieval）與更新（update）。

> Data model = 結構 + 限制 + 操作

### 1.2 各資料模型簡史

| Data Model | 起源 | 代表系統 | 結構特徵 |
|---|---|---|---|
| **Relational** | 1970 年 E.F. Codd（IBM）提出；1981–82 年首個商用系統 | ORACLE、SYBASE、SQL Server | 平面資料表（table），表與表之間以欄位值關聯 |
| **Network** | 最早被實作：Honeywell 1964–65（IDS System）；因 CODASYL-DBTG 報告（1971）而廣泛採用 | IDMS、DMS 1100、IMAGE、VAX-DBMS | 紀錄以連結（link）串成網狀，一筆紀錄可有多個上層 |
| **Hierarchical** | IBM 與 North American Rockwell 約 1965 年合作 | IMS 系列 | 樹狀結構，每筆紀錄只有一個上層（parent） |
| **Object-oriented** | 多種模型被提出 | 持久化 C++（OBJECTSTORE、VERSANT）、Smalltalk（GEMSTONE）、O2、ORION、IRIS | 以 object、tuple、set 組成的物件圖；標準為 ODMG-93、2.0、3.0 |
| **Object-relational** | 始於 Informix Universal Server | Oracle、DB2、SQL Server 新版 | 關聯式系統吸收物件資料庫概念；納入 SQL-99 標準 |
| **NoSQL** | 因應 big data 與 web 應用 | 見下表 | 非表格式（non-tabular） |

投影片以同一組 College → Department → Professor → Student 資料對照三種傳統模型：

- **Relational**：四張獨立的表，Department 以 `College` 欄、Student 以 `Prof ID` 欄指向上層，彼此平行存放。
- **Hierarchical**：College 為根，往下展開成一棵樹；一位學生只能掛在一位教授底下。
- **Network**：結構類似樹，但一筆 Student 紀錄可以同時被多位 Professor 連到（如 Munro、AlNajjar 都連向同一批學生），可表達多對多關係。

### 1.3 NoSQL（Not Only SQL）

| 類型 | 代表產品 |
|---|---|
| Key-value / Tuple stores | Redis |
| Column-oriented stores | Cassandra |
| Document-based stores | MongoDB |
| Graph databases | Neo4j |
| Vector databases（AI 專用） | Pinecone |

### 1.4 DBMS 排名（DB-Engines，2026 年 9 月）

投影片列出的前幾名：Oracle、MySQL、Microsoft SQL Server、PostgreSQL（皆為 Relational）、MongoDB（Document）、Snowflake、Databricks、Redis（Key-value）、IBM Db2、SQLite。

> 觀察：前四名全是關聯式資料庫，relational model 仍是主流；但幾乎所有產品都已標示為 multi-model。

---

## 2. Schema 與 Instance（Schemas vs. Instances）

| | Database Schema | Database Instance（State） |
|---|---|---|
| 內容 | 資料庫的**描述**：結構與應成立的限制 | 某個**時間點**上實際儲存的資料 |
| 別名 | —（圖示稱為 schema diagram） | database state、occurrence |
| 變動頻率 | 極少改變 | 每次更新（insert、delete）都會改變 |
| 類比 | 程式中的型別宣告 | 程式執行時變數的值 |

Schema diagram 範例（只有欄位名稱，沒有資料）：

```text
STUDENT       (Name, StudentNumber, Class, Major)
COURSE        (CourseName, CourseNumber, CreditHours, Department)
PREREQUISITE  (CourseNumber, PrerequisiteNumber)
SECTION       (SectionIdentifier, CourseNumber, Semester, Year, Instructor)
GRADE_REPORT  (StudentNumber, SectionIdentifier, Grade)
```

填入 Chapter 1 那組 Smith、Brown 的資料後，就是一個 database state。

---

## 3. 三層綱要架構（Three-Schema Architecture）

```text
         END USERS
     ↓               ↓
 EXTERNAL VIEW … EXTERNAL VIEW       ← External level
          \         /
   external/conceptual mapping
             ↓
     CONCEPTUAL SCHEMA               ← Conceptual level
             ↓
   conceptual/internal mapping
             ↓
      INTERNAL SCHEMA                ← Internal level
             ↓
      STORED DATABASE
```

| 層次 | 描述內容 | 使用的 data model |
|---|---|---|
| **External schemas** | 各類使用者的 view | 通常與 conceptual level 相同 |
| **Conceptual schema** | 整個資料庫的結構與限制（不涉及儲存細節） | Conceptual 或 implementation data model |
| **Internal schema** | 儲存結構與存取路徑（access path） | Physical data model |

**目的**：支援 program-data independence，以及支援多重使用者視角（multiple views）。

### 範例：選課系統（Course registration system）

| 層次 | 對應內容 |
|---|---|
| External | 學生看到的選課／加簽網頁畫面 |
| Conceptual | Student Registration ERD：STUDENT、REGISTRATION、REG_DETAIL、CLASS、COURSE、SECTION、FACULTY、ROOM 等 entity 與其 PK／FK |
| Internal | Oracle 的儲存結構（tablespace、datafile、redo log、backup set）；依 Name 排序並分 block 存放的檔案；以 hash 或 binary search 建立的 access index（bucket number → disk block address） |

---

## 4. 資料獨立性（Data Independence）

| 類型 | 定義 | 需修改的部分 | 投影片範例 |
|---|---|---|---|
| **Logical data independence** | 改變 conceptual schema，不必改 external schema 與應用程式 | 只改 view 定義與 external/conceptual mapping | 將 GRADE_REPORT 擴充加入 StudentName、CourseNumber 欄位，TRANSCRIPT view 不受影響 |
| **Physical data independence** | 改變 internal schema，不必改 conceptual schema | 只改 conceptual/internal mapping | 為 SECTION 新增依 Semester、Year（或 SectionID）排序的 index file，查詢「list all sections offered in fall 1998」不需改寫 |

> 關鍵：mapping 吸收了變動。某一層改變時，只要調整該層與上一層之間的 mapping，上一層就感受不到差異。
>
> Physical independence 較容易達成（只動存取路徑）；logical independence 較難（結構變動容易牽動應用程式）。

---

## 5. DBMS 語言（DBMS Languages）

### 5.1 DDL 與其變體

| 語言 | 用途 | 使用者 |
|---|---|---|
| **DDL**（Data Definition Language） | 定義 conceptual schema；許多 DBMS 也用它定義 internal、external schema | DBA、database designer |
| **SDL**（Storage Definition Language） | 定義 internal schema（部分 DBMS 獨立出來） | DBA |
| **VDL**（View Definition Language） | 定義 external schema／view（部分 DBMS 獨立出來） | DBA、designer |

投影片範例（醫院 schema，非 SQL 的舊式 DDL）：以 `MASTER ... KEYED(...)` 宣告紀錄與鍵值，以 `ITEM ... AS A15` 宣告欄位型別與長度，並可附加 `LIMITS`（值域）、`MASK`（格式）、`HEADING`（顯示標題）等限制。

```text
MASTER doctor KEYED(last_name);
    ITEM last_name AS A15;
    ITEM dept AS A12 LIMITS ('internal', 'oncology', 'ob/gyn', 'urology');
MASTER patient KEYED(ssn, A);
    ITEM ssn AS A11 MASK '999-99-9999';
    ITEM age AS 999 LIMIT (0:120);          -- 值域限制
    ITEM adm_date AS DATE 'mm/dd/yy';
```

### 5.2 DML（Data Manipulation Language）

用來指定資料的查詢與更新，有兩種使用方式：

| 方式 | 說明 |
|---|---|
| **Embedded DML**（data sublanguage） | 嵌入 COBOL、PL/1、C/C++ 等通用程式語言中 |
| **Stand-alone DML**（query language） | 直接獨立下達指令 |

Stand-alone SQL 範例：

```sql
SELECT BDATE, ADDRESS          -- 要取出的欄位
FROM   EMPLOYEE
WHERE  FNAME = 'John' AND MINIT = 'B' AND LNAME = 'Smith';
```

Embedded SQL 範例：以 `EXEC SQL ... END-EXEC` 包住 SQL，並用 cursor 逐筆取回結果；`:amount` 是宿主語言（host language）的變數。

```sql
EXEC SQL
  declare c cursor for
    select customer-name, customer-city
    from depositor, customer, account
    where depositor.customer-name = customer.customer-name
      and depositor.account-number = account.account-number
      and account.balance > :amount    -- :amount 來自宿主程式
END-EXEC
```

---

## 6. 資料庫系統環境（Database System Environment）

組成部分：DBMS component modules、database system utilities、tools（CASE tools、data dictionary）、application environment（例如 PowerBuilder）、communication facilities。

### DBMS 元件模組（Component Modules）

依使用者類型，請求經由不同路徑進入 DBMS：

| 使用者 | 輸入 | 處理路徑 |
|---|---|---|
| DBA staff | DDL statements | DDL Compiler → 寫入 System Catalog / Data Dictionary |
| DBA staff | Privileged commands | 直接交給 Run-time Database Processor |
| Casual users | Interactive query | Query Compiler → Run-time Database Processor |
| Application programmers | Application programs | Precompiler 分離出 DML statements → DML Compiler；其餘交 Host Language Compiler |
| Parametric users | Compiled (canned) transactions | 直接執行於 Run-time Database Processor |

底層：Run-time Database Processor 透過 **Stored Data Manager** 存取 Stored Database，並與 **Concurrency Control / Backup & Recovery Subsystems** 協作；各 compiler 與 processor 都會查詢 System Catalog。

---

## 7. DBMS 架構（DBMS Architectures）

### 7.1 Centralized DBMS

DBMS 軟體、硬體、應用程式、使用者介面處理全部集中在單一系統。使用者可透過遠端 terminal 連線，但所有處理都在中央站台完成。

### 7.2 Basic Client-Server

由各司其職的專門伺服器（print、file、DBMS、web、email server）組成，client 依需求透過網路存取。

### 7.3 Two-Tier vs. Three-Tier

| | Two-Tier | Three-Tier |
|---|---|---|
| Client 端 | 使用者介面 + 應用程式 | GUI / Web interface（presentation layer） |
| 中間層 | 無 | Application server / Web server（business logic layer） |
| Server 端 | DBMS（data source） | Database server（database services layer） |
| 特點 | Client 可連多個 DBMS（data sources），data source 也可以是檔案等非 DBMS 軟體；部分 object DBMS 把更多功能移到 client | 常見於 web 應用；中間層存放連線軟體與商業邏輯，在 DB server 與 client 間傳遞部分處理過的資料 |
| 安全性 | Client 直接連 DB | Client 無法直接存取 DB，只能透過中間層 → 較安全 |

---

## 8. DBMS 的分類（Classification of DBMSs）

| 分類依據 | 類別 |
|---|---|
| Data model | Traditional：Relational、Network、Hierarchical；Emerging：Object-oriented、Object-relational、NoSQL |
| 使用者數量 | Single-user（通常用於個人電腦）；Multi-user（多數 DBMS） |
| 站台數量 | Centralized（單一電腦、單一資料庫）；Distributed（多台電腦、多個資料庫），再分為 Homogeneous 與 Heterogeneous（Federated DBMS） |

---

## 重點整理

| 概念 | 核心意義 | 關鍵字 / 注意事項 |
|---|---|---|
| Data model | 結構 + 限制 + 操作 | 最早實作：Network；Relational 由 Codd 於 1970 提出 |
| Hierarchical vs. Network | 樹狀 vs. 網狀 | Hierarchical 只有一個 parent；Network 可多對多 |
| NoSQL | Not Only SQL | key-value、column、document、graph、vector |
| Schema | 資料庫的描述 | 很少改變；可畫成 schema diagram |
| Instance / State | 某時刻的實際資料 | 每次 insert/delete 都改變 |
| Three-schema architecture | External / Conceptual / Internal | 層與層之間靠 mapping 連接 |
| Logical data independence | 改 conceptual 不影響 external | 較難達成 |
| Physical data independence | 改 internal 不影響 conceptual | 例：新增 index |
| DDL / SDL / VDL | 定義 conceptual / internal / external schema | 多數 DBMS 以 DDL 統包 |
| DML | 查詢與更新 | Embedded（宿主語言中）vs. stand-alone（query language） |
| Three-tier | Client ↔ App server ↔ DB server | Client 不能直連 DB，安全性較高 |
| DBMS 分類 | 依 model、使用者數、站台數 | Distributed 分 homogeneous / heterogeneous（federated） |
