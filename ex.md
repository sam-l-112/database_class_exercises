# ex
```sql
SELECT 出版社.PH_NAME AS 出版社名, 圖書館.L_NAME AS 書名, COUNT(*) AS 總借書次數
FROM 出版社, 圖書館, 借書
WHERE 圖書館.L_ID = 借書.L_ID
AND 出版社.PH_NO = 圖書館.PH_NO    -- 💡 註：請檢查圖書館內是 PH_NO 還是 PH_on
AND YEAR(Borrow_at) = 2020         -- 修正為題目要求的 2020 年
AND MONTH(Borrow_at) = 5           -- 補上 5 月
GROUP BY 出版社.PH_NAME, 圖書館.L_NAME  -- 核心修正：把 SELECT 的非統計欄位都群組化
ORDER BY COUNT(*) DESC;            -- 依次數由多至少（降冪）排序
```
