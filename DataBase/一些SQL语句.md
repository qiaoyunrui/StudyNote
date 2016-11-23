# 一些SQL语句

## 根据分组获取平均成绩并把这些数据插入另一个表中

```SQL
INSERT
INTO  Dept_age(Sdept，Avg_age)
SELECT  Sdept，AVG(Sage)
FROM  Student
GROUP BY Sdept;
```

## 同时修改表中所有数据的一个字段

```SQL
UPDATE Student
SET Sage = Sage + 1;
```

## 不知道该怎么定义

```SQL
UPDATE SC
SET Grade = 0
WHERE 'CS' =
(SELETE Sdept
FROM  Student
WHERE  Student.Sno = SC.Sno);
```

## 建立视图

```SQL
CREATE VIEW CS_S
        AS
        SELECT Sno,Sname,Sgroup
        FROM  Student
        WHERE  Sgroup = 'A'
        WITH CHECK OPTION;
```

**注**：如果末尾加上`WITH CHECK OPTION`的话，那么这个视图中新插入数据时`Sgroup`必须为A。

## 从三个表获取数据建立一个视图

```SQL
CREATE VIEW S_INFO
AS
SELECT s.Sno,s.Sname,i.Hname,t.Tname,t.Ttel
FROM
Student s,Info i,Teacher - t
WHERE s.Sno = i.Sno AND t.Tno = i.Tno
WITH CHECK OPTION
```

## 赋予某个账户操作表的权限

```SQL
GRANT SELECT ON Student To Juhezi WITH GRANT OPTION;
```

## 册小某个用户操作表的权限

```SQL
REVOKE SELECT ON Sudent FROM Juhezi;
```
