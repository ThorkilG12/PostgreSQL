Here is a smal organisation. Top manager is Employee number 11. Emp 11 has no Boss.  
There are 3 Bosses (11,22,34) and three low-level Floor Workers (33,44,45)
```
     11
     22
 33---+---34 
       44--+--45
```
```SQL
DROP TABLE if EXISTS OrgHier;
CREATE TEMP TABLE OrgHier AS (
SELECT * FROM (
            SELECT * FROM (SELECT 11 AS Emp, CAST(NULL AS INT4) AS Boss) a
  UNION ALL SELECT * FROM (SELECT 22 AS Emp, 11 AS Boss) a
  UNION ALL SELECT * FROM (SELECT 33 AS Emp, 22 AS Boss) a
  UNION ALL SELECT * FROM (SELECT 34 AS Emp, 22 AS Boss) a
  UNION ALL SELECT * FROM (SELECT 44 AS Emp, 34 AS Boss) a
  UNION ALL SELECT * FROM (SELECT 45 AS Emp, 34 AS Boss) a
) a
);
SELECT * 
  FROM OrgHier
;
```
Lets see how all 6 persons fits into the Boss-chain.  
```SQL
WITH RECURSIVE MyOrg (Emp, Boss, Lvl, List) AS (
SELECT Emp, Boss, 1, CAST('#' AS VARCHAR(100)) AS List
  FROM OrgHier
  WHERE Boss IS NULL
UNION ALL
SELECT oh.Emp, oh.Boss, mo.lvl + 1, CAST(list || CAST(oh.Boss AS VARCHAR(100)) || '#' AS VARCHAR(100)) AS List
  FROM OrgHier oh
    INNER JOIN MyOrg AS mo
      ON mo.Emp=oh.Boss
) 
SELECT Emp, List , Lvl
  FROM MyOrg
ORDER BY Emp, Lvl, Boss
;
-- Lets see how it looks from all three Bosses' perspective
-- Notice that Lvl now shows how "far away" a Boss is
WITH RECURSIVE BossList (Emp, Boss, Lvl) AS 
(
SELECT Emp, Boss, 1
  FROM OrgHier
  WHERE Boss IS NOT NULL
UNION ALL
SELECT bl.Emp, oh.Boss, bl.Lvl + 1
  FROM BossList bl
    INNER JOIN OrgHier AS oh 
      ON bl.Boss = oh.Emp
  WHERE oh.Boss IS NOT NULL
)
SELECT Boss, Emp, Lvl
  FROM BossList
ORDER BY Boss, Emp, Lvl
;
```
--oOo--
