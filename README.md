# Microsoft Access exerciser 
# 資料庫練習說明

本文件整理 Microsoft Access 常見 SQL 語法範例，包含：

- 資料表建立（CREATE TABLE）
- 插入資料（INSERT INTO）
- 查詢資料（SELECT）
- 刪除資料（DELETE）
- 更新資料（UPDATE）
- 修改資料表（ALTER TABLE）
- 排序（ORDER BY）
- 聚合函數：COUNT(*)、AVG
- 條件查詢：WHERE、LIKE
- 地址查詢、日期查詢

---

## 1. 建立資料表（CREATE TABLE）

語法：

```sql
CREATE TABLE TableName (
    FieldName1 DataType [(Size)] [CONSTRAINTS],
    FieldName2 DataType [(Size)] [CONSTRAINTS],
    ...
);
```

Access 常見資料型別：

- `AUTOINCREMENT`：自動編號（主鍵常用）
- `TEXT(n)`：文字，n 為最大字元數
- `DATE`：日期時間
- `CURRENCY`：貨幣
- `NUMBER`：數字
- `YESNO`：布林值

範例：建立客戶資料表

```sql
CREATE TABLE Customers (
    CustomerID AUTOINCREMENT PRIMARY KEY,
    CustomerName TEXT(100),
    Addr TEXT(200),
    City TEXT(50),
    JoinDate DATE,
    Amount CURRENCY
);
```

---

## 2. 插入資料（INSERT INTO）

語法：

```sql
INSERT INTO TableName (Field1, Field2, ...)
VALUES (Value1, Value2, ...);
```

Access 特別注意：

- 日期值要用 `#` 包住，例如 `#2024-05-01#`
- 文字值要用單引號 `'` 包住

範例：

```sql
INSERT INTO Customers (CustomerName, Addr, City, JoinDate, Amount)
VALUES ('王小明', '台北市中正區和平東路', '台北', #2024-05-01#, 1200.50);

INSERT INTO Customers (CustomerName, Addr, City, JoinDate, Amount)
VALUES ('李小華', '新北市板橋區文化路', '新北', #2024-04-20#, 850.00);
```

---

## 3. 查詢資料（SELECT）

語法：

```sql
SELECT Column1, Column2, ...
FROM TableName
[WHERE condition]
[GROUP BY Column]
[ORDER BY Column [ASC|DESC]];
```

簡單查詢：

```sql
SELECT CustomerID, CustomerName, Addr, City, JoinDate, Amount
FROM Customers;
```

指定欄位查詢：

```sql
SELECT CustomerName, City, Amount
FROM Customers;
```

條件查詢：

```sql
SELECT CustomerID, CustomerName, Addr, City
FROM Customers
WHERE City = '台北';
```

---

## 4. WHERE 條件查詢

語法：

```sql
SELECT ...
FROM TableName
WHERE condition1 [AND|OR condition2] ...;
```

比較運算符：

- `=` 等於
- `<>` 或 `!=` 不等於
- `>`、`<`、`>=`、`<=`
- `BETWEEN ... AND ...` 範圍查詢

範例：

```sql
SELECT *
FROM Customers
WHERE City = '新北'
  AND Amount > 500;
```

---

## 5. 模糊查詢（LIKE）

Access 使用 `*` 作為萬用字元，`?` 代表單一字元。

語法：

```sql
SELECT Column1, Column2
FROM TableName
WHERE Field LIKE 'pattern';
```

地址模糊查詢範例：

```sql
SELECT *
FROM Customers
WHERE Addr LIKE '*板橋*';
```

查詢地址包含「中正區」：

```sql
SELECT CustomerName, Addr
FROM Customers
WHERE Addr LIKE '*中正區*';
```

---

## 6. 日期查詢

Access 日期一定要用 `#` 包住。

查詢某日期之後：

```sql
SELECT CustomerName, JoinDate
FROM Customers
WHERE JoinDate >= #2024-05-01#;
```

查詢某一日期：

```sql
SELECT *
FROM Customers
WHERE JoinDate = #2024-04-20#;
```

日期範圍查詢：

```sql
SELECT *
FROM Customers
WHERE JoinDate BETWEEN #2024-04-01# AND #2024-04-30#;
```

---

## 7. 更新資料（UPDATE）

語法：

```sql
UPDATE TableName
SET Column1 = Value1, Column2 = Value2, ...
WHERE condition;
```

範例：

```sql
UPDATE Customers
SET Addr = '台北市大安區復興南路'
WHERE CustomerID = 1;

UPDATE Customers
SET Amount = Amount + 200
WHERE City = '台北';
```

---

## 8. 刪除資料（DELETE）

語法：

```sql
DELETE FROM TableName
WHERE condition;
```

注意：若省略 `WHERE`，會刪除整張表所有資料。

範例：

```sql
DELETE FROM Customers
WHERE CustomerID = 2;

DELETE FROM Customers
WHERE Addr LIKE '*板橋*';
```

---

## 9. 修改資料表結構（ALTER TABLE）

新增欄位：

```sql
ALTER TABLE TableName
ADD COLUMN NewField DataType [(Size)];
```

刪除欄位：

```sql
ALTER TABLE TableName
DROP COLUMN FieldName;
```

範例：

```sql
ALTER TABLE Customers
ADD COLUMN Email TEXT(100);

ALTER TABLE Customers
DROP COLUMN Email;
```

---

## 10. 排序（ORDER BY）

語法：

```sql
SELECT Column1, Column2
FROM TableName
ORDER BY Column1 [ASC|DESC], Column2 [ASC|DESC];
```

範例：

```sql
SELECT CustomerName, Amount
FROM Customers
ORDER BY Amount DESC;

SELECT CustomerName, JoinDate
FROM Customers
ORDER BY JoinDate DESC;
```

---

## 11. 聚合查詢：COUNT(*)、AVG

計算總筆數：

```sql
SELECT COUNT(*) AS TotalCustomers
FROM Customers;
```

計算平均值：

```sql
SELECT AVG(Amount) AS AvgAmount
FROM Customers;
```

依城市分組計算筆數與平均金額：

```sql
SELECT City, COUNT(*) AS CustomerCount, AVG(Amount) AS AvgAmount
FROM Customers
GROUP BY City;
```

---

## 12. 綜合範例

地址、日期、排序、聚合的綜合查詢：

```sql
SELECT CustomerName, Addr, City, JoinDate, Amount
FROM Customers
WHERE City = '台北'
  AND JoinDate > #2024-05-01#
ORDER BY Amount DESC;
```

查詢地址含「中正區」且金額大於 1000 的客戶：

```sql
SELECT CustomerName, Addr, Amount
FROM Customers
WHERE Addr LIKE '*中正區*'
  AND Amount > 1000
ORDER BY Amount DESC;
```

---

## 13. 實作練習題

1. 建立名為 `Customers` 的資料表，包含 `CustomerID`、`CustomerName`、`Addr`、`City`、`JoinDate`、`Amount`。
2. 插入 5 筆資料，包含不同城市、地址與日期。
3. 查詢 `City='台北'` 的所有客戶。
4. 查詢地址包含 `板橋` 的客戶。
5. 查詢 `JoinDate` 在 2024 年 5 月之後的資料。
6. 更新 `CustomerID=1` 的地址。
7. 刪除 `Amount < 500` 的資料。
8. 新增一個 `Email` 欄位。
9. 計算每個城市的客戶數與平均金額。
10. 依 `Amount` 由大到小排序查詢。

---

## 14. 常見提醒

- Access 的 `LIKE` 通配符是 `*`，不是 ` % `。
- 日期格式通常是 `#YYYY-MM-DD#`。
- `TEXT` 欄位長度必須指定上限。
- `AUTOINCREMENT` 配合 `PRIMARY KEY` 可自動編號。
- 刪除資料時務必加 `WHERE`，避免刪除整張表。
