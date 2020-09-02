---
title: sql-server期末复习
tags: sql
---

[toc]

> **用于期末复习,切勿用于作弊!违者后果自负!!!!!!!**

# 数据处理三个阶段

- 概念模型
- 逻辑模型
- 物理模型

----

# 常见数据模型

- 层次模型
- 网状模型
- 关系模型
- 面向对象模型

-----

# R&S例题

R

| A    | B    | C    |
| ---- | ---- | ---- |
| 3    | 6    | 7    |
| 2    | 6    | 7    |
| 7    | 2    | 3    |
| 4    | 4    | 3    |

S

| A    | B    | C    |
| ---- | ---- | ---- |
| 3    | 4    | 5    |
| 7    | 2    | 3    |

计算:

----



$$
1.R\bigcup S\\
2.R-S\\
3.RXS\\
4. \prod_{2,3}(S)\\
5.\sigma_{B<'5'}(R)\\
6. R\infty_{2<2}S\\
7. R\infty S
$$

-----



解:

1.

| A    | B    | C    |
| ---- | ---- | ---- |
| 3    | 6    | 7    |
| 2    | 5    | 7    |
| 7    | 2    | 3    |
| 4    | 4    | 3    |
| 3    | 4    | 5    |

2.

| A    | B    | C    |
| ---- | ---- | ---- |
| 3    | 6    | 7    |
| 2    | 5    | 7    |
| 4    | 4    | 3    |

3.

| R.A  | R.B  | R.C  | S.A  | S.B  | S.C  |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 3    | 6    | 7    | 3    | 4    | 5    |
| 3    | 6    | 7    | 7    | 2    | 3    |
| 2    | 5    | 7    | 3    | 4    | 5    |
| 2    | 5    | 7    | 7    | 2    | 3    |
| 7    | 2    | 3    | 3    | 4    | 5    |
| 7    | 2    | 3    | 7    | 2    | 3    |
| 4    | 4    | 3    | 3    | 4    | 5    |
| 4    | 4    | 3    | 7    | 2    | 3    |

4.

| C    | B    |
| ---- | ---- |
| 5    | 4    |
| 3    | 2    |

5.

| A    | B    | C    |
| ---- | ---- | ---- |
| 7    | 2    | 3    |
| 4    | 4    | 3    |

6.

| RA   | RB   | RC   |
| ---- | ---- | ---- |
| 7    | 2    | 4    |

| SA   | SB   | SC   |
| ---- | ---- | ---- |
| 3    | 4    | 5    |

7.

| A    | B    | C    |
| ---- | ---- | ---- |
| 7    | 2    | 3    |



----

# 教学管理例题

$$
S(SNO,SNAME,AGE,SEX,COLLEGE)\\
SC(SNO,CNO,GRADE)\\
C(CNO,CNAME,CDEPT,TNAME)\\
$$

- 检索LIU老师教的课程号,课程名

$$
\pi_{CNO,CNAME}(\sigma_{TNAME='LIU'}(C))
$$

- 检索年龄大于23的男生的学号和姓名

$$
\pi_{SNO,SNAME}(\sigma_{SEX='M' and AGE >23}(S))
$$

- 检索学号为S3学生所学课程的课程名与任课老师

$$
\pi_{CNAME,TNAME}(\sigma_{SNO='S3'}(SC\infty C))
$$

- 检索至少选修LIU老师教的课程中一门课的女生姓名

$$
\pi_{SNAME}(\sigma_{TNAME='LIU' and SEX='女'}(S\infty SC \infty C))
$$

- 检索WANG同学不学的课程的课程号

$$
\pi_{CNO}(C) - \pi_{CNO}(\sigma_{SNAME='wang'}(S\infty SC))
$$

- 检索至少选修两门课程的学生号

$$
\pi_{SNO}(SC\infty SC)- \pi_{SNO}(\sigma_{1=4  and  2 = 5}(SC \infty SC))
$$

# 部门例题

关系模式R(职工编号,日期,日营业额,部门名,部门经理),规定没个职工每天只有一个营业额,每个职工只能在一个部门,每个部门只有一个经理,回答:

- 写出模式R的基本FD和候选键

$$
FD:\\
(职工编号,日期)->日营业额\\
职工编号->部门名\\
部门名->部门经理\\
候选键:(职工编号,日期)
$$



- 说明R不是2NF理由,并吧R分解为2NF模式集

$$
由候选键知:\\
(职工编号,日期)->日营业额\\
(职工编号,日期)->部门名\\
(职工编号,日期)->部门经理\\
$$

因为在(职工编号,日期)->部门名,(职工编号,日期)->部门经理 中,日期是多余属性,存在部分函数依赖,所以R不是2NF

R进行分解:
$$
R1(职工编号, 日期,日营业额)\\
R2(职工编号,部门名,部门经理)\\
$$

- 进而分级3NF

R1不存在传递依赖,所以为3NF

R2中有

职工编号->部门名,职工编号->部门经理,

又部门名->部门经理

则存在传递依赖,R2不是3NF

分解为R21(职工编号,部门名)

R22(部门名,部门经理)

# 运动例题

有关系模式R(运动员编号,比赛项目,成绩,比赛类别,比赛主管),规定:每个运动员参加一个比赛项目,只有一个成绩,每个比赛项目只属于一个比赛类别,每个比赛类别只有一个比赛主管,回答:

- 写出基本FD和候选键

$$
FD:\\
(运动员编号,比赛项目)->成绩\\

比赛项目->比赛类别\\
比赛类别->比赛主管\\
候选键:(运动员编号,比赛项目),比赛项目
$$

- R不是2NF理由,并把R分解为2NF

R有两个这样的FD：

​    （运动员编号，比赛项目）→ （比赛类别，比赛主管）

​           比赛项目 → （比赛类别，比赛主管）

可见，前一个FD是部分（局部）函数依赖，所以R不是2NF模式。

分解:

R1(比赛项目，比赛类别，比赛主管)

R2(运动员编号，比赛项目，成绩)

- 分解为3NF

R2已是3NF模式。

在R1中，存在两个FD：比赛项目 → 比赛类别

​           比赛类别 → 比赛主管

因此，“比赛项目 → 比赛主管”是一个传递依赖，R1不是3NF模式。

R1应分解为R11（比赛项目，比赛类别）

​      R12（比赛类别，比赛主管）

这样，ρ={R11，R12，R2}是一个3NF模式集。

# 系统数据库

- master
- tempdb
- model
- msdb
- resource

# 数据库文件

- 主数据文件,扩展名:.mdf
- 辅助数据文件:扩展名:.ndf
- 事务日志文件:扩展名:.ldf

# 创建数据库

```mssql
CREATE DATAVASE JXGL
ON
(
    NAME=JXGL,
    FILENAME='\D:\...\JXGL.mdf',
    SIZE=5,
    FILEGROWTH=1
)
LOG ON
(
    NAME = JXGL_LOG,
    FILENAME='D:\..\JXGL_LOG.ldf',
    SIZE=2,
    MAXSIZE=20,
    FILEGROWTH=10%
)
```

# 完整性约束

- 实体完整性

> 每一行看做一个实体,满足唯一性

- 域完整性

> 指定列数据有正确的数据类型,格式,范围

- 参照完整性

> 维持参照表和被参照表之间的数据一致性

# 创建数据库&表例子

```mssql
　USE JXGL
　CREATE TABLE S
　(
     SNO NCHAR(9) NOT NULL,
     	CONSTRAINT PK_SNO PRIMARY KEY CLUSTERED
     	CHECK(SNO LIKE '201705121[0-9][0-9]'),
     SNAME NCHAR(8) NOT NULL,
     SEX NCHAR(2) NULL,
     BIRTHDATE DATE NULL,
     COLLEGE NCHAR(20) NULL
 )
 USE JXGL
 CREATE TABLE C
 (
 	CNO NCHAR(4) NOT NULL,
     CNAME NCHAR(20) NOT NULL,
     DESCRIPTION TEXT NULL,
     CREDIT REAL NULL,
     C_COLLEGE NCHAR(20)
     PRIMARY KEY(CNO)
 )
 USE JXGL
 CREATE TABLE SC
 (
     SNO CHAR(9) NOT NULL,
     CNO CHAR(4) NOT NULL,
     GRADE REAL NULL,
     PRIMARY KEY(SNO,CNO),
     FOREIGN KEY(SNO) REFERENCES S(SNO),
     FOREIGN KEY(CNO) REFERENCES C(CNO)
 )
```

# 数据库维护

----



参看此篇[文章](/_posts/2020-05-12-sql-server常用命令总结)



---



# 维护例子

对Ｓ，ＳＣ，Ｃ的查询

- 统计所有学生选修的课程门数

```mssql
USE JXGL
GO
SELECT COUNT(DISTINCT CNO)
FROM SC
GO
```

- 求选修Ｃ４号课程的学生的平均年龄

```mssql
USE JXGL
GO
SELECT AVG(AGE)
FROM S JOIN SC ON S.SNO=SC.SNO AND CNO='C4'
GO
```

- 求学习计算机学院（ＣＳ）每门课程的学生平均成绩

```mssql
SELECT SC.CNO,AVG(GARDE) 
 FROM SC JOIN C ON 
 SC.CNO = C.CNO and cddept = 'CS'
 GROUP BY SC.CNO
 GO
```

- 统计每门课程的学生选修人数（超过１０人的课程才统计）按人数降序排列，若人数相同，按课程号升序排列

```mssql
USE JXGL
GO
SELECT CNO,COUNT(SNO)
FROM SC
GROUP BY CNO HAVING COUNT(*)>10
ORDER BY 2 DESC,1
GO
```

- 查询姓＂王＂的所有学生的姓名和年龄

```mssql
 USE JXGL
GO
SELECT SNAME,AGE
FROM S
WHERE SNAME LIKE '王%'
GO
```

- 在ＳＣ中查询成绩为空值的学生学号和课程号

```mssql
 USE JXGL
GO
SELECT SNO,CNO
FROM SC
WHERE GRADE IS NULL
GO
```

- 查询１９９７年９月１日前出生的信息学院和数学学院女生的信息

```mssql
select * from S join SC on S.SNO = SC.SNO join C on SC.CNO = C.CNO
where brith < '1997-09-01' and (CDEPT = "信息学院" orCDEPT = "数学学院" )
```

-----

- 在基本表Ｓ中检索每一门课程成绩都大于８０分的学生学号，姓名，性别，并存储在一个`已经存在`的基本表ＳＴＵＤＥＮＴ（ＳＮＯ，ＳＮＡＭＥ，ＳＥＸ）

```mssql
建表：
USE JXGL
GO
CREATE TABLE STUDENT(SNO CHAR(9) NOT NULL,
SNAME CHAR(8) NOT NULL,
SEX CHAR(2))
GO
查询结果插入：
USE JXGL
GO
INSERT INTO STUDENT(SNO,SNAME,SEX)
SELECT SNO,SNAME,SEX
FROM S
WHERE SNO IN (SELECT SNO
              FROM SC
              GROUP BY SNO HAVING MIN(GRADE)>80)
GO
```

- 在ＳＣ中删除无成绩的学科元组

```mssql
USE JXGL
GO
DELETE FROM SC
        WHERE GRADE IS NULL
GO
```

- 把＂张成民＂同学在ＳＣ中的选课记录全部删除

```mssql
USE JXGL
GO
DELETE 
        FROM SC
        WHERE SNO IN(SELECT SNO 
                FROM S 
                WHERE SNAME='张成民')
GO
```

- 把选修“高等数学”课程中不及格的成绩全部改为空值

```mssql
USE JXGL
GO
UPDATE SC
          SET GRADE=NULL
          WHERE GRADE<60 AND CNO IN(SELECT CNO
                                FROM C
                                WHERE CNAME='高等数学')

GO
```

- 把低于总平均成绩的女同学成绩提高５％

```mssql
USE JXGL
GO
UPDATE SC
        SET GRADE=GRADE*1.05
        WHERE SNO IN(SELECT SNO
                FROM S 
                WHERE SEX='F')
                    AND GRADE<(SELECT AVG(GRADE)
                                 FROM SC)
GO
```

# 存储过程

在教学管理数据库中，创建一个名为ＳＴＵ＿ＡＧＥ的存储过程，根据输入的学号，输出该学生的出生年份

```mssql
USE JXGL
IF EXISTS(SELECT * FROM  SYSOBJECTS WHERE NAME='STU_AGE' AND TYPE='P')
DROP PROCEDURE STU_AGE
GO
CREATE PROCEDURE STU_AGE
      @S_NAME CHAR(8)
AS
      SELECT YEAR(GETDATE()-AGE) AS 'YEAR'
      FROM S 
      WHERE SNAME=@S_NAME
GO
```



创建ＧＲＡＤＥ＿ＩＮＦＯ存储过程，功能是查询某门课程所有学生成绩，显示字段ＣＮＡＭＥ，ＳＮＯ，ＳＮＡＭＥ，ＧＲＡＤＥ

```mssql
USE JXGL
IF EXISTS(SELECT * FROM  SYSOBJECTS WHERE NAME='GARDE_INFO' AND TYPE='P')
DROP PROCEDURE GARDE_INFO
GO
CREATE PROCEDURE GRADE_INFO
        @C_NAME VARCHAR(50)
AS
        SELECT CNAME,SC.SNO,SNAME,GRADE
        FROM S JOIN SC ON S.SNO=SC.SNO JOIN C ON SC.CNO=C.CNO AND CNAME=@C_NAME     
GO
```

# 触发器

创建触发器ＴＲ＿Ｃ＿ＩＮＳＥＲＴ，在Ｃ表中插入一条新元组，触发器触发，给出＂你插入了一门新课程!＂信息

```mssql
USE JXGL
CREATE TRIGGER TR_C_INSERT
ON C
       FOR
          INSERT
AS 
        PRINT '你插入了一门新的课程！'
GO
```



ＡＦＴＥＲ触发器，在ＳＣ表创建一个插入，更新类型的触发器ＴＲ＿ＧＲＡＤＥ＿ＣＨＥＣＫ，当ＧＲＡＤＥ字段中插入或修改成绩后触发，检查是否在０～１００之间

```mssql
USE JAGL
CREATE TRIGGER TR_GRADE_CHECK
ON SC
       FOR
          INSERT，UPDATE
AS 
  DECLARE @SC_grede tinyint
SELECT @SC_grade=SC.grade
FROM SC
IF (@SC_grade NOT BETWEEN 0 AND 100)
          PRINT '你插入的成绩不在0~100之间！'
GO
```



# 用户自定义函数

创建函数Ｃ＿ＭＡＸ，根据输入的课程名称，输出该门课程最高分数的同学学号

```mssql
USE JXGL
IF EXISTS(SELECT * FROM SYSOBJECTS WHERE NAME = 'C_MAX' AND TYPE = FN)
DROP FUNCTION dbo.C_MAX
GO
CREATE FUNCTION C_MAX
(@C_NAME CHAR(8))
      RETURNS CHAR(20)
     AS
BEGIN
          DECLARE @S_MAX REAL
          SELECT @S_MAX=MAX(GRADE) 
          FROM SC JOIN C ON SC.CNO=C.CNO AND C.CNAME=@C_NAME
          RETURN (SELECT SNO FROM SC WHERE GARDE= @S_MAX)
END
GO
```

创建ＳＮＯ＿ＩＮＦＯ函数，根据输入的课程名称，输出选修该门课程的学生学号，姓名，性别，系部，成绩

```mssql
USE JXGL
IF EXISTS(SELECT * FROM SYSOBJECTS WHERE NAME = 'SNO_INFO' AND TYPE = IF)
DROP FUNCTION dbo.SNO_INFO
CREATE FUNCTION SNO_INFO
(@C_NAME CHAR(8))
 RETURNS TABLE
     AS
     RETURN(SELECT S.SNO,SNAME,SEX,SDEPT,GRADE
          FROM S JOIN SC ON S.SNO=SC.SNO JOIN C ON SC.CNO=C.CNO AND C.CNAME=@C_NAME)          
GO
```



# 混合验证方式

- ＳＱＬ　ｓｅｒｖｅｒ
- Ｗｉｎｄｏｗｓ

# 备份

## 完整备份

备份整个数据库所有内容,包括事务日志,在还原时,只需还原一个备份文件即可

```mssql
USE master
GO
BACKUP DATABASE JXGL
TO DISK='D:\...path...\JXGL_BACKUP.bak'
```

## 差异备份

是对完整备份的补充,只是备份上次完整备份后更改的数据,量更小,速度更快

数据还原时,需要先还原前一次完整备份数据,然后再还原差异备份的数据

```mssql
BACKUP DATABASE <数据库名字>
TO DISK|TAPE=<物理文件名>
WITH DIFFERENTIAL
;--------例子
USE master
GO
BACKUP DATABASE JAGL
TO DISK='D:\...\FIRSTBACKUP'
WITH DIFFERENTIAL,
NOINIT
```

## 事务日志备份

事务备份只备份事务日志中的内容.事务日志记录了上一次完整备份或事务日志备份后数据库所有变动过程,所以在事务备份前,一定要先做完整备份.

还原时,除了要还原上一次完整备份内容,还要依次还原每个事务日志备份,而不是最后一个事务日志备份

```mssql
BACKUP LOG <数据库名>
TO DISK|TYPE = <NAME>
WITH DIFFERENTIAL
;----------------
;例子:事务日志备份,且追加到现有备份firstbackup
USE master
GO
BACKUP LOG JXGL
TO DISK='....\firstbackup'
WITH NOINIT
```

> 例题
>
> 备份JXGL和还原JXGL全过程
>
> ```mssql
> ;创建my_disk备份设备
> USE master
> GO
> EXEC  sp_addumpdevice 'disk','my_disk','D:\....\Dump.bak'
> ```
>
> ```mssql
> ;备份
> USE master
> GO
> BACKUP DATABASE JXGL
> TO DISK='d:\...\Dump2.bak'
> BACKUP LOG JXGL TO DISK='D:\...\Dump2.bak' WITH NORECOVERY
> ```
>
> ```mssql
> ;还原
> 
> USE master
> GO
> RESTORE DATABASE JXGL
> FROM DISK='D:\....\Dump2.bak'
> ```

# 还原模式

## 完全还原

选择完全还原常使用如下备份策略:

1. 完整数据库备份
2. 差异备份
3. 事务日志备份

## 大容量日志还原

是对完全还原模式的补充,也就是对大容量操作进行最小日志记录

## 简单还原

指在还原时,仅仅使用了数据库完整备份或差异备份,而不涉及事务日志备份