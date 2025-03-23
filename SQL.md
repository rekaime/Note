# SQL 基础

## 数据类型

### 数值

| type           | size    | SIGNED                                                | UNSIGNED                                                  | 描述                 |
| -------------- | ------- | ----------------------------------------------------- | --------------------------------------------------------- | -------------------- |
| TINYINT        | 1 byte  | (-128, 127)                                           | (0, 255)                                                  | 小整数值             |
| SMALLINT       | 2 bytes | (-32768, 32767)                                       | (0, 65535)                                                | 大整数值             |
| MEDIUMINT      | 3 bytes | (-8388608, 8388607)                                   | (0, 16777215)                                             | 大整数值             |
| INT \| INTEGER | 4 bytes | (-2147483648, 2147483647)                             | (0, 4294967295)                                           | 大整数值             |
| BIGINT         | 8 bytes | (-2 ^ 63, 2 ^ 63 - 1)                                 | (0, 2 ^ 64 - 1)                                           | 极大整数值           |
| FLOAT          | 4 bytes | (-3.402823466 E+38, 3.402823466351 E+38)              | 0 && (1.175494351 E-38, 3.402823466 E+38)                 | 单精度浮点数         |
| DOUBLE         | 8 bytes | (-1.7976931348623157 E+308, 1.7976931348623157 E+308) | 0 && (2.2250738585072014 E-308, 1.7976931348623157 E+308) | 双精度浮点数         |
| DECIMAL        |         | 依赖于M（精度）和D（标度）的值                        | 依赖于M（精度）和D（标度）的值                            | 小数值（精确定点数） |

### 字符串

| type       | size                 | 描述                         |
| ---------- | -------------------- | ---------------------------- |
| CHAR       | 0 - 255 bytes        | 定长字符串                   |
| VARCHAR    | 0 - 65535 bytes      | 变长字符串                   |
| TINYBLOB   | 0 - 255 bytes        | 不超过255个字符的二进制数据  |
| TINYTEXT   | 0 - 255 bytes        | 短文本字符串                 |
| BLOB       | 0 - 65535 bytes      | 二进制形式的长文本数据       |
| TEXT       | 0 - 65535 bytes      | 长文本数据                   |
| MEDIUMBLOB | 0 - 16777215 bytes   | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0 - 16777215 bytes   | 中等长度文本数据             |
| LONGBLOB   | 0 - 4294967295 bytes | 二进制形式的极大文本数据     |
| LONGTEXT   | 0 - 4294967295 bytes | 极大文本数据                 |

### 日期

| type      | size | scope                                      | format              | 描述                     |
| --------- | ---- | ------------------------------------------ | ------------------- | ------------------------ |
| DATE      | 3    | 1000-01-01 至 9999-12-31                   | YYYY-MM-DD          | 日期值                   |
| TIME      | 3    | -838:59:59 至 838:59:59                    | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1    | 1901 至 2155                               | YYYY                | 年份值                   |
| DATETIME  | 8    | 1000-01-01 00:00:00 至 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值         |
| TIMESTAMP | 4    | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |



## 基本语句

### DDL

Data Definition Language 数据定义语言

#### 数据库操作

**查询**

```sql
# 查询所有数据库
SHOW DATABASES;
# 查询当前数据库
SELECT DATABASE();
```

**创建**

```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];

# 例如
create database mytable default charset utf8mb4;
```

**删除**

```sql
DROP DATABASE [IF EXISTS] 数据库名;
```

**使用**

```sql
USE 数据库名;
```



#### 表操作

**查询**

```sql
# 查询当前数据库所有表
SHOW TABLES;

# 查询表结构
DESC 表名;

# 查询指定表的建表语句
SHOW CREATE TABLE 表名;
```

**创建**

```sql
CREATE TABLE 表名(
    字段1 字段1类型 [COMMENT 字段1注释],
    字段2 字段2类型 [COMMENT 字段2注释],
    字段3 字段3类型 [COMMENT 字段3注释],
    # ……
    字段n 字段n类型 [COMMENT 字段n注释]
) [COMMENT 表注释];
```

**修改**

```sql
# 添加字段
ALTER TABLE 表名 ADD 字段名 字段类型(长度) [COMMENT 注释] [约束]
# 修改数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);
# 修改字段名和字段类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 字段类型(长度) [COMMENT 注释] [约束]
# 删除字段
ALTER TABLE 表名 DROP 字段名;
# 修改表名
ALTER TABLE 表名 RENAME TO 新表名;
ALTER TABLE 表名 RENAME 新表名;
```

**删除**

```sql
# 删除表
DROP TABLE [IF EXISTS] 表名;
# 重建表
TRUNCATE TABLE 表名;
```



### DML

Data Manipulation Language 数据操作语言

#### 添加数据

```sql
# 给指定字段添加数据
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
# 给全部字段添加数据
INSERT INTO 表名 VALUES (值1, 值2, ...);
# 批量添加数据
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), ...;
INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), ...;

# 字符串和日期型数据应该包含在引号中
```

#### 修改数据

```sql
UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, ... [WHERE 条件];
# 若没有条件，则会修改整张表的所有数据
```

#### 删除数据

```sql
DELETE FROM 表名 [WHERE 条件];
# 若没有条件，则会删除整张表的所有数据
# 不能单独删除某个字段的值，如有需求可以用 UPDATE 语句
```



### DQL

Data Query Language 数据查询语言

#### 基本语法

```sql
SELECT 字段列表
FROM 表名列表
WHERE 条件列表
GROUP BY 分组字段列表
HAVING 分组后条件列表
ORDER BY 排序字段列表
LIMIT 分页参数;
```

#### 基本查询

```sql
# 查询多个字段
SELECT 字段1, 字段2, ... FROM 表名;
SELECT * FROM 表名;
# 设置别名
SELECT 字段1[ AS] 别名1, 字段2[ AS] 别名2, ... FROM 表名;
# 去重
SELECT DISTINCT 字段列表 FROM 表名;
```

#### 条件查询

```sql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

| 比较运算符         | 功能                                           |
| ------------------ | ---------------------------------------------- |
| >                  | 大于                                           |
| >=                 | 大于等于                                       |
| <                  | 小于                                           |
| <=                 | 小于等于                                       |
| =                  | 等于                                           |
| <> 或 !=           | 不等于                                         |
| BETWEEN ... AND... | 在某个范围内（含最小、最大值）                 |
| IN(...)            | 在in之后的列表中的值，多选一                   |
| LIKE 占位符        | 模糊匹配（'_'匹配单个字符，'%'匹配任意个字符） |
| IS NULL            | 是NULL                                         |

| 逻辑运算符 | 功能     |
| ---------- | -------- |
| AND 或 &&  | 并且     |
| OR 或 \|\| | 或者     |
| NOT 或 !   | 非，不是 |

#### 聚合函数

```sql
# 将一列数据作为一个整体进行纵向计算
# null 值不参与所有聚合运算
SELECT 聚合函数(字段列表) FROM 表名;
```

| 函数  | 功能     |
| :---: | -------- |
| count | 统计数量 |
|  max  | 最大值   |
|  min  | 最小值   |
|  avg  | 平均值   |
|  sum  | 求和     |

#### 分组查询

```sql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件]

# 例如
# 查询表中每个性别对应的人数
select gender, count(*) from mytable group by gender;
# 查询表中年龄小于45的员工，根据工作地址进行分组，然后获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3;
```

`where`和`having`的区别：

+  执行时机：`where`是在分组之前过滤，不满足`where`条件则不参与分组；而`having`是分组之后对结果进行过滤
+ 判断时机：`where`不能对聚合函数进行判断，而`having`可以

执行顺序上：`where`>聚合函数>`having`

分组后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 排序查询

```sql
# 排序方式默认为 ASC（升序），可填 ASC、DESC
# 若进行多字段排序，只有当前一个字段值相同时，才会根据第二个字段进行排序
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2, ...;
```

#### 分页查询

```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;
```

+ 起始索引从**0**开始，起始索引 = (查询页码 - 1) * 每页显示记录数
+ 分页查询在不同数据库有不同的实现，在 MySQL 中为 `LIMIT`
+ 如果查询的是第一页的数据，起始索引可以省略

#### 执行顺序

```sql
FROM
WHERE
GROUP BY
HAVING
SELECT
ORDER BY
LIMIT
```



### DCL

Data Control Language 数据控制语言

#### 用户管理

```sql
# 查询用户
USE mysql;
SELECT * FROM user;
```

#### 创建用户

```sql
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```

#### 修改用户密码

```sql
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';
```

#### 删除用户

```sql
DROP USER '用户名'@'主机名';
```

+ 主机名可以使用`%`通配
+ 这类语句SQL开发人员操作的比较少，主要是DBA（Database Administrator 数据库管理员）使用

#### 权限控制

|        常用权限         | 说明               |
| :---------------------: | ------------------ |
| `ALL`,` ALL PRIVILEGES` | 所有权限           |
|        `SELECT`         | 查询数据           |
|        `INSERT`         | 插入数据           |
|        `UPDATE`         | 修改数据           |
|        `DELETE`         | 删除数据           |
|         `ALTER`         | 修改表             |
|         `DROP`          | 删除数据库/表/视图 |
|        `CREATE`         | 创建数据库/表      |

#### 查询权限

```sql
SHOW GRANTS FOR '用户名'@'主机名';
```

#### 授予权限

```sql
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
```

#### 撤销权限

```sql
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

数据库名和表名可以使用`*`进行通配，表示所有



## 函数

### 字符串函数

|             函数             | 功能                                                        |
| :--------------------------: | ----------------------------------------------------------- |
|  `CONCAT(s1, s2, ..., sn)`   | 字符串拼接                                                  |
|         `LOWER(str)`         | 转小写                                                      |
|         `UPPER(str)`         | 转大写                                                      |
|     `LPAD(str, n, pad)`      | 左填充，用字符串`pad`对`str`左侧进行填充，达到`n`个字符长度 |
|     `RPAD(str, n, pad)`      | 右填充，用字符串`pad`对`str`右侧进行填充，达到`n`个字符长度 |
|         `TRIM(str)`          | 去掉字符串头尾空格                                          |
| `SUBSTRING(str, start, len)` | 返回从`start`位置起的`len`长度的子串                        |



### 数值函数

|     函数      | 功能                         |
| :-----------: | ---------------------------- |
|   `CELL(x)`   | 向上取整                     |
|  `FLOOR(x)`   | 向下取整                     |
|  `MOD(x, y)`  | `x mod y`运算                |
|   `RAND()`    | 返回 [0, 1) 内的随机数       |
| `ROUND(x, y)` | 求参数`x`保留`y`位小数的结果 |



### 日期函数

|                 函数                 | 功能                                            |
| :----------------------------------: | ----------------------------------------------- |
|             `CURDATE()`              | 返回当前日期                                    |
|             `CURTIME()`              | 返回当前时间                                    |
|               `NOW()`                | 返回当前日期和时间                              |
|             `YEAR(date)`             | 获取指定`date`的年份                            |
|            `MONTH(date)`             | 获取指定`date`的月份                            |
|             `DAY(date)`              | 获取指定`date`的日期                            |
| `DATE_ADD(date, INTERVAL expr type)` | 返回一个日期/时间值加上时间间隔`expr`后的时间值 |
|       `DATEDIFF(date1, date2)`       | 返回间隔天数（`date1 - date2`）                 |

```sql
select date_add(now(), interval 7 year);

select date_add(now(), interval 7 month);

select date_add(now(), interval 7 day);
```



### 流程函数

| 函数                                                         | 功能                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `IF(value, t, f)`                                            | 如果`value`为`true`则返回`t`，否则返回`f`                    |
| `IFNULL(value1, value2)`                                     | 如果`value`不为空则返回`value1`，否则返回`value2`            |
| `CASE WHEN [val1] THEN [res1] ... ELSE [default] END`        | 按序匹配，如果`val`为`true`则返回`res`，否则返回`default`    |
| `CASE [expr] WHEN [val1] THEN [res1] ... ELSE [default] END` | 按序匹配，如果`expr`的值等于`val`则返回`res`，否则返回`default` |



## 约束

### 概念

作用于表中字段上的规则

保证数据库数据的正确、有效、完整性

|           约束           | 描述                                                   | 关键字        |
| :----------------------: | ------------------------------------------------------ | ------------- |
|         非空约束         | 限制字段数据不能为`null`                               | `NOT NULL`    |
|         唯一约束         | 保证该字段所有数据唯一不重复                           | `UNIQUE`      |
|         主键约束         | 作为一行数据唯一标识，要求非空且唯一                   | `PRIMARY KEY` |
|         默认约束         | 保存数据时如果未指定该字段的值，则采用默认值           | `DEFAULT`     |
| 检查约束（8.2.16版本后） | 保证字段值满足某个条件                                 | `CHECK`       |
|         外键约束         | 用于两张表之间的数据建立连接，保证数据的一致性和完整性 | `FOREIGN KEY` |



**例如**

```
字段名		字段类型	约束条件			约束关键字
id		   int			主键、自增长		PRIMARY KEY, AUTO_INCREMENT
name	   varchar(10)	 非空、唯一		  NOT NULL, UNIQUE
age		   int			大于0，小于等于120 CHECK
status	   char(1)		 默认为1			DEFAULT
gender	   char(1)		 无
```

```sql
create table user (
	id int primary key auto_increment comment '主键',
	name varchar(10) not null unique comment '姓名',
    age int check (age > 0 && age <= 120) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
) comment '用户表';
```

```sql
### 主键约束也可以这么写
create table user (
	id int auto_increment comment '主键',
	name varchar(10) not null unique comment '姓名',
    age int check (age > 0 && age <= 120) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
    primary key (id, ...)
) comment '用户表';
```





### 外键约束

当一个表的某个字段是另一个表的主键时

成该字段为外键

此时另一个表为父表、本表为子表

外键可以保证其在父表中被删除时，子表作出一定的处理


#### 添加外键

```sql
# 方法一 建表时添加
CREATE TABLE 表名 (
	字段名 数据类型,
    ...
    [CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表(主列表名) 
);

# 方法二 建表后补上
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表字段名);

# 例如
create table student (
	...
    constraint tk_courseid foreign key (courseid) references course (id)
);
```



#### 删除外键

```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```



### 删除/更新行为

|     行为      | 说明                                                         |
| :-----------: | ------------------------------------------------------------ |
|  `NO ACTION`  | 在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与`RESTRICT`一致） |
|  `RESTRICT`   | 在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与`NO ACTION`一致） |
|   `CASCADE`   | 在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则同时删除/更新外键在子表中的记录（删除指的是把一整行删了，不是把该字段下的那一项删了） |
|  `SET NULL`   | 在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为`null`（要求外键允许取值为`null`） |
| `SET DEFAULT` | 父表有变更时，子表将外键设置成一个默认的值（Innodb不支持）   |

```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表字段名) ON UPDATE CASCADE DELETE CASCADE;
```



## 多表查询

### 多表关系

+ 多对一（一对多）
+ 多对多
+ 一对一

其中，一对一关系常用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率

实现：在任意一方加入外键，关联另一方的主键，且设置外键为唯一`UNIQUE`



### 多表查询概述

假设员工表`emp`记录17条数据，部门表`dept`记录6条数据

进行多表查询`select * from emp, dept`会产生 17 * 6 = 102 条数据

此时每个员工会对6个部门都进行匹配，产生一个笛卡儿积



因此应该消除无效的笛卡儿积元素

`select * from emp, dept where emp.dept_id = dept.id`



### 分类

+ 连接查询
  + 内连接：查询A、B交集部分数据
  + 外连接：
    + 左外连接：查询左表所有数据，以及两张表交集部分数据
    + 右外连接：查询右表所有数据，以及两张表交集部分数据
  + 自连接：当前表与自身的连接查询，自连接必须使用表别名
+ 子查询



### 内连接

查询语法：

+ 隐式内连接`SELECT 字段列表 FROM 表1, 表2 WHERE 条件 ...;`
+ 显式内连接`SELECT 字段列表 FROM 表1 [INNER] JOIN 表2 ON 连接条件 ...;`

​    

### 外连接

+ 左外连接：`SELECT 字段列表 FROM 表1  LEFT [OUTER] JOIN 表2 ON 条件`
+ 右外连接：`SELECT 字段列表 FROM 表1 RIGHT [OUTER] JOIN 表2 ON 条件`

一般用左外连接，因为将两个表的顺序对调即可实现两种外连接转换



### 自连接

`SELECT 字段列表 FROM 表名 别名1 JOIN 表名 别名2 ON 条件 ...`

自连接查询可以是内连接，也可以是外连接

可以将表看作是不同的两张，运用内连接或外连接语法进行查询



### 联合查询

将多次查询的结果合并起来，形成新的查询结果集

```sql
# 不写 all 则会对结果进行去重
# 两个字段数量、类型需要保持一致
SELECT 字段列表 FROM 表1 ...
UNION [ALL]
SELECT 字段列表 FROM 表2 ...;
```



### 子查询

```sql
# 嵌套 SELECT 语句称为嵌套查询，又称子查询
# 子查询外部可以是 INSERT/UPDATE/DELETE/SELECT 的任何一个
SELECT * FROM 表1 WHERE 字段1 = (SELECT 字段1 FROM 表2)
```

根据查询结果可以分类为：

+ 标量子查询：结果为单个值
+ 列子查询：结果为一列
+ 行子查询：结果为一行
+ 表子查询：结果为一个表（多行多列）

根据子查询位置可以分类为：

+ `WHERE`之后
+ `FROM`之后
+ `SELECT`之后



#### 标量子查询

```sql
# 根据销售部的部门ID查询销售部的所有员工
# (select id from dept where name = '销售部') 的结果为标量
select * from emp where dept_id = (select id from dept where name = '销售部');
```



#### 列子查询

| 常用操作符 | 作用                         |
| :--------: | ---------------------------- |
|    `IN`    | 在指定范围内                 |
|  `NOT IN`  | 不在指定范围内               |
|   `ANY`    | 满足子查询的列表中的任意一项 |
|   `SOME`   | 和`ANY`等价                  |
|   `ALL`    | 满足子查询的列表中的所有值   |

```sql
# 根据销售部和市场部的部门ID查询销售部的所有员工
# (select id from dept where name = '销售部' or '市场部') 的结果为一列
select * from emp where dept_id in (select id from dept where name = '销售部' or '市场部');

# 查询比财务部所有人工资都高的员工
select * from emp where salary > all (select salary from emp where dept = (select id from dept where name = '财务部'));
```



#### 行子查询

```sql
# 查询与员工a的薪资、领导相同的员工
# (select salary, managerid from emp where name = '员工a') 的结果为一行
select * from emp where (salary, managerid) = (select salary, managerid from emp where name = '员工a');
```



#### 表子查询

```sql
# 查询与员工a、员工b的职位、薪资相同的员工
# (select job, salary from emp where name = '员工a' or name = '员工b') 的结果为一个表
select * from emp where (job, salary) in (select job, salary from emp where name = '员工a' or name = '员工b');
```



## 事务

### 概念

**事务**是一组操作的集合，会将所有操作作为一个整体向系统提交或撤销操作请求，要么同时成功，要么同时失败



### 操作

+ 方式一

```sql
# 查看/设置事务的提交方式
SELECT @@autocommit; -- @@autocommit 1
SET @@autocommit = 0;

# 提交事务
COMMIT;

# 回滚事务
ROLLBACK;
```

+ 方式二

```sql
# 开启事务
START TRANSACTION 或 BEGIN;

# 提交事务
COMMIT;

# 回滚事务
ROLLBACK;
```



### 特性

+ 原子性 - Atomicity：事务是不可分割的最小操作单元，要么全部成功，要么全部失败
+ 一致性 - Consistency：事务完成时，必须使所有数据都保持一致
+ 隔离性 - Isolation：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
+ 持久性 - Durability：事务一旦提交或回滚，它对数据库中的数据改变就是永久的



### 并发事务问题

|    问题    | 描述                                                         |
| :--------: | ------------------------------------------------------------ |
|    脏读    | 一个事务读到另一个事务还没有提交的数据                       |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同             |
|    幻读    | 一个事物按条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在 |

#### 脏读

事务A正在对数据进行`select`和`update`，但还没进行提交时，事务B又对数据进行`select`，此时会读取到事务A`update`后的数据，然而实际上数据并没有提交

#### 不可重复读

事务A两次`select`的间隔之间，事务B`update`并`commit`了一组新数据，造成事务A两次读取的内容不一致

#### 幻读

事务A`select`时数据不存在，而后事务B执行`insert`，后续事务A`insert`数据时会出现冲突



### 事务隔离级别

|        隔离级别         | 脏读 | 不可重复读 | 幻读 |
| :---------------------: | :--: | :--------: | :--: |
|   `Read uncommitted`    |  √   |     √      |  √   |
|    `Read committed`     |  ×   |     √      |  √   |
| `Repeatable Read`(默认) |  ×   |     ×      |  √   |
|     `Serializable`      |  ×   |     ×      |  ×   |

```sql
# 查询事务隔离级别
SELECT @@TRANSACTION_ISOLATION;

# 设置事务隔离级别
# 隔离级别越高 数据越安全 性能越低
SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE};
```

1. `Read uncommitted`：
2. `Read committed`：
3. `Repeatable Read`：可能出现的问题为，A、B事务对甲的数据进行自减操作，A设置了不可重复读隔离，B执行完操作未提交时，A将收到阻塞，B提交后，A的执行语句会基于B的执行结果进行再一次自减，在A的视角中，这条数据突然减少了很多。



# SQL 进阶

## 存储引擎

### 概念

存储引擎是存储数据、建立索引、更新/查询数据等技术的实现方式

存储引擎是基于表的，而非基于库的，因此存储引擎也可被称为表类型

体系结构分为：

+ 连接层
+ 服务层
+ 引擎层
+ 存储层

在表建立后，通过查询建表语句可以发现引擎的默认值为`InnoDB`：

```sql
show create table emp;

# CREATE TABLE `emp` (
#   `id` int DEFAULT NULL COMMENT '编号',
#   `workerID` varchar(10) DEFAULT NULL COMMENT '工号',
#   `name` varchar(20) DEFAULT NULL,
#   `gender` char(1) DEFAULT NULL COMMENT '性别',
#   `age` int unsigned DEFAULT NULL,
#   `idcard` char(18) DEFAULT NULL COMMENT '身份证号',
#   `entrydate` date DEFAULT NULL COMMENT '入职时间'
# ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='员工表'
```

在创建表时，可以指定存储引擎：

```sql
# 指定存储引擎
CREATE TABLE 表名 (
	字段1 字段1类型 [COMMENT 字段1注释],
    ...
    字段n 字段n类型 [COMMENT 字段n注释],
) ENGINE = INNODB [COMMENT 表注释];

# 查询当前数据库支持的存储引擎
SHOW ENGINES;
```



### InnoDB

兼顾高可靠和高性能的通用存储引擎

* `DML`操作遵循**ACID**模型，支持事务
* 行级锁，提高并发访问性能
* 支持外键约束，保证数据的完整性、正确性

`table_name.ibd`是表空间文件，存储该表的表结构（`frm、sdi`）、数据和索引

参数`innodb_file_per_table`控制是否让每张表都有一个表空间文件

通过`show variables like 'innodb_file_per_table'`进行查询，默认`是`

在命令行中通过`ibd2sdi table_name.ibd`可以查看表空间文件内容



**InnoDB**的逻辑存储结构依次为：

+ TableSpece：表空间
+ Segment：段
+ Extent：区
+ Page：页
+ Row：行



### MyISAM

**MyISAM**是`MySQL`早期默认的存储引擎

* 不支持事务，不支持外键
* 支持表锁，不支持行级锁
* 访问速度快

通过以下文件记录表信息：

* `table_name.sdi`：存储表结构信息
* `table_name.MYD`：存储数据
* `table_name.MYI`：存储索引



### Memory

**Memory**引擎的表数据存储在内存中

受到硬件问题、断电问题的影响，表数据会丢失

因此只能作为临时表、缓存使用

特点为：内存存放、hash索引

通过以下文件记录表信息：

* `table_name.sdi`：存储表结构信息



### 总结

|     特点     |   **InnoDB**    | **MyISAM** | **Memory** |
| :----------: | :-------------: | :--------: | :--------: |
|   存储限制   |      64 TB      |     有     |     有     |
|   事务安全   |      支持       |     -      |     -      |
|    锁机制    |      行锁       |    表锁    |    表锁    |
|  B+tree索引  |      支持       |    支持    |    支持    |
|   Hash索引   |        -        |     -      |    支持    |
|   全文索引   | 支持（v 5.6后） |    支持    |     -      |
|   空间使用   |       高        |     低     |    N/A     |
|   内存使用   |       高        |     低     |     中     |
| 批量插入速度 |       低        |     高     |     高     |
|   支持外键   |      支持       |     -      |     -      |

* **InnoDB**：完整性高
* **MyISAM**：读写为主 =>被**Mongodb**替代
* **MEMORY**：访问快 =>被**Redis**替代



## 配置Linux下的MySQL

### 控制相关

```
systemctl start mysqld
systemctl restart mysqld
systemctl stop mysqld
```



### 获取初始密码

`grep 'temporary password' /var/log/mysqld.log`



### 更改密码强度

```sql
# 复杂度限制
set global validate_password.policy = 0;

# 长度限制
set global validate_password.length = 4;
```



### 修改密码

`alter user 'root'@'localhost' identified by 'weige666';`



### 创建远程连接用的用户

```sql
# 创建
create user 'root'@'%' identified with mysql_native_password by '1234';

# 分配权限
grant all on *.* to 'root'@'%';
```



### 关闭虚拟机防火墙

```
# 查看防火墙状态
firewall-cmd --state

# 关闭防火墙
systemctl stop firewalld.service

# 关闭开机自启
systemctl disable firewalld.service
```



### 查看虚拟机内 IP

`ifconfig`



### DataGrip设置

新建数据源 -> MySQL -> 主机=虚拟机内IP -> 用户=root -> 密码=密码



## 索引

### 概念

一种帮助**MySQL**高效获取数据的有序数据结构

数据库维护着满足某种特定查找算法的数据结构，以某种方式指向数据

可以基于这种数据结构实现高级查找算法



### 优缺点

**优点**

+ 提高检索效率，降低IO成本
+ 降低排序成本，降低CPU消耗

**缺点**

+ 占用磁盘空间
+ 需要耗费一定资源维护数据结构



### 索引结构

+ B+Tree索引 `[InnoDB | MyISAM | Memory]`
+ Hash索引 `[Memory]`
+ R-tree（空间索引）`[MyISAM]`
+ Full-text（全文索引）`[InnoDB v5.6 | MyISAM]`



### 索引分类

|   分类   | 含义                                                 | 特点                     |   关键字   |
| :------: | ---------------------------------------------------- | ------------------------ | :--------: |
| 主键索引 | 针对于表中逐渐创建的索引                             | 默认自动创建，只能有一个 | `PRIMARY`  |
| 唯一索引 | 避免同一个表中某数据列中的值重复                     | 可以有多个               |  `UNIQUE`  |
| 常规索引 | 快速定位特定数据                                     | 可以有多个               |            |
| 全文索引 | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个               | `FULLTEXT` |



在**InnoDB**存储引擎中，根据索引的存储形式可以分为两种

+ 聚集索引（Clustered Index）将数据存储与索引放一块，索引结构的叶子节点保存了行数据，必须是有且仅有一个，聚集索引选取规则如下：
  + 如果存在主键，主键就是聚集索引
  + 如果不存在主键，使用第一个唯一索引作为聚集索引
  + 如果不存在主键以及合适的唯一索引，则**InnoDB**会自动生成一个`rowid`作为隐藏的聚集索引
+ 二级索引（Secondary Index）将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键，可以存在多个

通俗讲就是， 聚集索引的字段指向表的某一行，二级索引的字段指向聚集索引维护的数据



### 语法

```sql
# 创建索引 不加 unique|fulltext 代表创建常规索引
# 索引可以关联多个字段 如果只关联一个字段则称为单列索引 关联多个字段成为联合索引（组合索引）
# 索引不能修改 只能先删除后再重建
CREATE [UNIQUE | FULLTEXT] INDEX index_name ON table_name(index_col_name [ASC|DESC], ...);

# 查看索引
SHOW INDEX FROM table_name;

# 删除索引
DROP INDEX index_name ON table_name;
```



### 性能分析工具

#### 查看执行频次

用于分析增删改查的使用频率 来分析是否需要通过索引来优化查询

```sql
# 七个下划线代表七个字符
SHOW [SESSION|GLOBAL] STATUS LIKE 'Com_______';
```



#### 慢查询日志

记录所有执行时间超过指定阈值（默认10s）的所有**SQL**语句的日志

慢查询日志默认是关闭状态 需要配置`/etc/my.cnf`文件

通过 `show variables like 'show_query_log';`可以检查其是否开启

```sql
# /etc/my.cnf
# 开启 MySQL 慢查询日志
slow_query_log=1

# 设置慢查询日志阈值时间为 2s
long_query_time=2

# /var/lib/mysql/localhost-slow.log 查看慢查询日志
# 设置完慢查询日志配置后要重启数据库 systemctl restart mysqld
```



#### Profile

`show profile`可以帮助我们了解**SQL**处理事务时时间都耗费在哪些方面

```sql
# 查询当前数据库是否支持 profile 操作
SELECT @@have_profiling;

# profiling 默认是关闭的
SELECT @@profiling;

# 通过 set 语句可以在 session/global 级别开启 profiling
SET profiling = 1;
```

```sql
# 查看每条 SQL 的耗时基本情况
SHOW profiles;

# 查看指定 query_id 的 SQL 语句各个阶段的耗时情况
SHOW profile FOR QUERY query_id;

# 查看指定 query_id 的 SQL 语句 CPU 的使用情况
SHOW profile cpu FOR QUERY query_id;
```



#### explain 执行计划

获取 **MySQL** 如何执行`select`语句的信息，包括在`select`语句执行过程中表如何连接、连接的顺序等

```sql
EXPLAIN|DESC SELECT 字段列表 FROM 表名 ...;
```

+ id：`select`查询的序列号，表示查询中执行`select`子句或是操作表的顺序（id相同，执行顺序从上到下，id不同，值越大越先执行）
+ select_type：表示`select`的类型，常见取值有**SIMPLE**（简单表，不含表连接或子查询）、**PRIMARY**（主查询）、**UNION**（联合查询所连接部分）、**SUBQUERY**（子查询）
+ type：表示表连接的类型，性能由好到差依次为**NULL**、**system**、**const**、**eq_ref**、**ref**、**range**、**index**、**all**
  + NULL：`select 'A'`
  + system：访问一张系统表
  + const：通过主键或唯一索引进行访问
  + ref：使用非唯一性的索引进行查询
  + index：使用索引遍历表
  + all：遍历表
+ possible_key：显示可能应用在这张表上的索引，一个或多个
+ key：实际使用的索引，如果为**NULL**则没有使用索引
+ key_len：索引中使用的字节数，该值为索引字段最大可能长度，而非实际使用长度，在不损失精确性的前提下长度越短越好
+ rows：**MySQL**认为必须要执行查询的行数，在**InnoDB**引擎中，是估计值，不总是准确的
+ filtered：表示返回结果的行数占需读取行数的百分比，值越大越好

### 索引失效情况

#### 最左前缀法则

如果索引多列（联合索引），要遵循最左前缀法则：查询从索引的最左列开始，并且不能跳过索引中的列，如果跳过某一列，则索引将部分失效（后面的字段索引失效）

例如一个索引关联的字段顺序是`col_1 > col_2 > col_3`，如果查询的时候`where col_1, col_3`，因为缺失了`col_2`，因此`col_3`会失效，只有`col_1`使用索引；如果查询的时候`where col_2, col_3`，因为缺失了`col_1`，因此查询不会使用索引

注意，只要索引关联的字段存在于查询语句中就能够触发索引，只和索引本身的字段顺序有关，和查询语句中字段出现的顺序无关



#### 范围查询

联合索引中出现范围查询`>,<`，则范围查询右侧的列索引失效

优化：将`>,<`改为`>=,=<`



#### 列运算失效

不要在索引列上进行运算操作，索引将会**失效**

```sql
# 通过函数进行运算 导致索引失效
explain select * from tb where substring(col, 10, 2) = '09';
```



#### 字符串不加引号失效

字符串类型字段使用时 不加引号会导致索引**失效**

```sql
# 生效
explain select * from tb where col = '9';
# 失效
explain select * from tb where col = 9;
```



#### 模糊查询失效

查询进行模糊匹配（`%`）时，如果尾部模糊匹配则**不失效**，如果头部模糊匹配则**失效**



#### `OR`连接失效

用`or`分割的条件，如果`or`一侧的条件的列有索引，另一侧没有，则涉及的索引都不会被用到



#### 数据分布影响失效

如果**MySQL**评估使用索引比全表扫描慢，则不使用索引

也就是查询的结果占数据本身的大部分时，不会使用索引



### 索引使用规则

#### SQL提示

在 **SQL** 语句中加入一些认为的提示达到优化操作的目的

例如，暗示 **SQL** 使用哪个索引

```sql
# 使用（建议）索引 use index
explain select * from tb use index(col) where col = '09';

# 忽视索引 ignore index
explain select * from tb ignore index(col) where col = '09';

# 强制使用索引 force index
explain select * from tb force index(col) where col = '09';
```



#### 覆盖索引

尽量使用覆盖索引，查询使用了索引且需要返回的列，列在索引中能够全部找到，减少`select *`的使用

因为不在索引中的字段可能会涉及回表查询



#### 前缀索引

当字段类型为字符串时，有时需要索引很长的字符串，使得索引变得很大，查询时浪费大量的磁盘和IO资源，影响查询效率

可以只将字符串的一部分前缀建立索引，这样可以大大节约索引空间，提高索引效率

```sql
# 创建前缀索引 指定前n个字符为前缀
create index idx_name on tb_name(col_name(n));
```

前缀长度可以根据索引的选择性来决定，选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高查询效率越高，唯一索引的选择性为1，性能最好

```sql
# 计算选择性
select count(distinct col_name) / count(*) from tb_name;
select count(distinct substring(col_name, 1, n)) / count(*) from tb_name;
```



#### 单列&联合索引

+ 单列索引只包含一列
+ 联合索引包含多列

在业务中如果存在多个查询条件，需要返回多个字段时，推荐使用联合索引进行覆盖



### 索引设计原则

+ 针对数据量较大且查询较频繁的表
+ 针对常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引
+ 选择区分度高的列作为索引，尽量建立唯一索引
+ 对于字符串可以针对地设计前缀索引
+ 尽量使用联合索引进行索引覆盖
+ 控制索引的数量，并不是多多益善，因为维护索引也需要代价
+ 如果索引列不能存储`NULL`值，在建表时使用`NOT NULL`进行约束，当优化器知道每列是否包含`NULL`值时，可以更好地确定哪个索引最有效



## SQL优化

### `insert`优化

```sql
# 不建议将数据分次插入
insert into tb_name values(1);
insert into tb_name values(2);
insert into tb_name values(3);
```



#### 批量插入

```sql
insert into tb_name values(1), (2), (3);
```



#### 手动提交事务

```sql
start transaction;
insert into tb_name values(1);
insert into tb_name values(2);
insert into tb_name values(3);
commit;
```



#### 主键顺序插入

主键的顺序插入要优于乱序插入，因为乱序插入时可能会引发页分裂



#### 大批量插入数据

当数据以文件的形式被记录时，可以通过`load`指令，将文件中记录的数据载入数据库

```sql
# 文件记录数据
1, name_1, gender_1, age_1
2, name_2, gender_2, age_2
3, name_3, gender_3, age_3
...
10000, name_10000, gender_10000, age_10000
```

```sql
# 客户端连接服务端时 加上参数 --local-infile
mysql --local-infile -u root -p

# 设置全局参数 local_infile 为 1
# 开启从本地加载文件导入数据的开关
set global local_infile = 1;

# 执行 load 指令 将准备好的数据加载到表结构中
load data local infile '/file_path/file.log' into table `tb_name` fields terminated by ',' lines terminated by '\n';
```



### 主键优化

在 **InnoDB** 存储引擎中，表数据都是根据逐渐顺序组织存放的，这种存储方式的表称为索引组织表（Index organized table, IOT）



#### 页分裂

页可以为空，也可以填充一半或填满

每个页包含2-N行数据，如果插入的数据导致页发生溢出，会生成新的页，将旧的页中部分数据连同新插入数据一起放入新的页中



#### 页合并

当删除一行记录时，实际上并没有物理地删除数据，而是修改标识`flaged`为**已删除**并且令其所占用空间允许被其他记录声明使用

当标识为**已删除**的数据到达一定阈值时，会检索最靠近的页（前后的页），分析两个页是否可以进行合并以优化存储空间占用

阈值默认为**50%**，可以通过修改`MERGE_THRESHOLD`的值改变阈值大小



#### 主键设计原则

+ 在合理范围内尽量降低主键长度（因为二级索引包含主键，过长的主键会大量占用存储空间）
+ 插入数据时尽量选择顺序插入
+ 选择使用`AUTO_INCREMENT`自增主键
+ 尽量不要使用**UUID**或是其他自然主键，因为它们的增长没有规律性
+ 业务操作时，避免对逐渐的修改



### `order by`优化

+ `Using filesort`：通过表的索引或者全表扫描，读取到满足条件的数据行，然后在排序缓冲区中完成排序操作。所有不通过索引直接返回排序结果的都叫 FileSort 排序
+ `Using index`：通过有序索引顺序扫描直接返回有序数据，这种情况就叫 using index，不需要额外排序，操作效率高

```sql
# 没有创建索引时 order by 排序会采用 Using filesort
explain select id, age, phone from tb_name order by age, phone;

# 创建相应的索引
create index idx_tb_name_age_phone on tb_name(age, phone);

# 创建索引后 order by 排序会采用索引直接获取有序的数据列表
explain select id, age, phone from tb_name order by age, phone;

# 注意
# 如果采用如下方式进行排序 因为违背了最左前缀法则 因此实际上会部分采用 Using filesort
explain select id, age, phone from tb_name order by phone, age;
explain select id, age, phone from tb_name order by age, phone desc;
```



设计原则：

+ 根据排序字段建立合适索引，注意遵循最左前缀法则
+ 尽量使用覆盖索引
+ 多字段排序时，注意索引的升序与降序设定
+ 如果不可避免地出现**filesort**，在大数据量排序时，要适当增加缓冲区`sort_buffer_size`大小（默认`256k`，通过`show variables like 'sort_buffer_size'`查看）



### `group by`优化

`group by`在分组的过程中可能建立临时表`Using temporary`来帮助分组

和`order by`一样可以建立对应的索引来优化

```sql
# 补充
create index idx_user_pro_age_sta on tb_user(profession, age, status);

# 上述索引在以下查询语句中会完全生效
explain select age, count(*) from tb_user where profession = '软件工程' group by age;
```



### `limit`优化

当数据量非常大时，`limit`查询的起始索引越大，查询的数据量越多，耗时越长

```sql
# 直接通过 limit 操作会非常耗时
select * from tb_user limit 9000000, 10;

# 通过主键进行排序可以覆盖索引，避免回表，缩短时间
select id from tb_user order by id limit 9000000, 10;
select * from tb_user where id in (select id from tb_user order by id limit 9000000, 10);
# 写法二
select * from tb_user o, (select id from tb_user order by id limit 9000000, 10) t where o.id = t.id;
```



### `count`优化

**MyISAM** 引擎把一个表的总行数存储在磁盘中，执行`count(*)`时直接返回个数，效率很高

**InnoDB** 引擎执行`count(*)`时需要将数据一行行读取出来，然后累计计数，效率很低

优化：自己写一个表来进行计数

**InnoDB** 的`count`相关细节：

+ `count(主键)`：遍历整张表，将每一行的主键值取出，返回给服务层，服务层直接按行累加计数（无需判断`NULL`，因为主键不能为空）
+ `count(字段)`：
  + 没有`NOT NULL`约束：遍历整张表，将每一行的字段值取出，返回给服务层，服务层判断是否为`NULL`后再进行累加计数
  + 有`NOT NULL`约束：遍历整张表，将每一行的字段值取出，返回给服务层，服务层直接进行累加计数
+ `count(数字)`：遍历整张表，但不取值，服务层对于返回的每一行，都置入该数字，然后进行累加计数
+ `count(*)`：不会将所有的字段都取出，而是进行了优化，不取值，服务层直接按行进行累加计数

效率`count(字段) < count(主键) < count(1) ≈ count(*)`



### `update`优化

**思路**：避免将行锁升级为表锁

行锁：是针对索引加的锁，而不是针对记录。需要保证索引不失效，否则行锁还是会升级为表锁

以 a b 两个终端来模拟事务运行

```sql
# 表 course 只有 id 和 name 两个字段
# 行锁出现的情况
# a => 插入数据
begin;
update course set name = 'java' where id = 1;
# 为了避免事务的冲突 此时 id=1 的一行会被加上行锁
# b => 插入数据
begin;
update course set name = 'Kafka' where id = 4;
# 为了避免事务的冲突 此时 id=4 的一行会被加上行锁
# 这时两个事务无论是谁都可以进行事务提交
# 因为双方利用主键 id 进行筛选 表中添加的是行锁
# a, b => 事务提交
commit;
```

```sql
# 表锁出现的情况
# a => 插入数据
begin;
update course set name = 'java' where name='PHP';
# 因为 name 字段没有索引 在更新数据的时候整张表都会被上锁
# b => 插入数据
begin;
update course set name = 'Kafka2' where id = 4;
# 这时事务 b 无法提交 或者说提交事务后会被阻塞

# 解决方案添加索引
create index idx_course_name on course(name);
```



## 存储对象 - 视图

### 概念

View 是一种虚拟存在的表，视图中的数据并不在数据库中真实存在，行和列的数据来自于定义视图的查询中使用的表（基表），并且在使用视图时动态生成



### 作用

+ 简单：简化用户对数据的理解，简化操作，使用频次高的查询可以被定义为视图
+ 安全：数据库可以授权，但不能授权到特定的行列上，通过视图可以规定用户所能查询以及修改的数据范畴
+ 数据独立：试图可以帮助用户屏蔽表的真实结构变化带来的影响（如给视图的列起别名）



### 定义

#### 创建

```sql
CREATE [OR REPLACE] VIEW view_name[(col_name...)] AS SELECT ... [WITH [CASCADED|LOCAL] CHECK OPTION]
```



#### 查询

```sql
# 查看创建视图的语句
SHOW CREATE VIEW view_name;

# 查看视图数据
SELECT * FROM view_name ...;
```



#### 修改

```sql
# 方法一 重新创建
CREATE [OR REPLACE] VIEW view_name[(col_name...)] AS SELECT ... [WITH [CASCADED|LOCAL] CHECK OPTION]

# 方法二
ALTER VIEW view_name[(col_name...)] AS SELECT [WITH [CASCADED|LOCAL] CHECK OPTION]
```



#### 删除

```sql
DROP VIEW [IF EXISTS] view_name[, ...];
```



### 检查选项

使用`with check option`子句创建视图时，**MySQL** 会通过试图检查正在更改的每一行，使其符合视图的定义。**MySQL** 允许基于试图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，**MySQL** 提供了两个选项：

+ `cascaded`（默认值）：级联检查，除了检查本视图是否保持一致性，还要对上层视图进行检查
+ `local`：检查本视图是否保持一致性，上层视图如果有检查选项，才进行上层试图的检查



#### cascaded

```sql
# 无检查
create view v_emp as select id, workerID, name, gender, age from emp where id >= 2;
# 有检查 cascaded
create view v2_emp as select id, workerID, name, gender, age from v_emp where id <= 5 with cascaded check option;

# 这条语句可以执行 因为 v_emp 视图没有添加检查约束
insert into v_emp values(1, 1, 'yy', '男', 24);

# 这个时候 v2_emp 的插入会失败 因为会检查 v2_emp 的约束项 然后再检查 v_emp 的约束项
# 结果发现不满足 v_emp 视图的约束 因此插入失败
insert into v2_emp values(1, 1, 'yy', '男', 24);

# 完全基于 v2_emp 创建 v3_emp
create or replace view v3_emp as select id, workerID, name, gender, age from v2_emp with local check option;
# 这条语句虽然没有开启检查 但依然无法执行
# 因为数据是顺着 v2_emp 进行插入的 因此不满足 v2_emp 的约束项
insert into v3_emp values(8, 8, 'yy', '男', 24);
```



#### local

```sql
# 无检查
create view v_emp as select id, workerID, name, gender, age from emp where id >= 2;
# 有检查 cascaded
create view v2_emp as select id, workerID, name, gender, age from v_emp where id <= 5 with cascaded check option;

# 这条语句可以执行 因为 v_emp 视图没有添加检查约束
insert into v_emp values(1, 1, 'yy', '男', 24);

# 这条语句也可以执行 它首先通过了 v2_emp 的检查
# 因为 v_emp 无检查选项 因此不进行任何检查 可以完成插入工作
insert into v2_emp values(1, 1, 'yy', '男', 24);
```



### 视图更新相关

如果需要进行视图**插入或更新**，视图中的行与基表的行之间必须存在一对一的关系

如果视图包含以下任何一项，则视图不更新：

+ 聚合函数或窗口函数`SUM() MIN() MAX() COUNT() ...`
+ `DISTINCT`
+ `GROUP BY`
+ `HAVING`
+ `UNION [ALL]`



## 存储对象 - 存储过程

### 概念

实现经过编译并存储在数据库中的一段 **SQL** 语句的集合，调用存储过程可以简化开发人员的工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。存储过程，根本上是对 **SQL** 语言层面代码的封装和复用



### 定义

#### 创建

```sql
# 如果在命令行中进行操作 需要先修改分隔符
# delimiter //
CREATE PROCEDURE procedure_name([params...])
BEGIN
	-- SQL
END;
```



#### 调用

```sql
CALL procedure_name([params...])
```



#### 查看

```sql
# 查看指定数据库的存储过程以及状态信息
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'schema_name';

# 查询存储过程的定义
SHOW CREATE PROCEDURE procedure_name;
```



#### 删除

```sql
DROP PROCEDURE [IF EXISTS] procedure_name;
```



### 变量

#### 系统变量

系统变量是 **MySQL** 服务器提供的，不由用户定义、属于服务器层面的变量

分为全局（`GLOBAL`）变量和会话变量（`SESSION`）

```sql
# 查看系统变量
SHOW [SESSION|GLOBAL] VARIABLES [LIKE '...'];
SELECT @@[SESSION|GLOBAL].var_name;

# 设置系统变量
SET [SESSION|GLOBAL] var_name = '...';
SET @@[SESSION|GLOBAL].var_name = '...';

# 以上设定默认为 SESSION
# MySQL 服务器重启后 所有设定的全局参数都会失效
# 如果需要保存设置 可以在 /etc/my.cnf 中配置
```



#### 用户定义变量

用户根据需要自己定义的变量，无需提前声明，在使用时直接使用`@var_name`调用即可，作用域为当前会话连接

```sql
# 赋值
SET @var_name = expr [, @var_name = expr, ...];
SET @var_name := expr [, @var_name := expr, ...];

SELECT @var_name := expr [, @var_name := expr, ...];
SELECT col_name INTO @var_name FROM tb_name;

# 使用
SELECT @var_name;
```



#### 局部变量

根据需要定义的在局部生效的变量，在访问前需要用`DECLARE`声明

可以用作存储过程内的局部变量和输入参数，局部变量的范围是`BEGIN...END`块

```sql
# 声明
DECLARE var_name var_type [DEFAULT ...];

# 赋值
SET var_name = ...;
SET var_name := ...;
SELECT col_name into var_name FROM tb_name ...;
```



### if

```sql
IF expr_1 THEN
	...
ELSE IF expr_2 THEN
	...
ELSE
	...
END IF;
```



### 参数

分为三种类型：

+ `IN`（默认）：该类型参数作为输入，也就是调用时传入值
+ `OUT`：该类型参数作为输出，也就是该参数可以作为返回值
+ `INOUT`：既可以作为输入参数，也可以作为输出参数

```sql
# delimiter //
CREATE PROCEDURE procedure_name([IN|OUT|INOUT par_name par_type])
BEGIN
	-- SQL
END;
```



### case

```sql
# 语法一
CASE case_value
	WHEN when_value_1 THEN statement_1
	[WHEN when_value_2 THEN statement_2
	...
	ELSE statement_n]
END CASE;

# 语法二
CASE
	WHEN search_condition_1 THEN statement_1
	[WHEN search_condition_2 THEN statement_2
	...
	ELSE statement_n]
END CASE;
```



### while

```sql
WHILE expr DO
	-- SQL
END WHILE;
```



### repeat

```sql
# 和 while 相反
# 先执行循环体 直到满足条件则退出循环
REPEAT
	-- SQL
	UNTIL expr
END REPEAT;
```



### loop

```sql
[label:] LOOP
	-- SQL
END LOOP
```

```sql
# repeat 本身是死循环
# 可以配合以下关键字操控循环
LEAVE label; -- 退出循环
ITERATE label; -- 跳过本轮循环
```



### 游标 - cursor

用于存储查询结果集合的数据类型

在存储过程和函数中可以使用游标对结果集进行循环的处理

```sql
# 声明
DECLARE cur_name CURSOR FOR SELECT ...;

# 打开
OPEN cur_name;

# 获取游标记录
FETCH cur_name INTO var_1[, var_2, ...];

# 关闭
CLOSE cur_name;
```



### 条件处理程序

[参考网址](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html)

```sql
DECLARE handler_action HANDLER FOR condition_value[, condition_value, ...] statment;

handler_action
	CONTINUE: 继续执行程序
	EXIT: 终止执行程序
condition_value
	SQLSTATE sqlstate_value: 填写状态码 如 02000
	SQLWARNING: 指所有以 01 开头的 SQLSTATE 代码
	NOT FOUND: 指所有以 02 开头的 SQLSTATE 代码
	SQLEXCEPTION: 指所有不被 SQLWARNING 和 NOT FOUND 捕获的 SQLSTATE 代码
```

`handler_action`：

+ `CONTINUE`：继续执行程序
+ `EXIT`：终止执行程序

`condition_value`：

+ `SQLSTATE sqlstate_value`：填写状态码 如 02000
+ `SQLWARNING`：指所有以 01 开头的`SQLSTATE`代码
+ `NOT FOUND`：指所有以 02 开头的`SQLSTATE `代码
+ `SQLEXCEPTION`：指所有不被`SQLWARNING`和`NOT FOUND`捕获的`SQLSTATE`代码

```sql
# 利用条件处理来读取游标
create procedure p1()
begin
    declare uname varchar(100);
    declare ugender char(1);
    declare u_cursor cursor for select name, gender from emp;
    # 定义条件处理程序
    declare exit handler for SQLSTATE '02000' close u_cursor;

    drop table if exists tb_user_ex;
    create table tb_user_ex(
      id integer primary key auto_increment,
      name varchar(100),
      gender varchar(100)
    );

    open u_cursor;    
    while true do
    	fetch u_cursor into uname, ugender;
    	insert into tb_user_ex values (null, uname, ugender);
    end while;
end;
```



## 存储函数

具有返回值的存储过程，存储函数的参数只能是`IN`类型数据

```sql
CREATE FUNCTION func_name([params...])
RETURNS var_type [characteristic...]
BEGIN
	-- SQL
	RETURN ...;
END;
```

`characteristic`：

+ `DETERMINISTIC`：相同的输入参数总是产生相同的结果
+ `NOT SQL`：不包含 **SQL** 语句
+ `READS SQL DATA`：包含读取数据的语句，但不包含写入数据的语句



## 存储对象 - 触发器

### 概念

触发器是与表有关的数据库对象，指在`insert`/`update`/`delete`的前后，触发并执行触发器中定义的 **SQL** 语句集合

触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作

使用别名`OLD`和`NEW`来引用触发器中发生变化的记录内容，与其他数据库是相似的，现在的触发器只支持行级触发，不支持语句级触发

（行级触发器：触发器执行次数，取决于一条语句影响的行数；语句级触发器：无论一条语句影响多少行，触发器都只执行一次）

+ `insert`型触发器：`new`表示将要或者已经新增的数据
+ `update`型触发器：`old`表示修改之前的数据，`new`表示将要或者已经修改后的数据
+ `delete`型触发器：`old`表示将要或者已经删除的数据



### 定义

#### 创建

```sql
# 创建
# DELIMITER //
CREATE TRIGGER trigger_name
BEFORE|AFTER INSERT|UPDATE|DELETE
ON tb_name FOR EACH ROW -- 行级触发器
BEGIN
	-- SQL
END; -- END//
```

#### 查看

```sql
SHOW TRIGGERS;
```

#### 删除

```sql
DROP TRIGGER trigger_name;
```



## 锁

### 概念

锁是计算机协调多个进程或线程并发访问某一临界资源的机制

在数据库中，除了传统计算资源（CPU、RAM、I/O）的竞争外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤为重要，也更为复杂



### 分类

+ 全局锁：锁定数据库中的所有表
+ 表级锁：每次操作锁住整张表
+ 行级锁：每次操作锁住对应的行数据



### 全局锁

对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的 **DML** 的写语句，**DDL** 语句，以及更新操作的事务提交语句都将被阻塞

使用场景大多表现在对全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性

施加全局锁后，数据库内的表可读不可写：

+ DML（阻塞）
+ DDL（阻塞）
+ DQL



#### 使用

```sql
# 加全局锁
flush tables with read lock;

# 备份 (在 OS cmd 中运行)
mysqldump [-h(IP)] -uroot -p1234 itcast > itcast.sql

# 解锁
unlock tables;
```



#### 特点

+ 如果在主库上备份，在备份期间不能执行更新，业务停摆
+ 如果在从库上备份，在备份期间从库不能执行主库同步过来的二进制日志，会导致主从延迟

在 **InnoDB** 引擎中，可以在备份时加上`--single-transaction`参数来完成不加锁的一致性数据备份

`mysqldump --single-transaction -uroot -p1234 itcast > itcast.sql`



### 表级锁

#### 介绍

每次操作时锁住整张表

锁定粒度大（粒度：锁的作用范围），发生锁冲突的概率最高，并发度最低



#### 分类

+ 表锁
+ 元数据锁（meta data lock MDL）
+ 意向锁



#### 表锁

分为两类：

+ 表共享读锁（read lock）
  + 本用户
    + DDL（阻塞）
    + DML（阻塞）
    + DQL
  + 其他用户
    + DDL（阻塞）
    + DML（阻塞）
    + DQL
+ 表独占写锁（write lock）
  + 本用户
    + DDL
    + DML
    + DQL
  + 其他用户
    + DDL（阻塞）
    + DML（阻塞）
    + DQL（阻塞）

```sql
# 加锁
lock tables tb_name... read|write;

# 释放锁 或者断开客户端连接
unlock tables;
```



#### 元数据锁

**MDL** 加锁的过程是系统自动控制的，无需显示使用，在访问一张表的时候会自动加上

**MDL** 的主要作用是维护表元数据的一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。为了避免 **DML** 与 **DDL** 冲突，保证读写的正确性

在 **MySQL** 5.5 中引入了 **MDL**，当一张表正进行增删改查时，添加 **MDL** 读锁（共享）；当表结构发生变更时，添加 在 **MySQL** 5.5 中引入了 **MDL**，当一张表正进行增删改查时，添加 **MDL** 写锁（排他）

| SQL                                                   | 对应锁类型                                  | 说明                                                         |
| ----------------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------ |
| `lock table tb_name read|write`                       | **SHARED_READ_ONLY / SHARED_NO_READ_WRITE** |                                                              |
| `select`、`select ... lock in share mode`             | **SHARED_READ**                             | 与 **SHARED_READ**、**SHARED_WRITE** 兼容，与 **EXCLUSIVE** 互斥 |
| `insert`、`update`、`delete`、`select ... for update` | **SHARED_WRITE**                            | 与 **SHARED_READ**、**SHARED_WRITE** 兼容，与 **EXCLUSIVE** 互斥 |
| `alter table ...`                                     | **EXCLUSIVE**                               | 与其他的 **MDL** 都互斥                                      |

查看元数据锁可以通过如下语句：

```sql
select object_type, object_schema, object_name, lock_type, lock_duration from performance_schema.metadata_locks;
```



#### 意向锁

在施加表锁前，需要检查一张表是否被了行锁

为了避免 **DML** 在执行时，加的行锁与表锁存在冲突，在 **InnoDB** 中引入了意向锁，使表锁无需检查每行数据是否加锁，使用意向锁可以减少表锁检查

分类：

+ 意向共享锁（IS）：由语句 `select ... lock in share mode`添加，与表锁共享锁（read）兼容，与表锁排他锁（write）互斥
+ 意向排他锁（IX）：由`insert`、`update`、`delete`、`select ... for update`添加，与表锁共享锁（read）以及表锁排他锁（write）都互斥，意向锁之间不会互斥

查询意向锁和行锁的加锁情况：

```sql
select object_schema, object_name, index_name, lock_type, lock_mode, lock_data from performance_schema.data_locks;
```



### 行级锁

#### 介绍

每次操作锁住对应的行数据，粒度最小，发生锁冲突的概率最低，并发度最高，应用在 **InnoDB** 引擎中

**InnoDB** 的数据是基于索引组织的，行锁是通过对索引上的索引项加锁实现的，而不是对记录加锁



#### 分类

+ 行锁（Record Lock）：锁定单个行对应的数据，防止其他事务对本行进行 `update`、`delete`，在 **RC** 和 **RR** 隔离级别下都支持
+ 间隙锁（Gap Lock）：锁定索引之间的间隙（不包含数据本身），确保索引记录间隙不变，防止其他事务在这个间隙进行`insert`，产生幻读，在 **RR** 隔离级别下都支持
+ 临键锁（Next-Key Lock）：行锁和间隙锁的组合，同时锁住数据和数据前面的间隙，在 **RR** 隔离级别下支持



### 行锁

分为两类：

+ 共享锁（S）：允许一个事务读取一行，阻止其他事务获得相同数据集的排他锁
+ 排他锁（X）：允许获取排他锁的事务更新数据，组织其他事务获得相同数据集的共享锁和排他锁

| SQL                             | 行锁类型         |
| ------------------------------- | ---------------- |
| `INSERT UPDATE DELETE`          | 自动加锁：排他锁 |
| `SELECT`                        | 不加任何锁       |
| `SELECT ... LOCK IN SHARE MODE` | 共享锁           |
| `SELECT ... FOR UPDATE`         | 排他锁           |

查询意向锁和行锁的加锁情况：

```sql
select object_schema, object_name, index_name, lock_type, lock_mode, lock_data from performance_schema.data_locks;
```

在默认情况下，**InnoDB** 在`REPEATABLE READ`隔离级别运行，**InnoDB** 使用`next-key`锁进行搜索和索引扫描，防止幻读

对于行锁：

+ 针对唯一索引进行检索时，对于已存在的记录进行等值匹配时，将自动优化为行锁
+ **InnoDB** 的行锁是针对于索引加的锁，如果不通过索引条件检索数据，那么 **InnoDB** 将对表中所有记录加临键锁，此时行锁升级，表现得如同整张表附加上了表锁



### 间隙锁 & 临键锁

在默认情况下，**InnoDB** 在`REPEATABLE READ`隔离级别运行，**InnoDB** 使用`next-key`锁进行搜索和索引扫描，防止幻读

+ **唯一索引**上的等值查询：给不存在的记录加锁时，优化为间隙锁
+ **普通索引**上的等值查询：向右遍历时，最后一个值不满足查询需求时，这个值对应索引上的锁由`next-key lock`退化为间隙锁
+ **唯一索引**上的范围查询，会访问到不满足条件的第一个值为止

间隙锁唯一目的是防止其他事务插入间隙，间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁



## InnoDB 引擎

### 逻辑存储结构

1. 表空间（Tablespace）：存储在`.ibd`文件中，一个 **MySQL** 实例可以对应多个表空间，用于存储记录、索引等数据，`.ibd`文件存放路径为`/var/lib/mysql/`，该路径下的`/mysql.ibd`以及各个数据库`/{db_name}/{tb_name}.ibd`就是各个数据库的表空间文件
2. 段（Segment）：分为数据段（Leaf node segment）、索引段（Non-leaf node segment）、回滚段（Rollback segment），**InnoDB** 是索引组织表，数据段就是 **B+树** 的叶子节点，索引段即为 **B+树 ** 的非叶子节点，段用来管理多个区
3. 区（Extent）：表空间的单元结构，每个区的大小为 1M。默认情况下，**InnoDB** 存储引擎页大小为 16K，即一个区中一共有 64 个连续的页
4. 页（Page）：是 **InnoDB** 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为保障页的连续性，**InnoDB** 存储引擎每次从磁盘申请 4-5 个区
5. 行（Row）：**InnoDB** 存储引擎数据是按行进行存放的
   1. `Trx_id`：每次对某条记录进行改动时，都会把对应的事务`id`赋值给`trx_id`隐藏列
   2. `Roll pointer`：每次对某条引记录进行改动时，都会把旧的版本写入到`undo`日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息
   3. `column`：一般字段



### 架构

#### 内存结构

`Buffer Pool`：缓冲池是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后以一定的频率刷新到磁盘中，从而减少磁盘读写操作，加快处理速度

缓冲池以`Page`为单位，底层采用链表管理页，根据状态，页可以分为三种：

+ `free page`：空闲页，未被使用过
+ `clean page`：被使用的`page`，数据没被修改过
+ `dirty page`：脏页，被使用的`page`且数据被修改过



`Change Buffer`：更改缓冲区（针对非唯一的二级索引页），在执行`DML`语句时，如果这些数据页不存在于`Buffer Pool`中，不会直接操作磁盘，而是将数据变更存储在更改缓冲区中，在未来数据被读取时，再将数据合并恢复到`Buffer Pool`中，接着再将合并的数据刷新到磁盘中

作用在于，二级索引通常是非唯一的，这点和聚集索引不同，并且二级索引的插入顺序相对随机，因此删除和更新可能会影响索引树中不相邻的二级索引页，如果每一次更新操作都操作磁盘，会引入过多的磁盘读写，利用`Change Buffer`可以在缓冲池中进行合并处理，减少磁盘读写频率



`Adaptive Hash Index`：自适应`hash`索引，用于优化对`Buffer Pool`数据的查询，**InnoDB** 存储引擎会监控对表上各索引页的查询，如果观察到`hash`索引可以提升速度，则建立`hash`索引，称之为自适应`hash`索引

无需人工干预，是系统根据情况自动完成的，可以查询以下变量查看自适应哈希是否开启：`adaptive_hash_index`



`Log Buffer`：日志缓冲区，用来保存要写入磁盘中的日志数据（`redo log`、`undo log`），默认大小为 **16MB**，日志缓冲区的日志会定期刷新到磁盘中，如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘读写操作

可以查询以下变量查看自适应哈希是否开启：

+ `innodb_log_buffer_size`：缓冲区大小
+ `innodb_flush_log_at_trx_commit`：日志刷新到磁盘的时机
  + 1：日志在每次事务提交时写入并刷新到磁盘
  + 0：每秒将日志写入并刷新到磁盘一次
  + 2：日志在每次事务提交后写入，并每秒刷新到磁盘一次



#### 磁盘结构

`System Tablespace`：系统表空间是更改缓冲区的存储区域，如果表是在系统表空间而不是每个表文件或通用表空间中创建的，它也可能包含表和索引数据。相关变量：`innodb_data_file_path`



`file-Per-Table Tablespaces`：每个表的文件表空间包含单个 **InnoDB** 表的数据和索引，并存储在文件系统上的单个数据文件中。相关变量：`innodb_file_per_table`



`General Tablespaces`：通用表空间，需要通过`CREATE TABLESPACE`语法创建通用表空间，在创建表时，可以指定该表空间

```sql
# 创建表空间
CREATE TABLESPACE sp_name ADD DATAFILE 'file_name' ENGINE = 'engine_name';

# 建表时指定表空间
CREATE TABLE tb_name ... TABLESPACE sp_name;
```



`Undo Tablespaces`：撤销表空间，**MySQL** 实例在初始化时会自动创建两个默认的`undo` 表空间（初始大小为 **16MB**，用于存储`undo log`



`Temporary Tablespaces`：临时表空间，**InnoDB** 使用会话临时表空间和全局临时表空间，存储用户创建的临时表等数据



`Doublewrite Buffer Files`：双写缓冲区，**InnoDB** 引擎将数据页从`Buffer pool`刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据。双写缓冲区一般保存在两个文件中：`#ib_16384_0.dblwr`、`#ib_16384_1.dblwr`



`Redo Log`：重做日志，用来实现事务的持久性。该日志由两部分组成：重做日志缓冲`redo log buffer`以及重做日志文件`redo log`，前者在内存中，后者在磁盘中。当事务提交后会把所有修改信息都存储到该日志中，用于在刷新脏页到磁盘中时，如果发生错误则允许恢复数据。重做日志一般保存在两个文件中：`ib_logfile0`、`ib_logfile1`



#### 后台线程

`Master Thread`：核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中，保持数据的一致性，还包括脏页的刷新、合并插入缓存、`undo`页的回收



`IO Thread`：在 **InnoDB** 存储引擎中大量使用了`AIO`（异步读写）来处理 **IO** 请求，这样可以极大提高数据库的性能，而 **IO Thread** 主要负责这些 **IO** 请求的回调

+ `Read thread`：4个线程，负责读操作
+ `Write thread`：4个线程，负责写操作
+ `Log thread`：1个线程，负责将日志缓冲区刷新到磁盘
+ `Insert buffer thread`：1个线程，负责将写缓冲区内容刷新到磁盘



`Purge Thread`：用于回收事务已经提交的`undo log`，在事务提交后，`undo log`可能失去用途，用`Purge Thread`来回收



`Page Cleaner Thread`：协助`Master Thread`刷新脏页到磁盘的线程，减轻`Master Thread`的工作压力，减少阻塞



### 事务原理

#### 概述

+ 原子性、一致性、持久性：由`redo log`、`undo log`保证
+ 隔离性：由锁和 **MVCC** 保证



#### redo log

重做日志，记录事务提交时数据页的物理修改，实现事务的**持久性**

该日志文件由重做日志缓冲`redo log buffer`和重做日志文件`redo log file`组成，前者在内存中，后者在磁盘中

事务提交后会把所有修改信息都存储到该日志文件中，用于在刷新脏页到磁盘发生错误时进行数据恢复使用



#### undo log

回滚日志，用于记录数据被修改前的信息，作用包括提供回滚和 **MVCC**，保证事务的原子性

`undo log`和`redo log`记录的物理日志不一样，它是逻辑日志，可以认为当`delete`一条记录时，`undo log`会记录一条对应的`insert`操作，反之亦然，当`update`一条记录时，它会记录一条相反的`update`操作，当执行`rollback`时，就可以从`undo log`中的逻辑记录读取到相应的内容并进行回滚

`undo log`相关：

+ 销毁：`undo log`在事务执行时产生，事务提交时，并不会立即删除`undo log`，因为这些日志还可以用于 **MVCC**
+ 存储：`undo log`采用段式存储进行管理和记录，存放在`rollback segment`回滚段中，内部包含 1024 个`undo log segment`



#### MVCC

##### 基本概念

+ 当前读：读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行枷锁。`select ... lock in share mode`（共享锁）、`select ... for update、update、insert、delete`（排他锁）都是一种当前读
+ 快照读：读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。简单的`select`（不加锁）就是快照读：
  + `Read Committed`：每次`select`都生成一个快照读
  + `Repeatable Read`：开启事务后第一个`select`语句才是快照读的地方
  + `Serializable`：快照读会退化为当前读
+ **MVCC**（Multi-Version Concurrency Control）：多版本并发控制，维护一个数据的多个版本，使得读写操作没有冲突，快照读为 **MySQL** 实现 **MVCC** 提供了一个非阻塞读功能。**MVCC** 的具体实现还依赖于数据库记录中的三个隐式字段、`undo log`和`readView`



##### 隐式字段

每个表中都存在以下二至三个隐藏字段

+ `DB_TRX_ID`：最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务的 **ID**
+ `DB_ROLL_PTR`：回滚指针，指向这条记录的上一版本，用于配合`undo log`指向上一版本
+ `DB_ROW_ID`：隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段

对`.ibd`文件，在命令行中通过`ibd2sdi *.ibd`命令，可以查看表结构



##### undo log版本链

`insert`操作产生的`undo log`只在回滚时需要，事务提交后可以被立即删除

`update`、`delete`操作产生的`undo log`不仅在回滚时需要，在快照读时也需要，不会被立即删除

当同时存在多个事务对同一个表进行操作时，每当有一个事务执行`update`时，会将修改的逻辑记录在`undo log`中，同时修改表的`DB_TRX_ID`、`DB_ROLL_PTR`字段，用来记录事务ID和事务回滚所代表的逻辑语句在`undo log`的哪一个位置。在多个事务操作下，`undo log`的各个逻辑记录会形成链条，连接到下一个回滚语句上（通过`DB_TOLL_PTR`形成的链表）



##### readview

读视图是快照读执行时 **MVCC** 提取数据的依据，记录并维护系统当前活跃的事务 ID

核心字段包括：

+ `m_ids`：当前活跃的事务 ID 集合
+ `min_trx_id`：最小活跃事务 ID
+ `max_trx_id`：预分配事务 ID，当前最大事务 ID+1（因为事务 ID 是自增的）
+ `creator_trx_id`：`ReadView`创建者的事务 ID

在版本链数据访问规则中，读视图会链式遍历版本链，去读取第一个满足规则约束的数据版本：

+ `trx_id == creator_trx_id` => 可以访问该版本 => 数据是当前事务更改的
+ `trx_id < min_trx_id` => 可以访问该版本 => 该版本事务已提交
+ `trx_id > max_trx_id` => 不可以访问该版本 => 该版本事务在`ReadView`生成后才开启
+ `min_trx <= trx_id <= max_trx_id` => 如果`trx_id`不在`m_ids`中则可以访问该版本 => 该版本事务已提交

不同隔离级别下`ReadView`生成时机不同：

+ **Read Committed：**在事务中每次执行快照读时生成`ReadView`
+ **Repeatable Read：**仅在事务中第一次执行快照读时生成`ReadView`，后续复用该`ReadView`



### 系统数据库

**MySQL** 数据库安装完毕后会自动生成四个系统数据库：

+ `mysql`：存储 **MySQL** 服务器正常运行所需要的各种信息（时区、主从、用户、权限等）
+ `information_schema`：提供了访问数据库元数据的各种表和试图，包含数据库、表、字段类型以及访问权限等
+ `performance_schema`：为 **MySQL** 服务器运行时状态提供了底层监控系统，主要用于收集数据库服务器性能参数
+ `sys`：包含了一系列方便`DBA`和开发人员利用`performance_schema`性能数据库进行性能调优和诊断的视图



### 常用工具

#### mysql

+ 语法：`mysql [option] [database]`
+ options：
  + `-u|--user=name`：指定用户名
  + `-p|--password[=pwd]`：指定密码
  + `-h|--host=ipndomain`：指定服务器IP或域名
  + `-P|--port=port`：指定连接端口
  + `-e|--execute=statement`：执行 SQL 语句并退出

示例：`mysql -uroot -p123 db01 -e "select * from stu"`或`mysql --user=root --password=123 db01 --execute="select * from stu"`



#### mysqladmin

执行管理操作的客户端程序，可以用来检查服务器的配置和当前状态、创建或删除数据库等

通过命令行了解如何使用：`mysqladmin --help`

示例：`mysqladmin -uroot -p123 drop 'test01';`、`mysqladmin -uroot -p123 version;`



#### mysqlbinlog

由于服务器生成的二进制日志文件以二进制格式保存（`/var/lib/mysql`目录下的`binlog.000001`，后缀名会从1开始自增），如果想要检查这些文本，就需要`mysqlbinlog`

+ 语法：`mysqlbinlog [options] log-files1 log-files2...`
+ options：
  + `-d,--database=name`：指定数据库名称，只列出指定的数据库相关操作
  + `-o,--offset=#`：忽略掉日志中的前n行命令
  + `-r,--result-file=name`：将输出的文本格式日志输出到指定的文件
  + `-s,--short-form`：显示简单格式，省略掉一些信息
  + `--start-datatime=sdate --stop-datetime=edate`：指定日期间隔内的所有日志
  + `--statr-position=spos --stop-position=epos`：指定位置间隔内的所有日志
  + `-v`：将行事件（数据变更）重构为 **SQL** 语句
  + `-vv`：将行事件（数据变更）重构为 **SQL** 语句，并输出注释信息



#### mysqlshow

客户端对象查找工具，用来快速查询存在哪些数据库、库中的表、表中的列或索引

+ 语法：`mysqlshow [options] [db_name [table_name [col_name]]]`
+ options：
  + `--count`：显示数据库以及表的统计信息（库和表均可以不指定）
  + `-i`：显示指定数据库或指定表的状态信息

示例：

+ 查询每个数据库的表的数量以及表中记录的数量：`mysqlshow -uroot -p123 --count`
+ 查询`test`库中每个表中的字段数以及行数：`mysqlshow -uroot -p123 test --count`
+ 查询`test`库中`book`表的详细情况：`mysqlshow -uroot -p123 test book --count`



#### mysqldump

客户端工具用来备份数据库或者在不同数据库之间进行数据迁移

备份内容包括创建表以及插入表的 **SQL** 语句，默认保存在`.sql`文件中

+ 语法：
  + `mysqldump [options] db_name [tables]`
  + `mysqldump [options] --database/-B db_name...`
  + `mysqldump [options] --all-databases/-A`
+ 连接选项：
  + `-u,--user-name`：指定用户名
  + `-p,--password[=pwd]`：指定密码
  + `-h,--host=ipndomain`：指定服务器ip或域名
  + `-P,--port=#`：指定连接端口
+ 输出选项：
  + `--add-drop-database`：在每个数据库创建语句前加上`drop database`语句
  + `--add-drop-table`：在每个表创建语句前加上`drop table`语句，默认开启，如果需要关闭则添加`--skip-add-drop-table`
  + `-n,--no-create-db`：不包含数据库的创建语句
  + `-t,--no-create-info`：不包含数据表的创建语句
  + `-d,--no-data`：不包含数据
  + `-T,--tab=name`：自动生成两个文件，包含创建表结构语句的`.sql`文件和额外的包含数据的`.txt`文件，**MySQL** 会对``.txt`文件的输出路径进行检查，默认安全的存放路径可以通过`show variables like '%secure_file_priv%'`查询



#### mysqlimport/source

客户端数据导入工具，用来导入`mysqldump`加上`-T`参数后导出的文本文件（文件名需要和表名一致）

+ 语法：`mysqlimport [options] db_name filename...`
+ 示例：`mysqlimport -uroot -p123 test /tmp/emp.txt`

如果需要导入`.sql`文件则可以使用 **MySQL** 终端下的`source`指令：`source #.sql`



# SQL 运维

## 日志

### 错误日志

**MySQL** 中最重要的日志之一，记录了当`mysqld`启动和停止时、以及服务器在运行过程中发生任何严重错误时的相关信息，当数据库出现任何故障导致无法正常使用时，建议首先查看此日志

该日志默认开启，存放在`/var/log/`目录下的`mysqld.log`文件中，日志位置查询：`show variables like '%log_error%'`



### 二进制日志

记录了所有的`DDL`和`DML`语句，但不包括数据查询语句

作用：

+ 意外发生时进行数据恢复
+ **MySQL** 的主从复制

相关参数：`show variables like '%log_bin%'`

日志格式：

+ `STATEMENT`：基于 **SQL** 语句的日志记录，记录的是 **SQL** 语句，对数据进行修改的 **SQL** 都会记录在日志中
+ `ROW`：基于行的日志记录，记录的是每一行的数据变更（默认）
+ `MIXED`：混合了`STATEMENT`和`ROW`两种格式，默认采用`STATEMENT`，在某些特殊情况下会自动切换为`ROW`进行记录

相关参数：`show variables like '%binlog_format%'`

日志清理：业务系统较为繁忙时，会生成大量的`binlog`，如果不进行清理则会占用大量的磁盘空间

+ `reset master`：删除全部`binlog`日志，删除后日志编号也会重置为`binlog.000001`
+ `purge master logs to 'binlog.******'`：删除`******`（不包含）编号以前的所有日志
+ `purge master logs before 'yyyy-mm-dd hh24:mi:ss'`删除`'yyyy-mm-dd hh24:mi:ss'`之前产生的所有日志



### 查询日志

查询日志记录了客户端的所有操作语句（二进制日志不包含查询数据的语句），默认情况下查询日志不开启，需要手动开启

```sql
# 相关参数
show variables like '%general%';

# 开启方式为修改 /etc/my.cnf 文件
# general_log = 1 表示开启日志
# general_log_file = # 表示指定保存的文件名 默认为 host_name.log
```



### 慢查询日志

记录了所有执行时间超过参数`long_query_time`（默认10s）并且扫描记录数不小于`min_examined_row_limit`的所有 SQL 语句的日志，默认关闭

开启方式为：修改`/etc/my.cnf`的`show_query = 1`，相关参数的配置也在该文件下修改

默认情况下，慢查询日志不会记录管理语句，也不会记录不适用索引进行查找的查询，可以修改`log_slow_admin_statements`和`log_queries_not_using_indexes`的值为**1**表示开启两种功能



## 主从复制

### 概述

将主数据库的`DDL`和`DML`操作通过二进制日志传到从库服务器中，然后在从库上对这些日志重新执行（也称为重做），从而使从库和主库的数据保持同步

**MySQL** 支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制

优点：

+ 主库出现问题时，可以快速切换到从库提供服务
+ 实现读写分离，降低主库的访问压力
+ 可以在从库中实现备份，避免备份期间影响主库服务



### 原理

主库生成二进制日志后，从库通过 **IOthread** 向主库发起请求，获取主库生成的二进制日志内容，将内容写入本地的中继日志`Relay log`当中，接着从库再通过 **SQLthread** 读取中继日志的内容，执行其中的 SQL 语句，完成数据的载入



### 搭建

首先需要开放主从服务器的 **3306** 端口或者直接关闭防火墙（开发阶段不建议关闭）

+ 开放端口：`firewall-cmd --zone=public --add-port=3306/tcp -permanent`、`firewall-cmd -reload`
+ 关闭防火墙：`systemctl stop firewalld`、`systemctl disable firewalld`

然后确保两台服务器安装好 **MySQL** 并且处于开启状态



#### 主库配置

修改`/etc/my.cnf`配置文件

```
# mysql 服务 ID
# 保证整个集群环境中唯一 取值范围为 1 至 2^32 - 1 默认为 1
server-id = 1

# 是否只读 1代表只读 0代表读写
read-only = 0

# 忽略的数据 指不需要同步的数据库
# binlog-ignore-db = mysql
# 指定同步的数据库
# binlog-do-db = db01
```

配置完毕后重启 **MySQL**：`systemctl restart mysqld`



登录 **MySQL** 创建远程连接用的账号，授予主从复制权限

```sql
# 创建 testusr 用户并设置密码'ROOT@123' 令该用户可以在任意主机连接该 MySQL 服务
CREATE USER 'testusr'@'%' IDENTIFIED WITH mysql_native_password BY 'ROOT@123';

# 为 testusr 分配主从复制权限
GRANT REPLICATION SLAVE ON *.* TO 'testusr'@'%';
```



在 **MySQL** 终端中通过指令查看二进制日志坐标`show master status;`，通过该指令查询的内容包含多个字段，其中：

+ `file`：从哪个二进制日志开始推送日志文件
+ `position`：从哪个位置开始推送日志
+ `binlog_ignore_db`：指定不需要同步的数据库



#### 从库配置

修改`/etc/my.cnf`配置文件

```
# mysql 服务 ID
# 保证整个集群环境中唯一 取值范围为 1 至 2^32 - 1 默认为 1
server-id = 2

# 是否只读 1代表只读 0代表读写
read-only = 1
# 只读设定表示的是对普通用户只读
# 如果具有超级管理员权限则依然可写 除非设定超级管理员也只读
# super-read-only = 1
```

配置完毕后重启 **MySQL**：`systemctl restart mysqld`



登录 **MySQL** 设置主库配置

```sql
# v8.0.23+
# 下列参数分别是 主库IP地址 连接主库的用户名 连接主库的密码 binlog日志文件名 binlog日志文件起始位置
CHANGE REPLICATION SOURCE TO SOURCE_HOST='xxx.xxx', SOURCE_USER='xxx', SOURCE_PASSWORD='xxx', SOURCE_LOG_FILE='xxx', SOURCE_LOG_POS=xxx;
# 安装先前的配置填入参数 其中 LOG_FILE 和 LOG_POS 可以通过 show master status; 指令查询
CHANGE REPLICATION SOURCE TO SOURCE_HOST='主库IP', SOURCE_USER='testuser', SOURCE_PASSWORD='ROOT@123', SOURCE_LOG_FILE='binlog.xxxxxx', SOURCE_LOG_POS=xxx;

# 旧版本
CHANGE MASTER TO MASTER_HOST='xxx.xxx.xxx.xxx', MASTER_USER='xxx', MASTER_PASSWORD='xxx', MASTER_LOG_FILE='xxx', MASTER_LOG_POS=xxx;
```



配置完毕后，开启同步操作

```sql
# v8.0.22+
start replica;

# 旧版本
start slave;
```



查看主从同步状态，如果`Replica_IO_Running`和`Replica_SQL_Running`两项的值为 **Yes** 则说明主从复制正常运行

```sql
# v8.0.22+
show replica status;

# 旧版本
show slave status;
```

此后，在主库进行的更改会自动同步到从库当中



## 分库分表

### 概念

随着业务累计，应用系统的数据量大幅增加，如果采用但数据库进行数据存储，存在一定性能瓶颈：

+ IO 瓶颈：热点数据过多，数据库缓存不足，产生大量磁盘 IO，效率降低；请求数据过多，带宽不足，网络 IO 瓶颈
+ CPU 瓶颈：排序、分组、连接查询、聚合统计等会耗费大量的 CPU 资源，请求数过多则 CPU 出现瓶颈

分库分表的核心思想在于将数据分散存储，使得单一数据库 / 表的数据量变小来缓解单一数据库性能问题，从而达到提升数据库性能的目的

实现方式有：

+ `shardingJDBC`：基于 **AOP**（面向切面编程）原理，在应用程序中对本地执行的 SQL 进行拦截，然后进行解析、改写、路由处理，需要自行编码配置实现，只支持 **Java** 语言，性能较高
+ `MyCar`：数据库分库分表的中间件，不用调整代码即可实现分库分表，支持多种语言，性能不及`shardingJDBC`



### 拆分策略

+ 垂直拆分
  + 垂直分库
  + 垂直分表
+ 水平拆分
  + 水平分库
  + 水平分表



#### 垂直拆分

垂直分库：以表为依据，根据业务将不同表拆分到不同库中：

+ 每个库的表结构都不一样
+ 每个库的数据也不一样
+ 所有库的并集是全量数据

垂直分表：以字段为依据，根据字段属性将不同字段拆分到不同表中

+ 每个表的结构都不一样
+ 每个表的数据也不一样，一般通过主键外键进行关联
+ 所有表的并集是全量数据

#### 水平拆分

水平分库：以字段为依据，按照一定策略，将一个库的数据拆分到多个库中

+ 每个库的表结构都一样
+ 每个库的数据都不一样
+ 所有库的并集是全量数据

水平分表：以字段为依据，按照一定策略，将一个表的数据拆分到多个表中

+ 每个表的表结构都一样
+ 每个表的数据都不一样
+ 所有表的并集是全量数据



### MyCat

**MyCat** 是一种开源、活跃、基于 **Java** 语言编写的 **MySQL** 中间件，可以像使用 **MySQL**一样使用 **MyCat**，对于开发人员来说无法感知到 **MyCat** 的存在



优势：

+ 性能可靠稳定
+ 强大的技术团队
+ 体系完善
+ 社区活跃



#### 安装

官网链接：[点击进入](https://github.com/MyCATApache/Mycat2)

需要下载的内容：

+ MySQL（中间件服务器、分片服务器）
+ JDK（中间件服务器）
+ MyCat（中间件服务器）

将 jdk 和 mycat 安装包传入服务器进行解压：`tar -zxvf #.tar.gz -C dir`

**JDK**

配置环境变量，配置文件路径为`/etc/profile`，在文件末尾加入：

```
JAVA_HOME={{{path_jdk}}}
PATH=$PATH:$JAVA_HOME/bin
```

然后重载配置文件：`source /etc/profile`

检查是否配置成功：`java -version`

**MyCat**

来到 **MyCat** 目录下，可以观察到有若干文件，其中：

+ `/bin`：存放可执行文件，用于启动和停止 **MyCat**
+ `/conf`：存放配置文件
+ `/lib`：存放项目依赖包（jar）
+ `/logs`：存放日志文件

如果`/lib`目录下的驱动包`mysql-connector-java-#.jar`版本过低，可以替换为高版本，然后为该驱动包授权`chmod 777 mysql-connector-java-#.jar`



#### 核心概念

+ 逻辑结构：逻辑库（schema）->若干逻辑表（table）-<span style="font-size: .64rem">分片规则</span>->若干分片节点（data node）
+ 物理结构：分片节点存储的内容，物理上存放于各个主机当中，每个数据库所在的主机成为节点主机



#### 简单入门

**配置**

进行分库分表前，需要开放相应的端口`:3306`或者关闭防火墙

进行分片配置：`/conf/schema.xml`

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
	<schema name="{{{逻辑库名}}}" checkSQLschema="true" sqlMaxLimit="100">
		<!-- rule 表示分片规则 -->
		<table name="{{{逻辑表名}}}" dataNode="dn1,dn2,dn3" rule="auto-sharding-long"/>
	</schema>
    
	<dataNode name="dn1" dataHost="localhost1" database="db1"/>
	<dataNode name="dn2" dataHost="localhost2" database="db2"/>
	<dataNode name="dn3" dataHost="localhost3" database="db3"/>
    
	<!-- dbDriver 表示数据库驱动 可选 native 和 jdbc -->
	<dataHost name="localhost1" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
        <heartbeat>select user()</heartbeat>
        <!-- url 填写主机地址 后方的参数表示时区等参数信息 -->
        <writeHost host="hostS1" url="jdbc:mysql://{{{IP:port}}}?userSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="123456"/>
    </dataHost>
    
	<dataHost name="localhost2" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
        <heartbeat>select user()</heartbeat>
        <writeHost host="hostS1" url="jdbc:mysql://{{{IP:port}}}?userSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="123456"/>
    </dataHost>
    
	<dataHost name="localhost2" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
        <heartbeat>select user()</heartbeat>
        <writeHost host="hostS1" url="jdbc:mysql://{{{IP:port}}}?userSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="123456"/>
    </dataHost>
</mycat:schema>
```

进行分片配置：`/conf/server.xml`

```xml
...
<!-- 配置 mycat 的用户以及用户的权限信息 -->
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">{{{逻辑库名}}}</property>
    
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="false">
		<schema name="TESTDB" dml="0110">
			<table name="tb01" dml="0000"></table>
			<table name="tb02" dml="1111"></table>
		</schema>
    </privileges>
	-->
</user>

<user name="user">
	<property name="password">123456</property>
	<property name="schemas">{{{逻辑库名}}}</property>
	<property name="readOnly">true</property>
</user>
```



**启动服务**

```
# 启动
bin/mycat start

# 停止
bin/mycat stop
```

如果希望检查 **MyCat** 是否安装成功，可以通过`tail -f logs/wrapper.log`检查日志中是否有相应的启动信息

启动完毕后，**MyCat** 会占用`8066`号端口



**分片测试**

通过 **MySQL** 连接到 **MyCat** 对应的服务：`mysql -h {{{IP}}} -P 8066 -uroot -p123456`

此时 **MySQL** 连接成功后，展示的服务器版本为 **MyCat** 相关信息

```sql
# 查看数据库
show databases;
# 此时可以查询到先前配置的逻辑库

use {{{逻辑库名}}};
show tables;
# 此时可以查询到先前配置的逻辑表
# 但是在服务器主机上 该表是不存在的 也就是说这个表还处于逻辑层面上
# 可以通过建表语句为逻辑表赋予表结构
# 建表后 真实表也就建立完成 底层涉及的所有数据节点当中都会有同样的表产生
# 后续更新表数据时 会根据配置好的规则 将数据有规律地插入某一个数据节点当中
```



#### 配置相关

**schema.xml**

作为 **MyCat** 最重要的配置文件之一，涵盖了逻辑库、逻辑表、分片规则、分片节点以及数据源的配置

主要包含以下三组标签：

+ `<schema>`
+ `<dataNode>`
+ `<dataHost>`



`<schema>`用于定义 **MyCat** 中的逻辑库，一个 **MyCat** 实例中可以有多个逻辑库，可以通过`<schema>`来划分不同的逻辑库（在 **MyCat** 中逻辑库的概念相当于 **MySQL** 中的`database`，需要在操作某个逻辑库前`use db_name;`然后才能操作该库下的表）

核心属性：

+ `name`：指定自定义的逻辑库库名
+ `checkSQLschema`：在 SQL 语句操作时指定了数据库名称`db_name.tb_name`，执行时是否自动去除库名称
  + `true`：自动去除
  + `false`：不自动去除
+ `sqlMaxLimit`：如果未指定`limit`进行查询，列表查询模式查询多少条记录

`<schema> <table> </schema>`标签定义了 **MyCat** 中逻辑库下的逻辑表，所有需要拆分的表都需要在`<table>`中定义

核心属性：

+ `name`：定义逻辑表表名，在一个逻辑库下唯一
+ `dataNode`：定义逻辑表所属的`dataNode`，该属性需要与`<dataNode>`中的`name`属性对应，多个`dataNode`用`,`分隔
+ `rule`：分片规则的名字，分片规则是在`rule.xml`中定义的
+ `primaryKey`：逻辑表对应真实表的主键
+ `type`：逻辑表类型，目前逻辑表只有全局表`global`和普通表，如果未配置默认为普通表，由于多表联查时不能跨越实体库连接多个表，因此如果有的表属于字典表，可以设置为全局表`global`使用（结点也要同时设置为多个节点，`global`控制的是数据是否要同时插入到各个库中对应的表内）



`<dataNode>`定义了 **MyCat** 中的数据节点，也就是数据分片，一个`<dataNode>`就是一个独立的数据分片

核心属性：

+ `name`：定义数据节点名称
+ `dataHost`：数据库实例主机名称，引用自`<dataHost>`中的`name`属性
+ `database`：定义分片所属数据库



`<dataHost>`在 **MyCat** 逻辑看中作为底层标签存在，直接定义了具体的数据库实例、读写分离、心跳语句

核心属性：

+ `name`：唯一标识，供上层标签使用
+ `maxCon / minCon`：最大 / 最小连接数
+ `balance`：负载均衡策略，取值为：0, 1, 2, 3，是控制读写分离的重要手段
+ `writeType`：写操作分发方式
  + 0：写操作转发到第一个`writeHost`，第一个挂了就切换到第二个
  + 1：写操作随机分发到配置的`writeHost`
+ `dbDriver`：数据库驱动，支持`native`和`jdbc`



**ruler.xml**

定义了所有拆分表的规则，在使用过程中可以灵活使用分片算法，或者对同一个分片算法使用不同参数，它让分片过程可配置化，主要包含两类标签：`<tableRule>`、`<function>`



**server.xml**

包含 **MyCat** 的系统配置信息，主要有两个重要标签：`<system>`、`<user>`

`<system>`主要包含对应的系统配置项以及含义



`<user>`主要配置的是用户信息

```xml
<user name="root" defaultAccount="true"> <!-- name 表示用户名 -->
	<property name="password">123456</property> <!-- 配置密码 -->
	<property name="schemas">{{{逻辑库名}}}</property> <!-- 该用户可以访问的逻辑库 多个逻辑库用','分隔 -->
    
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="false"> # 是否开启 DML 权限检查 默认为 false
		<schema name="TESTDB" dml="0110"> # 配置指定的逻辑库权限 dml四个位置分别代表增改查删
			<table name="tb01" dml="0000"></table> # 配置指定的逻辑表权限 没配置表权限则沿用库权限
			<table name="tb02" dml="1111"></table> # 配置指定的逻辑表权限
		</schema>
    </privileges>
	-->
</user>

<user name="user">
	<property name="password">123456</property>
	<property name="schemas">{{{逻辑库名}}}</property>
	<property name="readOnly">true</property> <!-- 是否只读 默认为 false -->
</user>
```



#### 分片示例

+ 垂直分库

```xml
<!-- schema.xml -->
<schema name="db01" checkSQLschema="true" sqlMaxLimit="100">
    <table name="tb01" dataNode="dn1" primaryKey="id"/>
    <table name="tb02" dataNode="dn1" primaryKey="id"/>
    <table name="tb03" dataNode="dn1" primaryKey="id"/>

    <table name="tb04" dataNode="dn2" primaryKey="id"/>
    <table name="tb05" dataNode="dn2" primaryKey="id"/>
    <table name="tb06" dataNode="dn2" primaryKey="id"/>

    <table name="tb07" dataNode="dn3" primaryKey="id"/>
    <table name="tb08" dataNode="dn3" primaryKey="id"/>
    <table name="tb09" dataNode="dn3" primaryKey="id"/>
</schema>

<dataNode name="dn1" dataHost="localhost1" database="db01"/>
<dataNode name="dn2" dataHost="localhost2" database="db01"/>
<dataNode name="dn3" dataHost="localhost3" database="db01"/>
```

```xml
<!-- server.xml -->
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">db01</property>
</user>

<user name="user">
	<property name="password">123456</property>
	<property name="schemas">db01</property>
	<property name="readOnly">true</property>
</user>
```



+ 水平分表

```xml
<!-- schema.xml -->
<schema name="db02" checkSQLschema="true" sqlMaxLimit="100">
    <table name="tb10" dataNode="dn4,dn5,dn6" rule="mod-long"/>
</schema>

<dataNode name="dn4" dataHost="localhost1" database="db02"/>
<dataNode name="dn5" dataHost="localhost2" database="db02"/>
<dataNode name="dn6" dataHost="localhost3" database="db02"/>
```

```xml
<!-- server.xml -->
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">db01,db02</property>
</user>
```



#### 分片规则

##### 范围分片

根据指定的字段及其配置的范围与数据节点的对应情况，决定数据属于哪一个分片

`<table rule="auto-sharding-long" />`

相关配置如下：`autopartition-long.txt`

```
# range start-end ,data node index
# K=1000,M=10000.
0-500M=0
500M-1000M=1
1000M-1500M=2
```



##### 取模分片

根据指定的字段值与节点数量进行模运算，根据运算结果决定数据属于哪一分片

`<table rule="mod-long" />`

相关配置如下：`rule.xml`

```xml
<function name="mod-long" class="io.mycat.route.function.PartitionByMod">
	<property name="count">3</property> <!-- 节点数量 -->
</function>
```



##### 一致性哈希

计算指定的字段的散列值，然后根据该散列值决定数据属于哪一分片

相同的哈希因子计算值总是被划分到相同的分区表中，不会因为分区节点的增加而改变原来数据的分区位置

`<table rule="sharding-by-murmur" />`

相关配置如下：`rule.xml`

```xml
<function name="murmur" class="io.mycat.route.function.PartitionByMurmurHash">
	<property name="seed">0</property> <!-- 默认为 0 -->
	<property name="count">3</property> <!-- 要分片的节点数量 必须指定 -->
	<property name="virtualBucketTimes">160</property> <!-- 每个物理节点被映射为 160 份虚拟节点 -->
	<!-- <property name="weightMapFile">weightMapFile</property> # 节点权重 默认为 1 -->
</function>
```



##### 枚举分片

通过在配置文件中配置可能的枚举值，指定数据分布到不同数据节点上，适用于省份、性别、状态等数据拆分

`<table rule="sharding-by-intfile-enumstatus" />`

相关配置如下：`rule.xml`

```xml
<function name="hash-int" class="io.mycat.route.function.PartitionByFileMap">
    <property name="defaultNode">0</property> <!-- 默认节点 -->
    <property name="mapFile">partition-hash-int.txt</property>
</function>
```

`partition-hash-int.txt`

```
key_1=0
key_2=1
key_3=2
```



##### 子串分片

运行阶段由应用自主决定路由到哪个分片，直接根据字符子串（必须是数字）计算分片号

`<table rule="sharding-by-substring" />`

相关配置如下：`rule.xml`

```xml
<function name="sharding-by-substring" class="io.mycat.route.function.PartitionBySubString">
    <property name="startIndex">0</property> <!-- 开始索引 -->
    <property name="size">2</property> <!-- 截取长度 -->
    <property name="partitionCount">3</property> <!-- 分片数量 -->
    <property name="defaultPartition">0</property> <!-- 默认分片 -->
</function>
```

示例：`001001`、`011001`、`021001`



##### 固定分片哈希

类似十进制模运算，但是会取一个串的几位和`1`按位与，根据计算结果判断数据属于哪一分片

特点：

+ 取模分片连续的值会分配到不同分片，固定分片连续的值可能会分配到相同分片，降低事务处理难度
+ 可以均匀分配，也可以非均匀分配
+ 分片字段必须为数字类型

`<table rule="sharding-by-long-hash" />`

相关配置如下：`rule.xml`

```xml
<function name="sharding-by-long-hash" class="io.mycat.route.function.PartitionByLong">
    <!-- 分片大小之和不能超过 1024 -->
    <property name="partitionCount">2,1</property> <!-- 分片数量 共 3 个 -->
    <property name="partitionLength">256,512</property> <!-- 对应的分片大小 2个256 1个512 -->
</function>
```



##### 字符串哈希

截取字符串中指定位置的子字符串进行哈希算法分片

`<table rule="sharding-by-stringhash" />`

相关配置如下：`rule.xml`

```xml
<function name="sharding-by-stringhash" class="io.mycat.route.function.PartitionByString">
    <property name="partitionLenght">512</property> <!-- 节点大小 -->
    <property name="partitionCount">2</property> <!-- 节点数量 -->
    <property name="hashSlice">0:2</property> <!-- start:end 左右包含 -->
    <!-- end位置: 0代表str.lenght(); -1代表srt.lenght(); 大于0代表数字本身 -->
</function>
```



##### 按天分片

根据字段日期值决定数据属于哪一分片

`<table rule="sharding-by-date" />`

相关配置如下：`rule.xml`

```xml
<tableRule name="sharding-by-date">
	<rule>
		<columns>{{{column_date}}}</columns>
		<algorithm>sharding-by-date</algorithm>
    </rule>
</tableRule>

<function name="sharding-by-date" class="io.mycat.route.function.PartitionByDate">
    <property name="dateFormat">yyyy-MM-dd</property>
    <property name="sBeginDate">2024-01-01</property> <!-- 起始日期 -->
    <property name="sEndDate">2024-01-30</property> <!-- 结束日期 -->
    <property name="sPartitionDay">10</property> <!-- 每 10 天一周期 到达结束日期后会循环重复分片插入 且至少要 3 个分片 -->
</function>
```



##### 按月分片

根据字段的月份值决定数据属于哪一分片

`<table rule="sharding-by-sharding-by-month" />`

相关配置如下：`rule.xml`

```xml
<tableRule name="sharding-by-month">
	<rule>
		<columns>{{{column_date}}}</columns>
		<algorithm>partbymonth</algorithm>
    </rule>
</tableRule>

<function name="partbymonth" class="io.mycat.route.function.PartitionByMonth">
    <property name="dateFormat">yyyy-MM-dd</property>
    <property name="sBeginDate">2024-01-01</property> <!-- 起始日期 -->
    <property name="sEndDate">2024-03-31</property> <!-- 结束日期 --> <!-- 至少要 3 个分片 -->
</function>
```



#### 管理与监控

**原理：MyCat** 在执行时会对语句进行如下操作

解析 SQL -> 分片分析 -> 路由分析 -> 读写分离分析

如果是查询语句还会进行进一步操作再将结果返回

结果合并 -> 聚合处理 -> 排序处理 -> 分页处理



**管理**

**MyCat** 默认开放两个端口（可以在`server.xml`中进行查看与配置）：

+ `:8066`：数据访问端口，可以进行`DML`和`DDL`操作
+ `:9066`：数据库管理端口，用于管理 **MyCat** 整个集群状态

连接：`mysql -h {{{IP}}} -P9066 -uroot -p123456`

命令：

+ `show @@help`：查看 **MyCat** 管理工具帮助文档
+ `show @@version`：查看 **MyCat** 版本
+ `reload @@config`：重新加载配置文件
+ `show @@datasource`：查看数据源信息
+ `show @@datanode`：查看现有的分片节点信息
+ `show @@threadpool`：查看线程池信息
+ `show @@sql`：查看执行的 SQL
+ `show @@sql.sum`：查看执行的 SQL 统计



#### Mycat-eye

**Mycat-web(Mycat-eye)** 可以对 mycat-server 提供监控服务，功能不局限于对 mycat-server 的使用，通过 **JDBC** 连接并监控 **MyCat**，监控远程服务器（目前仅限于 **Linux**）的 cpu、内存、网络、磁盘

运行过程依赖`zookeeper`

启动 **Mycat-eye** 后（`sh start.sh`），进入`http://{{{IP}}}:8082/mycat`即可通过图形化界面监控管理 **MyCat**



## 读写分离

### 概念

将数据库的读写操作分开，对应到不同的数据库服务器上，主数据库提供写操作、从数据库提供读操作，有效减轻单台数据库的压力

可以通过 **MyCat** 实现上述功能



### 一主一从

利用 **MyCat** 配置文件实现读写分离，优势在于主库宕机后从库依然能提供数据读取服务

+ `schema.xml`

```xml
<schema name="{{{逻辑库名}}}" checkSQLschema="true" sqlMaxLimit="100" dataNode="dn1">
    <!-- <table name="{{{逻辑表名}}}" dataNode="dn1" primaryKey="id"/> -->
</schema>

<dataNode name="dn1" dataHost="localhost1" database="db1"/>

<dataHost name="localhost1" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
    <heartbeat>select user()</heartbeat>
    
    <writeHost host="master" url="jdbc:mysql://{{{IP_master:port}}}?userSSL=false" user="root" password="123">
		<readHost host="slave" url="jdbc:mysql://{{{IP_slave:port}}}?userSSL=false" user="root" password="123"/>
	</writeHost>
</dataHost>
```

关键属性`balance`：负载均衡策略

+ `0`：不开启读写分离机制，所有操作都发送到可用的`<writeHost>`对应主机上
+ `1`：全部的`<readHost>`与备用的`<writeHost>`都参与`select0`语句的负载均衡（针对双主双从）
+ `2`：所有的读写操作都随机在`<writeHost>`和`<readHost>`上分发
+ `3`：所有的读请求随机分发到`<writeHost>`对应的`<readHost>`上执行，`<writeHost>`不负担读压力

`1`和`3`可以实现读写分离



### 双主双从

一对一主一从结构，然后两台`master`互为主从，构成双主双从读写分离结构

`master_1`处理所有的写请求，`slave_1`、`master_2`、`slave_2`负责所有的读请求，当`master_1`宕机后，`master_2`负责写请求



示例：

|        IP         |         框架         | 作用                   |
| :---------------: | :------------------: | ---------------------- |
| `192.168.200.210` | **MySQL**、**MyCat** | **MyCat** 中间件服务器 |
| `192.168.200.211` |      **MySQL**       | `M1`                   |
| `192.168.200.212` |      **MySQL**       | `S1`                   |
| `192.168.200.213` |      **MySQL**       | `M2`                   |
| `192.168.200.214` |      **MySQL**       | `S2`                   |



#### 192.168.200.211

+ `/etc/my.cnf`

```
server-id=1

# 指定同步的数据库
binlog-do-db=db01
binlog-do-db=db02
binlog-do-db=db03

# 在作为从数据库时 有写入操作也要更新二进制日志文件
log-slave-updates
```

+ **MySQL cmd**

```sql
# 创建用户 分配权限
create user 'wwr'@'%' identified with mysql_native_password by 'Root'@'123456';
grant replication slave on *.* to 'wwr'@'%';

# 查看两台主库的二进制日志坐标
show master status;
```



#### 192.168.200.213

+ `/etc/my.cnf`
```
server-id=3

# 指定同步的数据库
binlog-do-db=db01
binlog-do-db=db02
binlog-do-db=db03

# 在作为从数据库时 有写入操作也要更新二进制日志文件
log-slave-updates
```

+ **MySQL cmd**

```sql
# 创建用户 分配权限
create user 'wwr'@'%' identified with mysql_native_password by 'Root'@'123456';
grant replication slave on *.* to 'wwr'@'%';

# 查看两台主库的二进制日志坐标
show master status;
```



#### 192.168.200.212

+ `/etc/my.cnf`

```
server-id=2
```

+ **MySQL cmd**

```sql
change master to MASTER_HOST='192.168.200.211', MASTER_USER='wwr', MASTER_PASSWORD='123456', MASTER_LOG_FILE='自己查', MASTER_LOG_POS='自己查';

# 启动主从复制
start slave;

# 查看从库状态
show slave status \G;
```



#### 192.168.200.214

+ `/etc/my.cnf`

```
server-id=4
```

+ **MySQL cmd**

```sql
change master to MASTER_HOST='192.168.200.213', MASTER_USER='wwr', MASTER_PASSWORD='123456', MASTER_LOG_FILE='自己查', MASTER_LOG_POS='自己查';

# 启动主从复制
start slave;

# 查看从库状态
show slave status \G;
```



#### 两台主服务器互相连接

##### 192.168.200.211

```sql
# 将 M2 设为自己的主服务器
change master to MASTER_HOST='192.168.200.213', MASTER_USER='wwr', MASTER_PASSWORD='123456', MASTER_LOG_FILE='自己查', MASTER_LOG_POS='自己查';

# 启动主从复制
start slave;

# 查看从库状态
show slave status \G;
```



##### 192.168.200.213

```sql
# 将 M1 设为自己的主服务器
change master to MASTER_HOST='192.168.200.211', MASTER_USER='wwr', MASTER_PASSWORD='123456', MASTER_LOG_FILE='自己查', MASTER_LOG_POS='自己查';

# 启动主从复制
start slave;

# 查看从库状态
show slave status \G;
```



#### MyCat 配置

+ `server.xml`

```xml
<schema name="{{{逻辑库名}}}" checkSQLschema="true" sqlMaxLimit="100" dataNode="dn1">
    <!-- <table name="{{{逻辑表名}}}" dataNode="dn1" primaryKey="id"/> -->
</schema>

<dataNode name="dn1" dataHost="localhost1" database="db1"/>

<dataHost name="localhost1" maxCon="1000" minCon="10" balance="1" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
    <heartbeat>select user()</heartbeat>

	<!-- M1 负责写 S1 M2 S2 负责读 -->
	<writeHost host="master" url="jdbc:mysql://192.168.200.211:3306?userSSL=false" user="root" password="123">
		<readHost host="slave" url="jdbc:mysql://192.168.200.212:3306?userSSL=false" user="root" password="123"/>
	</writeHost>

	<writeHost host="master" url="jdbc:mysql://192.168.200.2131:3306?userSSL=false" user="root" password="123">
		<readHost host="slave" url="jdbc:mysql://192.168.200.214:3306?userSSL=false" user="root" password="123"/>
	</writeHost>
</dataHost>
```

关键属性：

+ `writeType`：
  + `0`：写操作都转发到第一台`<writeHost>`，发生宕机则切换到第二台`<writeHost>`
  + `1`：所有写操作都随机发送到配置的`<writeHost>`上
+ `switchType`：
  + `-1`：发生宕机不自动切换
  + `1`：发生宕机自动切换