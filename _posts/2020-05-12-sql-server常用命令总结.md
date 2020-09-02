---
title: sql-server常用命令总结
tags: sql
---

1. # 部分语句模板

- ## 创建数据库

```mssql
-- Create a new database called 'DatabaseName'
-- Connect to the 'master' database to run this snippet
USE master
GO
-- Create the new database if it does not exist already
IF NOT EXISTS (
    SELECT name
        FROM sys.databases
        WHERE name = N'DatabaseName'
)
CREATE DATABASE DatabaseName
GO
```

- ## 创建表

```mssql
-- Create a new table called 'TableName' in schema 'SchemaName'
-- Drop the table if it already exists
IF OBJECT_ID('SchemaName.TableName', 'U') IS NOT NULL
DROP TABLE SchemaName.TableName
GO
-- Create the table in the specified schema
CREATE TABLE SchemaName.TableName
(
    TableNameId INT NOT NULL PRIMARY KEY, -- primary key column
    Column1 [NVARCHAR](50) NOT NULL,
    Column2 [NVARCHAR](50) NOT NULL
    -- specify more columns here
);
GO
```

- ## 创建存储过程

```mssql
-- Create a new stored procedure called 'StoredProcedureName' in schema 'SchemaName'
-- Drop the stored procedure if it already exists
IF EXISTS (
SELECT *
    FROM INFORMATION_SCHEMA.ROUTINES
WHERE SPECIFIC_SCHEMA = N'SchemaName'
    AND SPECIFIC_NAME = N'StoredProcedureName'
)
DROP PROCEDURE SchemaName.StoredProcedureName
GO
-- Create the stored procedure in the specified schema
CREATE PROCEDURE SchemaName.StoredProcedureName
    @param1 /*parameter name*/ int /*datatype_for_param1*/ = 0, /*default_value_for_param1*/
    @param2 /*parameter name*/ int /*datatype_for_param1*/ = 0 /*default_value_for_param2*/
-- add more stored procedure parameters here
AS
    -- body of the stored procedure
    SELECT @param1, @param2
GO
-- example to execute the stored procedure we just created
EXECUTE SchemaName.StoredProcedureName 1 /*value_for_param1*/, 2 /*value_for_param2*/
GO
```

- ## 创建视图

```mssql
-- Create a new view called 'ViewName' in schema 'SchemaName'
-- Drop the view if it already exists
IF EXISTS (
SELECT *
    FROM sys.views
    JOIN sys.schemas
    ON sys.views.schema_id = sys.schemas.schema_id
    WHERE sys.schemas.name = N'SchemaName'
    AND sys.views.name = N'ViewName'
)
DROP VIEW SchemaName.ViewName
GO
-- Create the view in the specified schema
CREATE VIEW SchemaName.ViewName
AS
    -- body of the view
    SELECT [Column1],
        [Column2],
        [Column3],
    FROM SchemaName.TableName
GO
```

- ## 删除数据库

```mssql
-- Drop the database 'DatabaseName'
-- Connect to the 'master' database to run this snippet
USE master
GO
-- Uncomment the ALTER DATABASE statement below to set the database to SINGLE_USER mode if the drop database command fails because the database is in use.
-- ALTER DATABASE DatabaseName SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
-- Drop the database if it exists
IF EXISTS (
  SELECT name
   FROM sys.databases
   WHERE name = N'DatabaseName'
)
DROP DATABASE DatabaseName
GO
```

- ## 删除表

```mssql
-- Drop the table 'TableName' in schema 'SchemaName'
IF EXISTS (
    SELECT *
        FROM sys.tables
        JOIN sys.schemas
            ON sys.tables.schema_id = sys.schemas.schema_id
    WHERE sys.schemas.name = N'SchemaName'
        AND sys.tables.name = N'TableName'
)
    DROP TABLE SchemaName.TableName
GO
```

- ## 删除字段

```mssql
-- Drop 'ColumnName' from table 'TableName' in schema 'SchemaName'
ALTER TABLE SchemaName.TableName
    DROP COLUMN ColumnName
GO
```

- ## 删除存储过程

```mssql
-- Drop the stored procedure called 'StoredProcedureName' in schema 'SchemaName'
IF EXISTS (
SELECT *
    FROM INFORMATION_SCHEMA.ROUTINES
WHERE SPECIFIC_SCHEMA = N'SchemaName'
    AND SPECIFIC_NAME = N'StoredProcedureName'
)
DROP PROCEDURE SchemaName.StoredProcedureName
GO
```

- ## 删除视图

```mssql
-- Drop the view 'ViewName' in schema 'SchemaName'
IF EXISTS (
    SELECT *
        FROM sys.views
        JOIN sys.schemas
            ON sys.views.schema_id = sys.schemas.schema_id
    WHERE sys.schemas.name = N'SchemaName'
        AND sys.views.name = N'ViewName'
)
    DROP VIEW SchemaName.ViewName
GO
```

- GetHelp

```mssql
/*
mssql getting started:
-----------------------------
1. Change language mode to SQL: Open a .sql file or press Ctrl+K M (Cmd+K M on Mac) and choose 'SQL'.
2. Connect to a database: Press F1 to show the command palette, type 'sqlcon' or 'sql' then click 'Connect'.
3. Use the T-SQL editor: Type T-SQL statements in the editor using T-SQL IntelliSense or type 'sql' to see a list of code snippets you can tweak & reuse.
4. Run T-SQL statements: Press F1 and type 'sqlex' or press Ctrl+Shift+e (Cmd+Shift+e on Mac) to execute all the T-SQL code in the editor.

Tip #1: Put GO on a line by itself to separate T-SQL batches.
Tip #2: Select some T-SQL text in the editor and press `Ctrl+Shift+e` (`Cmd+Shift+e` on Mac) to execute the selection
*/
```

- ## 获取空白

```mssql
-- Get the space used by table TableName
SELECT TABL.name AS table_name,
INDX.name AS index_name,
SUM(PART.rows) AS rows_count,
SUM(ALOC.total_pages) AS total_pages,
SUM(ALOC.used_pages) AS used_pages,
SUM(ALOC.data_pages) AS data_pages,
(SUM(ALOC.total_pages)*8/1024) AS total_space_MB,
(SUM(ALOC.used_pages)*8/1024) AS used_space_MB,
(SUM(ALOC.data_pages)*8/1024) AS data_space_MB
FROM sys.tables AS TABL
INNER JOIN sys.indexes AS INDX
ON TABL.object_id = INDX.object_id
INNER JOIN sys.partitions AS PART
ON INDX.object_id = PART.object_id
AND INDX.index_id = PART.index_id
INNER JOIN sys.allocation_units AS ALOC
ON PART.partition_id = ALOC.container_id
WHERE TABL.name LIKE '%TableName%'
AND INDX.object_id > 255
AND INDX.index_id <= 1
GROUP BY TABL.name, 
INDX.object_id,
INDX.index_id,
INDX.name
ORDER BY Object_Name(INDX.object_id),
(SUM(ALOC.total_pages)*8/1024) DESC
GO
```

- ## 插入表

```mssql
-- Insert rows into table 'TableName'
INSERT INTO TableName
( -- columns to insert data into
 [Column1], [Column2], [Column3]
)
VALUES
( -- first row: values for the columns in the list above
 Column1_Value, Column2_Value, Column3_Value
),
( -- second row: values for the columns in the list above
 Column1_Value, Column2_Value, Column3_Value
)
-- add more rows here
GO
```

- ## 查找表

```mssql
-- List columns in all tables whose name is like 'TableName'
SELECT 
    TableName = tbl.TABLE_SCHEMA + '.' + tbl.TABLE_NAME, 
    ColumnName = col.COLUMN_NAME, 
    ColumnDataType = col.DATA_TYPE
FROM INFORMATION_SCHEMA.TABLES tbl
INNER JOIN INFORMATION_SCHEMA.COLUMNS col 
    ON col.TABLE_NAME = tbl.TABLE_NAME
    AND col.TABLE_SCHEMA = tbl.TABLE_SCHEMA

WHERE tbl.TABLE_TYPE = 'BASE TABLE' and tbl.TABLE_NAME like '%TableName%'
GO
```

- ## 列举数据库

```mssql
-- Get a list of databases
SELECT name FROM sys.databases
GO
```

- ## 列举当前数据库的表和视图

```mssql
-- Get a list of tables and views in the current database
SELECT table_catalog [database], table_schema [schema], table_name name, table_type type
FROM INFORMATION_SCHEMA.TABLES
GO
```

- ## 查询

```mssql
-- Select rows from a Table or View 'TableOrViewName' in schema 'SchemaName'
SELECT * FROM SchemaName.TableOrViewName
WHERE 	/* add search conditions here */
GO
```

- ## 更新表里面的数据

```mssql
-- Update rows in table 'TableName'
UPDATE TableName
SET
    [Colum1] = Colum1_Value,
    [Colum2] = Colum2_Value
    -- add more columns and values here
WHERE 	/* add search conditions here */
GO
```

- ## 添加字段

```mssql
-- Add a new column 'NewColumnName' to table 'TableName' in schema 'SchemaName'
ALTER TABLE SchemaName.TableName
    ADD NewColumnName /*new_column_name*/ int /*new_column_datatype*/ NULL /*new_column_nullability*/
GO
```

- ## 删除表里面的行数据

```mssql
-- Delete rows from table 'TableName'
DELETE FROM TableName
WHERE 	/* add search conditions here */
GO
```

------

1. # 详细语句

- `SELECT`

```mssql
SELECT [ALL|DISTINCT][TOP n[PERCENT]]<目标列表达式>[, … n]    [INTO <新表名>]
FROM <表名>|<视图名>[, … n]
[WHERE <条件表达式>]
[GROUP BY <列名l>
[HAVING <条件表达式>]]
[ORDER BY <列名2>[ASC|DESC]]；  
```

1. ALL：表示输出所有记录，包括重复记录。DISTINCT表示输出无重
   复结果的记录。TOP n [PERCENT]指定返回查询结果的前n行数据，如
   果指定PERCENT关键字，则返回查询结果的前n%行数据。 
2. <目标列表达式>：描述结果集的列，它制定了结果集中要包含的列的名称。
3. INTO <新表名>：指定使用结果集来创建新表，<新表名>指定新表的名称。  
4. FROM <表名>|<视图名>：该子句指定从中查询到结果集数据的源表名或源视图名。

5. WHERE <条件表达式>：该子句是一个筛选条件，它定义了源表或源视图中的行要满足SELECT语句的要求所必须达到的条件。 
6. GROUP BY <列名l>：该子句将结果按<列名l>的值进行分组，该属性列值相等的元组为一个组，通常需要在每组上取聚集函数值。
7.    HAVING <条件表达式>：该子句是应用于结果集的附加筛选，用来向使用GROUP BY子句的查询中添加数据过滤准则。
8. ORDER BY <列名2> [ASC|DESC]：该子句定义了结果集中行的排序顺序，升序使用ASC关键字，降序使用DESC关键字，默认为升序。 

### 确定范围

```markdown
语句BETWEEN…AND…和NOT BETWEEN…AND…可以用来查找属性值在（或不在）指定范围内的元组，其中BETWEEN后是范围的下限（即低值），AND后是范围的上限（即高值）
```



## 确定集合

```markdown
运算符IN可以用来查找属性值属于指定集合的元组
```

## 字符匹配

```markdown
[NOT] LIKE ’<匹配串>’[ESCAPE ’<换码字符>’]
其含义是查找指定的属性列值与<匹配串>相匹配的元组。<匹配串>可以是一个完整的字符串，也可以含有通配符%和_。其中：
 %（百分号）：代表任意长度（长度可以为0）的字符串。例如a%b表示以a开头，以b结尾的任意长度的字符串。如acb，addgb，ab等都满足该匹配串。
 _（下划线）：代表任意单个字符或汉字。例如a_b表示以a开头，以b结尾的长度为3的任意字符串。如acb，a王b等都满足该匹配串。 
```

## 涉及空值的查询例

```mssql
where **** IS NULL
```

## 多重条件查询

```markdown
可用逻辑运算符AND和OR来联结多个查询条件。AND的优先级高于OR，但可以用括号改变优先级
```

## ORDER BY

```mssql
  用户可以用ORDER BY子句对查询结果按照一个或多个属性列的升序（ASC）或降序（DESC）排列，缺省值为升序
SELECT *** FROM **
WHERE ***
ORDER BY *** DESC(ASC)
```

## 带HAVING子句的分组查询

```markdown
当完成数据结果的查询和统计后，可以使用HAVING关键字来对查询和统计的结果进行进一步的筛选。
```

## 输出前n行

```mssql
可以利用TOP语句输出查询结果集的前面若干行元组。也可以利用INTO语句将查询结果集输出到一个新建的数据表中
SELECT TOP NUMBER ** FROM ***
WHERE ***
ORDER BY **
TOP后面跟数字表示前几行，后跟PERCENT　表示百分比
```

## 查询结果集输出到新建表中 

```markdown
INTO子句用于把查询结果存放到一个新建的表中。新建的表名由<新表名>给出，新表的列由SELECT子句中指定的列构成
```

## 集合并运算

```mssql
集合并运算是将来自不同查询的结果集合组合起来，形成一个具有综合信息的查询结果集（并集），UNION操作会自动将重复的元组去除。
SELECT ** FROM **
WHERE ***
UNION
SELECT ** FROM **
WHERE ***
```

## 集合交运算

```mssql
集合交运算是将来自不同查询结果集合中共有的元组组合起来，形成一个具有综合信息的查询结果集（交集）
SELECT ** FROM **
WHERE ***
INTERSECT
SELECT ** FROM **
WHERE ***
```

## 集合差运算

```mssql
集合差运算是将属于左查询结果集但不属于右查询结果集的元组组合起来，形成一个具有综合信息的查询结果集（差集）。
SELECT ** FROM **
WHERE ***
EXCEPT
SELECT ** FROM **
WHERE ***
```

## 连接查询-内连接

```mssql
SELECT <目标列表达式> [, … n]
    FROM <表1> INNER JOIN <表2>
   ON <连接条件表达式>[, … n]
注意：连接条件表达式中的各连接字段类型必须是可比的，但名称不必相同。
 USE JXGL
GO
SELECT S.*,SC.*
FROM S INNER JOIN SC
ON S.SNO=SC.SNO 
GO 
```

## 连接查询-外连接

```mssql
(1) 左外连接
左外连接是对连接条件左边的表不加限制。当左边表元组与右边表元组不匹配时，与右边表的相应列值取NULL。语句格式如下：
    SELECT <目标列表达式>[, … n]
    FROM <表1>LEFT[OUTER]JOIN <表2>[, … n]
    ON <连接条件表达式>

  (2) 右外连接
右外连接是对连接条件右边的表不加限制。当右边表元组与左边表元组不匹配时，与左边表的相应列值取NULL。语句格式如下：
SELECT <目标列表达式>[, … n]
FROM <表1> RIGHT [OUTER] JOIN <表2>[, … n]
ON <连接条件表达式>

(3) 全外连接
全外连接是对连接条件的两个表都不加限制。当一边表元组与另一边表元组不匹配时，与另一边表的相应列值取NULL。语句格式如下：
SELECT <目标列表达式> [, … n]
FROM <表1> FULL [OUTER] JOIN <表2>[, … n]
ON <连接条件表达式>

```

## 交叉连接

```mssql
交叉连接（cross join）也称为笛卡尔积，它是在没有连接条件下的两个表的连接，包含了所连接的两个表中所有元组的全部组合。
该连接方式在实际应用中是很少的。语句格式如下：
  SELECT <目标列表达式> [,1 …n]
  FROM <表1> CROSS JOIN <表2>[,1 …n]
```

## 子查询

```
子查询（subquery）是指在一个SELECT查询语句中包含另一个SELECT查询语句，即一个SELECT语句嵌入到另一个SELECT语句中。其中，外层的SELECT语句称为父查询或外查询，嵌入内层的SELECT语句称为子查询或内查询。因此，子查询也称为嵌套查询（nested query）
```

## 无关子查询

```mssql
无关子查询的执行不依赖于父查询。它执行的过程是：首先执行子查询语句，得到的子查询结果集传递给父查询语句使用。无关子查询中对父查询没有任何引用
```

```mssql
例  查询选修了“C3”号课程的学生的姓名和所在专业。
GO
SELECT SNAME,SDEPT 
FROM S
WHERE SNO IN
     (SELECT SNO
      FROM SC
      WHERE CNO='C3')
GO 
注意：子查询的SELECT语句不能使用ORDER BY子句，ORDER BY子句只能对最终查询结果排序。

例6.41 查询其它系中比计算机科学系（CS）某一学生年龄小的学生姓名和年龄。
    GO
    SELECT SNAME,AGE
    FROM S
    WHERE AGE<ANY(SELECT AGE
                     FROM S
                     WHERE SDEPT='CS')
              AND Sdept<>‘CS’      --注意这是父查询块中的条件
    GO
    SQL Server执行此查询时，首先处理子查询，找出CS系中所有学生的年龄，构成一个查询结果集合，如（21，23，22）。然后处理父查询，查找所有不是CS系且年龄小于21或23或22的学生。 
```

## 插入子查询结果 

```mssql
子查询不仅可以嵌套在SELECT语句中，也可以嵌套在INSERT语句中，用以生成要插入的批量数据。
    插入子查询结果的INSERT语句的格式为：
     INSERT  INTO <表名>[(<列名>[, … n)]<子查询> 
例  对每一个系，求学生的平均年龄，并把结果存入数据库。首先在数据库中建立一个新表，其中一列存放系名，另一列存放相应的学生平均年龄。
    USE JXGL
    GO
    CREATE TABLE DEPT_AGE( SDEPT CHAR(15), AVG_AGE REAL)
    GO
    然后对S表按系分组求平均年龄，再把系名和平均年龄存入新表中。
    USE JXGL
    GO
    INSERT  INTO DEPT_AGE(SDEPT,AVG_AGE)
    SELECT SDEPT,AVG(AGE)
    FROM S
    GROUP BY SDEPT
    GO
  
     (2) 带子查询的删除语句
子查询也可以嵌套在DELETE语句中，用以构造执行删除操作的条件。
例6.45 删除计算机科学系（CS）所有学生的选课记录。
  USE JXGL
  GO
  DELETE
  FROM SC
  WHERE 'CS'=
      (SELECT SDEPT 
        FROM S
       WHERE S.SNO=SC.SNO)
  GO
                        
    (3) 带子查询的修改语句
子查询也可以嵌套在UPDATE语句中，用以构造修改的条件。
例  将计算机科学系（CS）全体学生的成绩提高5%。 
    USE JXGL
    GO
    UPDATE SC
    SET GRADE=GRADE+GRADE*0.05
    WHERE 'CS'=
       (SELECT SDEPT
        FROM S
        WHERE S.SNO=SC.SNO)
     GO   
注意：对某个基本表中数据的增、删、改操作有可能会破坏参照完整性。 

```

## 游标

声明游标
和使用其它类型变量一样，使用一个游标之前，必须先声明它。
    DECLARE CURSOR<游标名>
    [INSENSITIVE] [SCROLL] CURSOR
    FOR <SELECT-语句>
    [FOR READ ONLY|UPDATE[OF <列名>[, … n]]]
INSENSITIVE：定义的游标所选出来的元组存放在一个临时表中（建立在tempdb数据库中），对该游标的读取操作都有临时表来应答。
 SCROLL：指定游标使用的读取选项，默认时为NEXT，其取值如下表所示。

 SCROLL的取值

| **SCROLL**选项 | **含 义**                                                    |
| -------------- | ------------------------------------------------------------ |
| **FIRST**      | **读取游标中的第一行数据。**                                 |
| **LAST**       | **读取游标中的最后一行数据。**                               |
| **PRIOR**      | **读取游标当前位置的上一行数据。**                           |
| **NEXT**       | **读取游标当前位置的下一行数据。**                           |
| **RELATIVE n** | **读取游标当前位置之前或之后的第**n行数据（n为正向前，为负向后）。 |
| **ABSULUTE n** | **读取游标中的第**n行数据。                                  |

 READ ONLY：表示定义的游标为只读游标，表明不允许使用UPDATE、DELETE语句更新游标内的数据。默认状态下游标允许更新。
UPDATE[OF<列名>[, … n]]：指定游标内可以更新的列，如果没有指定要更新的列，则表明所有列都允许更新

```mssql
例  声明一个名为S_Cursor的游标，用以读取计算机科学系（CS）的所有学生的信息。
USE JXGL
GO
DECLARE S_Cursor CURSOR 
FOR SELECT *
         FROM S
         WHERE SDEPT='CS'
    GO
```

打开游标
声明一个游标后，还必须使用OPEN语句打开游标，才能对其进行访问。语句格式如下：
    OPEN [GLOBAL] <游标名>|<游标变量名>
参数说明如下：
GLOBAL：指定游标为全局游标。
<游标名>：已声明的游标名称。如果一个全局游标与一个局部游标同名，则要使用GLOBAL表明其全局游标。
 <游标变量名>：游标变量的名称，该名称可以引用一个游标

当执行打开游标的语句时，服务器将执行声明游标时使用的SELECT语句。如果声明游标时使用了INSENSITIVE选项，则服务器会在tempdb中建立一个临时表，存放游标将要进行操作的结果集的副本。
利用OPEN语句打开游标后，游标位于查询结果集的第一个行。同时也可以使用全局变量@@cursor_rows获得最后打开的游标中符合条件的行数。

```mssql
例  打开例6.47所声明的游标。
    GO
    OPEN S_Cursor
    GO
```

读取游标
在打开游标后，就可以利用FETCH语句从查询结果集中读取数据。使用FETCH语句一次可以读取一条记录，具体语句格式如下：
FETCH [[NEXT|PRIOR|FIRST|LAST
|ABSOLUTE n|@nvar
|RELATIVE n|@nvar]
FROM]
    [GLOBAL]<游标名>|<游标变量名>
    [INTO @变量名[, … n]]
NEXT：返回结果集中当前行的下一行，并将当前行向后移一行。 
PRIOR：读取紧临当前行的前面一行，并将当前行向前移一行。

FIRST：读取结果集中的第一行并将其设为当前行。
LAST：读取结果集中的最后一行并将其设为当前行。
ABSOLUTE n|@nvar：如果n或@nvar为正数，读取从结果集头部开始的第n行，并将返回的行变为新的当前行；如果n或@nvar为负数，读取从结果集尾部之前的第n行，并将返回的行变为新的当前行；如果n或@nvar为0，则没有行返回。
RELATIVE n | @nvar：如果n或@nvar为正数，则读取当前行之后的第n行，并将返回的行变为新的当前行；如果n或@nvar为负数，则读取当前行之前的第n行，并将返回的行变为新的当前行；如果n或@nvar为0，则读取当前行。
GLOBAL：指定游标为全局游标。
INTO @变量名[, … n]：允许读取的数据存放在多个变量中。在变量行中的每个变量必须与结果集中相应的属性列对应（顺序、数据类型等）

 @@FETCH_STATUS全局变量返回上次执行FETCH命令的状态。返回值如下：
0：表示 FETCH 语句成功。
-1：表示FETCH 语句失败或此行不在结果集中。
-2：表示被读取的行不存在。

```mssql
例 从例6.47所声明的游标中读取数据。
    GO
    FETCH NEXT FROM S_Cursor
    GO

```

关闭游标
在处理完结果集中数据之后，必须关闭游标来释放结果集。可以使用CLOSE语句来关闭游标，但此语句不释放与游标有关的一切资源。语句格式如下：
CLOSE[GLOBAL]<游标名>|<游标变量名>
其中各参数意义与打开命令一致。

```mssql
例  关闭例6.47所声明的游标。
     GO
     CLOSE S_Cursor
     GO
```

释放游标
游标使用不再需要之后，要释放游标，以获取与游标有关的一切资源。语句格式如下：
DEALLOCATE[GLOBAL]<游标名>|<游标变量名>
其中各参数意义与打开命令一致。

```mssql
例  释放例6.47所声明的游标。
     GO
     DEALLOCATE S_Cursor
     GO
```

## 创建视图

```mssql
语句格式为：
     CREATE VIEW <视图名>[(<列名>[, … n ])] 
     AS 
      <SELECT查询子句> 
     [WITH CHECK OPTION] 
```

```mssql
例7.2 建立数学系（MA）学生的视图V_MA，并要求进行修改和插入操作时仍需保证该视图只有数学系的学生。 
USE JXGL
GO
CREATE VIEW V_MA
AS
SELECT SNO,SNAME,AGE
FROM S
WHERE SDEPT='MA'
WITH CHECK OPTION
GO    
由于在定义V_MA视图时加上了WITH CHECK OPTION子句，以后对该视图进行插入、修改和删除操作时，RDBMS会验证条件SDEPT=’MA’。
```

## 修改视图

```mssql
T-SQL提供了ALTER VIEW语句修改视图，语句格式如下：
     ALTER VIEW <视图名>[(<列名>[, … n ])] 
     AS 
     <SELECT查询子句> 
     [WITH CHECK OPTION]
```

```mssql
例7.5 修改例7.2中视图V_MA，并要求该视图只查询数学系（MA）的男学生。 
    USE JXGL
    GO
    ALTER VIEW V_MA
    AS
    SELECT SNO,SNAME,AGE
    FROM S
    WHERE SDEPT='MA' AND SEX='M'
    WITH CHECK OPTION
    GO
```

## 删除视图

DROP VIEW <视图名>