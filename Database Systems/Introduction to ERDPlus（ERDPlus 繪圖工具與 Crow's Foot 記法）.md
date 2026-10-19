# Introduction to ERDPlus（ERDPlus 繪圖工具與 Crow's Foot 記法）

> Wen-Yang Lin, Dept of CSIE, National Univ. of Kaohsiung — Oct. 2025
> 對應 Mannino《Database Design》Ch. 5：Understanding Entity Relationship Models

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| Basic Symbols | Entity、attribute、relationship 的符號（與課本相同） |
| Cardinality Notation | Crow's Foot 的三個符號與 inside/outside 位置規則 |
| 與課本記法對照 | 1:N 記法 vs. Crow's Foot 記法 |
| Cardinalities Setting | ERDPlus 設定面板的「方向相反」陷阱 |
| Summary of Cardinalities | Mandatory／optional、1-N／M-N／1-1 的判定條件 |
| Weak Entity | 弱實體與 identification dependency |
| Relationships with Attributes | 關聯帶屬性（Enroll–Grade） |
| Recursive Relationship | 自我參照關聯（Course PrereqTo） |
| Associative Entity | 以關聯實體表達 M-way relationship |
| Specialization/Generalization | ERDPlus 採 top-down specialization |
| Union (Category) | ERDPlus **不支援**，需自行轉換 |

> 本章重點在**記法（syntax）**本身，而非如何應用在商業問題上（那是 Mannino Ch. 6 的範圍）。目標是能精確地畫出無記法錯誤的圖，並能偵測他人圖中的記法錯誤。

---

## 1. 基本符號（Basic Symbols）

Entity、attribute、relationship 的符號與課本（Elmasri）完全相同：矩形、橢圓、菱形。

> **主要差異在 relationship constraints**——ERDPlus 用 Crow's Foot 符號取代課本的 1、N、M 標記。

| 元素 | 內容 |
|---|---|
| **Entity type** | 一群有興趣的事物：人、地、物、事件；含有屬性（如同資料表的欄位）與 primary key。單一成員稱為 entity |
| **Relationship** | 實體間**具名**的關聯；類似關聯式模型中的 foreign key，但多了名稱與 cardinality |
| **Attribute** | Entity type 或 relationship 的性質；有 data type 決定可存的值與可做的運算 |

關聯的**雙向性（bidirectional）**：

- 可以從兩個方向導覽，因此有兩個名稱
- Course → Section：`Has`、`Provides`
- Section → Course：`IsProvidedFor`
- 命名原則：盡量用**主動動詞**（active verb），但不一定做得到

---

## 2. Cardinality 記法（Crow's Foot）

### 2.1 三個符號

| 符號 | 形狀 | 意義 |
|---|---|---|
| **○** | 橢圓／圓圈 | zero，optional（0） |
| **│** | 垂直短線 | one，mandatory（1） |
| **≺** | 鳥爪（crow's foot） | many（0 或更多，不受限） |

部分繪圖工具支援填入**精確數字**的 cardinality。

### 2.2 位置規則（最容易錯的地方）

每一端會畫**兩個**符號：

| 位置 | 意義 |
|---|---|
| **Outside**（離 entity 較遠、靠近線的中段） | **min** cardinality |
| **Inside**（緊鄰 entity 方框） | **max** cardinality |

### 2.3 讀法：看「對面」那一端的符號

> **Interpret the far cardinality symbols** — 要知道「一個 A 對應幾個 B」，去看**靠近 B** 那一端的符號。

以 Course `Has` Section 為例：

```text
            ││                              ○≺
  ┌────────┐ │                              │ ┌─────────┐
  │ Course │─┴────◇ Has ◇───────────────────┴─│ Section │
  └────────┘                                  └─────────┘
   min=1,max=1                                min=0,max=many
```

| 要讀的句子 | 看哪一端 | 結果 |
|---|---|---|
| 一門 Course 對應幾個 Section？ | Section 端（○ + 鳥爪） | min 0、max many |
| 一個 Section 對應幾門 Course？ | Course 端（│ + │） | min 1、max 1（恰好一門） |

### 2.4 與課本記法對照

```text
課本（Elmasri）：   Course ──1── ◇Has◇ ──N── Section

Crow's Foot：      Course ─││───◇Has◇───○≺─ Section
```

兩者描述同一件事：1:N。但**標記的位置相反**——課本把 N 標在 Section 側的線上，Crow's Foot 把「many」畫在 Section 旁。

> 更精確地說：課本的 1 與 N 分別標在**各自實體**那一側，而 Crow's Foot 的符號描述的是**對面實體**的數量。這是兩套記法互轉時最常出錯的地方。

---

## 3. ERDPlus 的 Cardinality 設定（⚠ 重要陷阱）

> **Notice!!! When setting cardinality, the direction is inversely specified on the entities.**

ERDPlus 的設定面板分成 Entity One 與 Entity Two 兩區，各有兩組選項：

| 欄位 | 選項 |
|---|---|
| 左欄（min） | Mandatory／Optional／Unspecified |
| 右欄（max） | One／Many／Unspecified |

**陷阱**：在「Entity One: Course」底下選 Optional + Many，符號會畫在**Section 那一端**，而不是 Course 旁邊。

```text
設定面板                          畫出來的結果
┌──────────────────────┐
│ Entity One: Course   │
│  ○ Mandatory  ○ One  │
│  ● Optional   ● Many │────→   符號畫在 Section 端（○≺）
│                      │
│ Entity Two: Section  │
│  ● Mandatory  ● One  │────→   符號畫在 Course 端（││）
│  ○ Optional   ○ Many │
└──────────────────────┘
```

最終結果：Course ─││──◇Has◇──○≺─ Section，即「每個 Section 恰好屬於 1 門 Course；每門 Course 有 0 到多個 Section」。

> 記住：**設定的實體名稱 ≠ 符號畫出來的位置**。設定前先想清楚自己要描述的是哪一個方向。

---

## 4. Cardinality 的分類與整理

### 4.1 依 minimum cardinality 分類

| 類型 | 條件 | 意義 |
|---|---|---|
| **Mandatory** | min > 0 | 必須參與；造成 **existence dependency** |
| **Optional** | min = 0 | 可不參與；類似允許 NULL 的 foreign key |

> **Existence dependency（存在相依）**：某個 entity 除非另一個相關的 entity 存在，否則它不能存在。Mandatory 關聯就會產生 existence dependency。

### 4.2 依 maximum cardinality 分類

| 類型 | 條件 |
|---|---|
| **Functional** | 某一端 max = 1（數學上的函數概念） |
| **1-N** | Entity 1: max = 1；Entity 2: max > 1 |
| **M-N** | Entity 1 與 2: max 皆 > 1 |
| **1-1** | Entity 1 與 2: max 皆 = 1（較少見） |

### 4.3 總表

| Type | Cardinality restriction |
|---|---|
| Mandatory | min > 0 |
| Optional | min = 0 |
| 1-N | Entity 1: max = 1，Entity 2: max > 1 |
| M-N | Entity 1 & 2: max > 1 |
| 1-1 | Entity 1 & 2: max = 1 |

---

## 5. 弱實體與識別相依（Weak Entity and Identification Dependency）

ERDPlus 的弱實體記法與課本相同：**雙框矩形** + **雙框菱形**（identifying relationship）。

| 概念 | 說明 |
|---|---|
| 定義 | 某些 entity type 借用（borrow）另一實體的部分或全部 PK |
| 關聯式模型中的對應 | PK 中包含 FK 的那一部分 |
| 典型情境 | 實體上的包含關係（physical containment）：building–room、country–state、order–orderline |
| 符號 | 弱實體：方框四角加斜線（ERDPlus 畫法）／雙框；identifying relationship：實線／雙框菱形 |

範例：BUILDING `Contain` ROOM

```text
 BuildNo  Buildname                RoomNo   RoomCapacity
    │        │                        │         │
 ┌───────────┐                   ╔═══════════╗
 │ BUILDING  │─││──◇Contain◇──≺──║   ROOM    ║
 └───────────┘                   ╚═══════════╝
```

| 要點 | 說明 |
|---|---|
| PK 組成 | ROOM 的 PK = RoomNo（local key）+ BldgID（borrowed attribute） |
| Cardinality 限制 | 弱實體在 identifying relationship 中必須是 **1-1**（mandatory + one） |
| 存在性 | 沒有對應的 BUILDING，ROOM 就不能存在 |
| 關係 | Identification dependency **包含** existence dependency：弱實體既存在相依，又借用 PK |
| 注意 | 若一個實體參與多個關聯，identifying relationship 要標清楚，否則會產生歧義 |

---

## 6. 帶屬性的關聯（Relationships with Attributes）

> Relationships are **first-class citizens**——關聯可以像 entity type 一樣擁有屬性。

| 要點 | 說明 |
|---|---|
| 最典型場合 | **M-N** 關聯 |
| 判準 | 該屬性同時依賴**兩個** entity type，而非只依賴其中一個 |
| 爭議 | 1-M 關聯帶屬性在設計上有爭議 |

範例：Student `Enroll` Section，屬性 **Grade**

```text
            Section
               │
          ◇ Enroll ◇──── Grade
               │
            Student
```

Grade 是「某位學生在某個開課班次的成績」，同時依賴 Student 與 Section，不屬於任何一方的實體屬性。

---

## 7. 遞迴關聯（Recursive Relationship）

關聯把 entity type 連到它**自己**。

範例：Course 的先修關係 `PrereqTo`

```text
       ┌──────────┐
       │  Course  │
       └──┬────┬──┘
          ○    ○
          ≺    ≺
          └─◇PrereqTo◇─┘
```

| 要點 | 說明 |
|---|---|
| Cardinality 位置 | **不重要**，因為兩端是同一個 entity type |
| 其他 | 除此之外與一般關聯沒有差別 |
| 關鍵判斷 | 究竟是 1-M 還是 M-N？建議畫 **instance diagram** 來推理 |

> 先修關係通常是 M-N：一門課可以有多門先修課，一門課也可以是多門課的先修課。

---

## 8. Associative Entity 與 M-way Relationship

### 8.1 問題

Crow's Foot 記法**不支援** M-way（三元以上）relationship。

### 8.2 解法：Associative Entity Type

| 性質 | 說明 |
|---|---|
| 本質 | 一個依賴**兩個以上** entity type 的弱實體 |
| 用途 | 取代 M-N 或 M-way relationship |
| PK | 通常借用**全部**的 PK |
| 連接方式 | 以多個 identifying relationship 連到各參與實體 |

範例：PART、SUPPLIER、PROJECT 三元關聯

```text
   PART            SUPPLIER            PROJECT
    │                  │                  │
    │             ◇SUPP-USE◇              │
    │                  │                  │
 ◇PART-USE◇──── ╔═══════════╗ ────◇PROJ-USE◇
                ║   USES    ║
                ╚═══════════╝
                 Associative Entity
```

記錄「哪個供應商在哪個專案上供應哪個零件」。

> **使用時機的警告**：這是特化概念，重要但不常見，而且**很容易誤用**。
>
> - ✔ 適用：local purchasing——各專案自行挑選供應商，三者真的綁在一起
> - ✘ 不適用：只需記錄「誰供應什麼零件」和「哪些專案用哪些零件」兩件獨立的事時，應該用兩個 binary 關聯

---

## 9. Specialization / Generalization

符號與課本相同（圓圈內 d／o、子類別以 ⊂ 符號連接）。

> **Note：ERDPlus 採用 top-down specialization**（由超類別往下畫子類別）。

| 詞彙 | 意義 |
|---|---|
| **Supertype** | 父實體型別（課本稱 superclass） |
| **Subtype** | 子實體型別，擁有額外屬性（課本稱 subclass） |
| **Generalization hierarchy** | 即 ISA 階層（ISA 不是縮寫，就讀作 "is a"） |

```text
    Name ─ UNIV-PERSON ─ BDate, Address, SSN
                │
               (d)
        ┌───────┴───────┐
     FACULTY         STUDENT
     (Rank)        (StdNo, Major)
```

- SalaryEmp **IS AN** Employee
- **Set inclusion**：子類別是超類別的**子集合**——SalaryEmp 的實體集合是 Employee 實體集合的子集

| 使用原則 | 說明 |
|---|---|
| 何時使用 | 當多個 entity type 相似但屬性有差異時 |
| 警告 | 特化技巧，**不要過度使用**；重要但不常見 |

---

## 10. Union（Category）：ERDPlus 不支援

> ERDPlus **does not support** Union (Category) type.

必須自行把 Union 轉換成 superclass/subclass 結構：

| 情形 | 轉換方式 |
|---|---|
| **Total** union type | 反向轉成 superclass/subclass（即改成 generalization） |
| **Partial** union type | **沒有統一的解法**，須依實際情況決定 |

> 這與 Chapter 4 的結論一致：total category 可以改寫成 specialization／generalization，partial category 則不行。在 ERDPlus 中遇到 partial category，只能依語意自行設計替代結構。

---

## 11. 綜合範例（Comprehensive Example）

一張圖涵蓋前面所有記法：

```text
   Name ─ UNIV-PERSON ─ SSN, BDate, Address
                │
               (d)
        ┌───────┴────────┐
     FACULTY          STUDENT ─ StdNo, Major
      (Rank)             │
        │             ◇Enroll◇ ─ Grade
     ◇Teach◇             │
        │                │
   COURSE ──◇Has◇── SECTION ─ SecNo, Sec_Loction, Sec_Time
    │  │
  ◇PrereqTo◇（遞迴）
```

| 記法 | 出現位置 |
|---|---|
| 1-M relationships | Has、Teaches、Supervises、Registers、Grants |
| **Optional** relationships | Teaches、Supervises |
| **Mandatory** relationships | Has、Registers、Grants |
| **Self-referencing** relationship | Supervises（本投影片中為 PrereqTo） |
| **Weak entity**（同時是 associative entity） | Enrollment／Enroll |
| **Identifying** relationships | Registers、Grants |
| **Generalization hierarchy** | UnivPerson → {Student, Faculty} |

> 補充：Enrollment、Registers、Grants 這組結構，可以用 **relationship equivalence** 轉換成「一個帶屬性的 M-N relationship」——兩種畫法等價。

---

## 12. 總結

| 要點 | 說明 |
|---|---|
| Data modeling 是資料庫設計最重要的技能 | 部分是科學、部分是藝術，本質上帶有 ambiguity |
| Crow's Foot ERD 記法被廣泛使用 | 業界常見 |
| 必須**精確**使用記法 | 就像使用程式語言一樣精確；diagram rules 之於 ERD，相當於 syntax 之於文字語言 |
| 先懂記法，才能應用 | 理解記法是把它應用在商業問題上的前提 |
| 檢查工具 | 可用 ER Assistant 的 **Check Diagram** 功能自我檢查 |

---

## 重點整理

| 概念 | 核心意義 | 關鍵字 / 注意事項 |
|---|---|---|
| ○ / │ / ≺ | zero / one / many | ○＝optional，│＝mandatory |
| Inside / Outside | Inside（貼著實體）＝max；Outside（遠離實體）＝min | 兩個符號一組，共四種組合 |
| 讀法 | 看**對面**那一端的符號 | 「一個 Course 有幾個 Section」→ 看 Section 端 |
| 與課本對照 | 1:N 的標記位置與 Crow's Foot **相反** | 互轉時最易出錯 |
| ERDPlus 設定 | 設定面板的實體名稱與符號繪製位置**相反** | 投影片以紅字標為 Notice!!! |
| Mandatory | min > 0 | 產生 existence dependency |
| Weak entity | 借用部分或全部 PK | 在 identifying relationship 中必為 1-1 |
| 關聯屬性 | 同時依賴兩端實體 | 最典型是 M-N（Enroll–Grade） |
| Recursive relationship | 連到自己 | Cardinality 位置不重要；用 instance diagram 判斷 1-M 或 M-N |
| Associative entity | 取代 M-way relationship | Crow's Foot 不支援三元關聯；易誤用 |
| Specialization | ERDPlus 採 top-down | Supertype／subtype；ISA；子集合關係 |
| Union (Category) | ERDPlus **不支援** | total → 轉 superclass/subclass；partial → 無統一解法 |
