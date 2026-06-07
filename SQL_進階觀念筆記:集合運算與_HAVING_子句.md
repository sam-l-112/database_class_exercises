```python
# Let's create a robust and beautifully formatted Markdown (.md) file matching the user's explicit request.
# The user wants explanations for HAVING, AVG, SUM, COUNT(*), UNION, INTERSECT, DIFFERENCE (EXCEPT), and DISTINCT 
# with detailed descriptions and business examples tailored to the DVD/Transaction/Customer ER diagram seen in the photos.
# It needs to be written in professional Traditional Chinese for a HackMD-compatible document.
```
content = """# 📊 SQL 核心語法與集合運算全攻略 (HackMD 筆記)

本篇筆記針對 DVD 租售管理系統之實體關係圖（ER Diagram：Customer, Transaction, Include, DVD, Publisher）進行延伸探討。內容涵蓋資料庫進階查詢的核心觀念，包含**分組過濾（HAVING）**、**聚合函數（Aggregation Functions）**與**集合運算子（Set Operators）**的詳細功能說明、語法架構及實戰商務範例。

[聚合函數簡易概念範例EX](./EX_HAVING.md)

---

## 📌 一、 分組過濾器：`HAVING`

### 1. 作用與功能
* **功能**：`HAVING` 關鍵字專門用來對 `GROUP BY` 分組後的「統計結果」進行條件篩選。
* **與 WHERE 的核心差異**：
  * `WHERE` 作用在**分組之前**，針對「原始資料列（Rows）」進行過濾，且裡面**絕對不能**包含聚合函數（例如不能寫 `WHERE SUM(...) > 100`）。
  * `HAVING` 作用在**分組之後**，針對「分組統計後的結果（Groups）」進行過濾，通常與聚合函數如 `SUM()`、`COUNT()`、`AVG()` 搭配使用。

### 2. 實戰範例
**情境**：找出 2026 年 5 月份，總成交金額大於 10,000 元的顧客編號、姓名與成交金額。



```text
Markdown file generated successfully.
```
```sql
SELECT 
    C.CID AS 顧客編號, 
    C.NAME AS 顧客姓名, 
    SUM(I.數量 * D.PRICE) AS 五月成交金額
FROM CUSTOMER C
INNER JOIN [TRANSACTION] T ON C.CID = T.CID
INNER JOIN INCLUDE I ON T.TID = I.TID
INNER JOIN DVD D ON I.DID = D.DID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5 -- 先過濾出五月份的原始資料
GROUP BY C.CID, C.NAME                                  -- 依顧客進行分組
HAVING SUM(I.數量 * D.PRICE) > 10000                     -- 篩選出分組後總金額 > 10,000 的組別
ORDER BY C.CID;

```

---

## 📌 二、 常用聚合函數 (Aggregation Functions)

聚合函數用來將多筆資料列（Rows）進行數值計算，最終匯總、壓縮成單一的值。通常與 `GROUP BY` 搭配。

### 1. `SUM()` — 求總和

* **功能**：計算指定數值欄位的累加總和。
* **商務範例**：計算 2026 年 5 月份系統的**總營業額**（所有交易品項的數量乘上單價）。

```sql
SELECT SUM(I.數量 * D.PRICE) AS 五月總營業額
FROM [TRANSACTION] T
INNER JOIN INCLUDE I ON T.TID = I.TID
INNER JOIN DVD D ON I.DID = D.DID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5;

```

### 2. `AVG()` — 求平均值

* **功能**：計算指定數值欄位的算術平均數（自動忽略 NULL 值）。
* **商務範例**：分析 2026 年 5 月份每筆交易單項商品的**平均消費金額**。

```sql
SELECT AVG(I.數量 * D.PRICE) AS 五月單項平均消費
FROM [TRANSACTION] T
INNER JOIN INCLUDE I ON T.TID = I.TID
INNER JOIN DVD D ON I.DID = D.DID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5;

```

### 3. `COUNT(*)` — 計算資料筆數

* **功能**：計算符合條件的資料總列數（包含包含 NULL 值或重複值的資料列）。
* **商務範例**：計算 2026 年 5 月份，所有顧客總共**購買/出租了多少人次的 DVD 品項數**。

```sql
SELECT COUNT(*) AS 五月總銷售品項次數
FROM [TRANSACTION] T
INNER JOIN INCLUDE I ON T.TID = I.TID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5;

```

### 💡 補充：`DISTINCT` — 去除重複資料

* **功能**：篩選出欄位中完全不重複的唯一值。
* **商務範例**：算出 2026 年 5 月份，**實際來消費的「不重複顧客總人數」**（一位顧客來三次，也只算一人）。

```sql
SELECT COUNT(DISTINCT CID) AS 五月實際消費顧客數
FROM [TRANSACTION]
WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5;

```

---

## 📌 三、 集合運算子 (Set Operators)

集合運算用來**將兩個或多個獨立 SELECT 查詢的結果集（Result Sets）合併為一個**。
⚠️ **使用限制（非常重要，考試必考）**：

1. 兩個查詢的**欄位數量**必須完全相同。
2. 對應欄位的**資料型態**必須相容。
3. 欄位名稱預設會以**第一個 SELECT** 語句定義的別名為主。

---

### 1. `UNION` (聯集 / 去重合併)

* **功能**：將兩個查詢的結果合併在一起，並**自動去除重複**的資料列。
* **商務情境**：行銷部門想發送優惠券給「**2026年5月份有消費**」**或者**「**2026年6月份有消費**」的所有顧客編號（兩者符合其一即可，重複出現的顧客只留一筆）。

```sql
-- 查詢 1：五月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5

UNION

-- 查詢 2：六月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 6;

```

> 💡 **小撇步**：如果希望保留重複的資料（例如某顧客5月、6月都有來，要出現兩次），請使用 `UNION ALL`，其執行速度也比 `UNION` 快，因為不需要做去重排序。

---

### 2. `INTERSECT` (交集 / 共同擁有)

* **功能**：只保留**同時存在**於兩個查詢結果集中的重疊資料。
* **商務情境**：CRM 系統想篩選出「**2026年5月份有來消費，且6月份也持續來消費**」的超高黏著度忠實老顧客名單。

```sql
-- 查詢 1：五月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5

INTERSECT

-- 查詢 2：六月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 6;

```

---

### 3. `EXCEPT` / `MINUS` (差集 / Difference / 扣除)

* **功能**：從第一個查詢結果中，**扣除掉**同時存在於第二個查詢結果的資料（集合 A - 集合 B）。
* **標準 SQL 語法**使用 `EXCEPT`；而在 Oracle 資料庫中則叫作 `MINUS`。
* **商務情境**：客戶關係維護（流失客挽回）。找出「**2026年5月份有來消費，但是6月份完全沒來消費**」的顧客編號。我們需要針對這群人發送簡訊、提供回流折扣。

```sql
-- 查詢 1：五月消費的顧客（集合 A）
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5

EXCEPT

-- 查詢 2：六月消費的顧客（集合 B）
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 6;

```

---

## 📊 集合運算子快速總結對照表

| 運算子 | 數學概念 | 包含資料範圍 | 商務應用情境舉例 |
| --- | --- | --- | --- |
| **`UNION`** | 聯集 ($A \cup B$) | 屬於 A 或屬於 B 的不重複資料 | 擴大受眾：5月或6月有消費的所有客戶。 |
| **`INTERSECT`** | 交集 ($A \cap B$) | 同時屬於 A 且屬於 B 的資料 | 活躍客篩選：連續兩個月都有來消費的常客。 |
| **`EXCEPT`** | 差集 ($A - B$) | 屬於 A 但不屬於 B 的資料 | 流失客預警：5月有來但6月沒來的顧客。 |

---

*本筆記完美對應 2026 春季班資料庫管理系統課程白板與投影片講義內容。*
"""

with open("SQL_Aggregation_and_Sets_Guide.md", "w", encoding="utf-8") as f:
f.write(content)

print("Markdown file generated successfully.")


[file-tag: code-generated-file-0-1780280916565521353]

這份筆記專門針對您投影片上的「DVD 租售管理系統」情境進行設計，並完美融合了老師在白板上強調的 **DISTINCT、HAVING、AVG、SUM、COUNT(*)** 以及三個集合運算（**UNION 聯集、INTERSECT 交集、EXCEPT 差集**）。

以下是為您整理的筆記完整預覽內容，您可以直接複製到 HackMD 使用：

***

# 📊 SQL 核心語法與集合運算全攻略 (HackMD 筆記)

本篇筆記針對 DVD 租售管理系統之實體關係圖（ER Diagram：Customer, Transaction, Include, DVD, Publisher）進行延伸探討。內容涵蓋資料庫進階查詢的核心觀念，包含**分組過濾（HAVING）**、**聚合函數（Aggregation Functions）**與**集合運算子（Set Operators）**的詳細功能說明、語法架構及實戰商務範例。

---

## 📌 一、 分組過濾器：`HAVING`

### 1. 作用與功能
* **功能**：`HAVING` 關鍵字專門用來對 `GROUP BY` 分組後的「統計結果」進行條件篩選。
* **與 WHERE 的核心差異**：
  * `WHERE` 作用在**分組之前**，針對「原始資料列（Rows）」進行過濾，且裡面**絕對不能**包含聚合函數（例如不能寫 `WHERE SUM(...) > 100`）。
  * `HAVING` 作用在**分組之後**，針對「分組統計後的結果（Groups）」進行過濾，通常與聚合函數如 `SUM()`、`COUNT()`、`AVG()` 搭配使用。

### 2. 實戰範例
**情境**：找出 2026 年 5 月份，總成交金額大於 10,000 元的顧客編號、姓名與成交金額（對應投影片第 2 題）。
```sql
SELECT 
    C.CID AS 顧客編號, 
    C.NAME AS 顧客姓名, 
    SUM(I.數量 * D.PRICE) AS 五月成交金額
FROM CUSTOMER C
INNER JOIN [TRANSACTION] T ON C.CID = T.CID
INNER JOIN INCLUDE I ON T.TID = I.TID
INNER JOIN DVD D ON I.DID = D.DID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5 -- 先過濾出五月份的原始資料
GROUP BY C.CID, C.NAME                                  -- 依顧客進行分組
HAVING SUM(I.數量 * D.PRICE) > 10000                     -- 篩選出分組後總金額 > 10,000 的組別
ORDER BY C.CID;

```

---

## 📌 二、 常用聚合函數 (Aggregation Functions)

聚合函數用來將多筆資料列（Rows）進行數值計算，最終匯總、壓縮成單一的值。通常與 `GROUP BY` 搭配。

### 1. `SUM()` — 求總和

* **功能**：計算指定數值欄位的累加總和。
* **商務範例**：計算 2026 年 5 月份整個系統的**總營業額**（所有交易品項的數量乘上單價）。

```sql
SELECT SUM(I.數量 * D.PRICE) AS 五月總營業額
FROM [TRANSACTION] T
INNER JOIN INCLUDE I ON T.TID = I.TID
INNER JOIN DVD D ON I.DID = D.DID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5;

```

### 2. `AVG()` — 求平均值

* **功能**：計算指定數值欄位的算術平均數（自動忽略 NULL 值）。
* **商務範例**：分析 2026 年 5 月份每筆交易單項商品的**平均消費金額**。

```sql
SELECT AVG(I.數量 * D.PRICE) AS 五月單項平均消費
FROM [TRANSACTION] T
INNER JOIN INCLUDE I ON T.TID = I.TID
INNER JOIN DVD D ON I.DID = D.DID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5;

```

### 3. `COUNT(*)` — 計算資料筆數

* **功能**：計算符合條件的資料總列數（包含包含 NULL 值或重複值的資料列）。
* **商務範例**：計算 2026 年 5 月份，所有顧客總共**購買/出租了多少人次的 DVD 品項數**。

```sql
SELECT COUNT(*) AS 五月總銷售品項次數
FROM [TRANSACTION] T
INNER JOIN INCLUDE I ON T.TID = I.TID
WHERE YEAR(T.DATETIME) = 2026 AND MONTH(T.DATETIME) = 5;

```

### 💡 補充：`DISTINCT` — 去除重複資料

* **功能**：篩選出欄位中完全不重複的唯一值（白板最上方寫的關鍵字）。
* **商務範例**：算出 2026 年 5 月份，**實際來消費的「不重複顧客總人數」**（一位顧客來三次，也只算一人）。

```sql
SELECT COUNT(DISTINCT CID) AS 五月實際消費顧客數
FROM [TRANSACTION]
WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5;

```

---

## 📌 三、 集合運算子 (Set Operators)

集合運算用來**將兩個或多個獨立 SELECT 查詢的結果集（Result Sets）合併為一個**。
⚠️ **使用限制（非常重要，考試必考）**：

1. 兩個查詢的**欄位數量**必須完全相同。
2. 對應欄位的**資料型態**必須相容。
3. 欄位名稱預設會以**第一個 SELECT** 語句定義的別名為主。

---

### 1. `UNION` (聯集 / 去重合併)

* **功能**：將兩個查詢的結果合併在一起，並**自動去除重複**的資料列。
* **商務情境**：行銷部門想發送優惠券給「**2026年5月份有消費**」**或者**「**2026年6月份有消費**」的所有顧客編號（兩者符合其一即可，重複出現的顧客只留一筆）。

```sql
-- 查詢 1：五月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5

UNION

-- 查詢 2：六月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 6;

```

> 💡 **小撇步**：如果希望保留重複的資料（例如某顧客5月、6月都有來，要出現兩次），請使用 `UNION ALL`，其執行速度比 `UNION` 更快，因為不需要做去重排序。

---

### 2. `INTERSECT` (交集 / 共同擁有)

* **功能**：只保留**同時存在**於兩個查詢結果集中的重疊資料。
* **商務情境**：CRM 系統想篩選出「**2026年5月份有來消費，且6月份也持續來消費**」的超高黏著度忠實老顧客名單。

```sql
-- 查詢 1：五月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5

INTERSECT

-- 查詢 2：六月消費的顧客
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 6;

```

---

### 3. `EXCEPT` / `MINUS` (差集 / Difference / 扣除)

* **功能**：從第一個查詢結果中，**扣除掉**同時存在於第二個查詢結果的資料（集合 A - 集合 B）。
* **標準 SQL 語法**使用 `EXCEPT`；而在 Oracle 資料庫中則叫作 `MINUS`。
* **商務情境**：客戶關係維護（流失客挽回）。找出「**2026年5月份有來消費，但是6月份完全沒來消費**」的顧客編號。我們需要針對這群人發送簡訊、提供回流折扣。

```sql
-- 查詢 1：五月消費的顧客（集合 A）
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 5

EXCEPT

-- 查詢 2：六月消費的顧客（集合 B）
SELECT CID FROM [TRANSACTION] WHERE YEAR(DATETIME) = 2026 AND MONTH(DATETIME) = 6;

```

---

## 📊 集合運算子快速總結對照表

| 運算子 | 數學概念 | 包含資料範圍 | 商務應用情境舉例 |
| --- | --- | --- | --- |
| **`UNION`** | 聯集 ($A \cup B$) | 屬於 A 或屬於 B 的不重複資料 | 擴大受眾：5月或6月有消費的所有客戶。 |
| **`INTERSECT`** | 交集 ($A \cap B$) | 同時屬於 A 且屬於 B 的資料 | 活躍客篩選：連續兩個月都有來消費的常客。 |
| **`EXCEPT`** | 差集 ($A - B$) | 屬於 A 但不屬於 B 的資料 | 流失客預警：5月有來但6月沒來的顧客。 |

---

*本筆記完美對應 2026 春季班資料庫管理系統課程白板與投影片講義內容。*
