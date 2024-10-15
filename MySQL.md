# MySQL

MySQL的连接

```shell
mysql [-h 127.0.0.1] [-P 3306] -u root -p
# 参数：
# -h : MySQL服务所在的主机IP
# -P : MySQL服务端口号， 默认3306
# -u : MySQL数据库用户名
# -p ： MySQL数据库用户名对应的密码
```



# 1 SQL语法

| 分类 | 全称                       | 说明                                                   |
| :--: | -------------------------- | ------------------------------------------------------ |
| DDL  | Data Definition Language   | 数据定义语言，用来定义数据库对象(数据库，表)           |
| DML  | Data Manipulation Language | 数据操作语言，用来对数据库表中的数据进行增删改         |
| DQL  | Data Query Language        | 数据查询语言，用来查询数据库中表的记录                 |
| DCL  | Data Control Language      | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |

**注释**

- 单行注释：**--** 注释内容 或 **#** 注释内容
- 多行注释：/* 注释内容 */

### 1.1 DDL

*Data Definition Language*  数据定义语言，用来定义数据库对象(数据库，表)

#### 1.1.1 数据库操作

```sql
# 查询所有数据库
show database;
# 创建数据库，字符集编码最好用utf8mb4；if not exists不写时，在库已存在时创建会报错
create database [ if not exists ] 数据库名 [ default charset 字符集 ] [ collate 排序规则 ];
create database if not exists xxx default charset utf8mb4;

# 删除数据库；不写if exists时，数据库不存在的删除动作会报错
drop database [ if exists ] 数据库名 ;

# 切换数据库
use 数据库名;

# 显示当前所在的数据库
select database();
```



#### 1.1.2 表操作

#### 1.1.2.1 查询当前数据库所有的表

```sql
show tables;
```

#### 1.1.2.2 查看表结构

```sql
desc 表名;
```

#### 1.1.2.3 创建表

##### 1.1.2.3.1 语法

```sql
CREATE TABLE 表名(
字段1 字段1类型 [ COMMENT 字段1注释 ],
字段2 字段2类型 [COMMENT 字段2注释 ],
字段3 字段3类型 [COMMENT 字段3注释 ],
......
字段n 字段n类型 [COMMENT 字段n注释 ]
) [ COMMENT 表注释 ] ;
```

##### 1.1.2.3.2 查询指定表的建表语句

```sql
show create table 表名 ;
```

##### 1.1.2.3.3 字段数据类型

| 类型        |       大小（bytes）        |
| :---------- | :------------------------: |
| TINYINT     |             1              |
| SMALLINT    |             2              |
| MEDIUMINT   |             3              |
| INT/INTEGER |             4              |
| BIGINT      |             8              |
| FLOAT       |             4              |
| DOUBLE      |             8              |
| DECIMAL     | 依赖于M(精度)和D(标度)的值 |

- `int` 类型数据默认有符号，使用无符号时可指定 `unsigned`，如`age int unsigned`
- `double`可指定小数位数，如`score double(4,1)`表示数字最多4位，小数部分最多1位，如：100.0
- DECIMAL

| 类型       |  大小(bytes)  | 描述                         |
| ---------- | :-----------: | ---------------------------- |
| CHAR       |      255      | 定长字符串(需要指定长度)     |
| VARCHAR    |     65535     | 变长字符串(需要指定长度)     |
| TINYBLOB   |      255      | 不超过255个字符的二进制数据  |
| TINYTEXT   |      255      | 短文本字符串                 |
| BLOB       |     65535     | 二进制形式的长文本数据       |
| TEXT       |     65535     | 长文本数据                   |
| MEDIUMBLOB |  16 777 215   | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT |  16 777 215   | 中等长度文本数据             |
| LONGBLOB   | 4 294 967 295 | 二进制形式的极大文本数据     |
| LONGTEXT   | 4 294 967 295 | 极大文本数据                 |

- char是定长字符串，无论数据多长，都占用指定的多少个字符长度，效率高。
- varchar是变长字符串，指定的长度为最大占用长度，数据占用实际字符长度，空间小。

| 类型      | 大小 | 格式                | 描述                     |
| --------- | ---- | ------------------- | ------------------------ |
| DATE      | 3    | YYYY-MM-DD          | 日期值                   |
| TIME      | 3    | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1    | YYYY                | 年份值                   |
| DATETIME  | 8    | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |
| TIMESTAMP | 4    | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |

##### 1.1.2.3.4 约束

| 关键字      | 说明                                               |
| ----------- | -------------------------------------------------- |
| NOT NULL    | 限制该字段的数据不能为null                         |
| UNIQUE      | 保证该字段的所有数据都是唯一、不重复的             |
| PRIMARY KEY | 主键是一行数据的唯一标识，要求非空且唯一           |
| DEFAULT     | 保存数据时，如果未指定该字段的值，则采用默认值     |
| CHECK       | 检查约束(8.0.16版本之后)，保证字段值满足某一个条件 |
| FOREIGN KEY | 用来让两张表的数据之间建立连接，保证数据的一致     |

**外键约束的影响**

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| SET NULL    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。 |
| SET DEFAULT | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |


```sql
# 创建表时直接指定外键
CREATE TABLE 表名(
	字段名 数据类型,
	...
	[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);

# 创建表后为两个表添加外键约束
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名)
REFERENCES 主表 (主表列名) ;

# 删除外键
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;

# 示例
# 部门表
create table dept(
	id int auto_increment comment 'ID' primary key,
	name varchar(50) not null comment '部门名称'
)comment '部门表';

# 员工表
CREATE TABLE emp(
	id int AUTO_INCREMENT PRIMARY KEY COMMENT 'ID唯一标识',
	name varchar(10) NOT NULL UNIQUE COMMENT '姓名' ,
	age int check (age > 0 && age <= 120) COMMENT '年龄' ,
	status char(1) default '1' COMMENT '状态',
	gender char(1) COMMENT '性别',
  dept_id int comment '部门ID'
  # 创建时添加外键使用此语句
  # [CONSTRAINT fk_emp_dept_id] FOREIGN KEY (dept_id) REFERENCES dept(id) ON UPDATE CASCADE ON DELETE SET NULL
);

alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) ON UPDATE CASCADE ON DELETE SET NULL;
```



#### 1.1.2.4 修改表

```sql
# 添加字段
ALTER TABLE 表名 ADD 字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];

# 修改数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);

# 修改字段名和字段类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];

# 删除字段
ALTER TABLE 表名 DROP 字段名;

# 修改表名
ALTER TABLE 表名 RENAME TO 新表名;

# 删除表
DROP TABLE [ IF EXISTS ] 表名;
# 删除表, 并重新创建表
TRUNCATE TABLE 表名;
```



## 1.2 DML

*Data Manipulation Language* 数据操作语言，用来对数据库表中的数据进行增删改

### 1.2.1 添加数据

```sql
# 给指定字段添加数据
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
# 给全部字段添加数据
INSERT INTO 表名 VALUES (值1, 值2, ...);
# 批量添加数据
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ; 
```

### 1.2.2 修改数据

```sql
UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;
```

### 1.2.3 删除数据

```sql
DELETE FROM 表名 [ WHERE 条件 ] ;
```



## 1.3 DQL

*Data Query Language* 数据查询语言，用来查询数据库中表的记录

### 1.3.1 基本语法

```sql
SELECT
	字段列表
FROM
	表名列表
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后条件列表
ORDER BY
	排序字段列表 [排序方式ASC/DESC，默认ASC]
LIMIT
	[起始索引（不传时默认为0）,] 查询条数
```

```sql
# DISTINCT用于去重
# AS可以将字段取别名，也可以省略AS关键字，直接后跟别名
SELECT [DISTINCT] 字段1 [ [AS] 别名1 ] , 字段2 [ [AS] 别名2 ] ... FROM 表名 [[AS] 表别名];
```

| 运算符              | 功能                                         |
| ------------------- | -------------------------------------------- |
| >、>=、< 、<=、=    | 大于、大于等于、小于、小于等于、等于         |
| <> 或 !=            | 不等于                                       |
| BETWEEN ... AND ... | 在某个范围之内(含最小、最大值)               |
| IN(...)             | 在in之后的列表中的值，多选一                 |
| LIKE 占位符         | 模糊匹配('_'匹配单个字符, '%'匹配任意个字符) |
| IS NULL             | 为空                                         |
| AND 或 &&           | 与                                           |
| OR 或 \|\|          | 或                                           |
| NOT 或 !            | 非                                           |

**执行顺序**

FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT

### 1.3.2 函数

#### 1.3.2.1 聚合函数

`count` 计数，`max`最大值，`min` 最小值，`avg` 平均值，`sum` 求和

```sql
SELECT 聚合函数(字段列表) FROM 表名 ;
```

#### 1.3.2.2 字符串函数

```sql
# concat : 字符串拼接
select concat('Hello' , ' MySQL');
# lower : 全部转小写
select lower('Hello');
# upper : 全部转大写
select upper('Hello');
# lpad : 左填充
select lpad('01', 5, '-');
# rpad : 右填充
select rpad('01', 5, '-');
# trim : 去除空格
select trim(' Hello MySQL ');
# substring : 截取子字符串
select substring('Hello MySQL',1,5);

# 示例：将工号改为5位，前补足0
update emp set workno = lpad(workno, 5, '0');
```

#### 1.3.2.3 数值函数

```sql
# ceil：向上取整
select ceil(1.1); # 2
# floor：向下取整
select floor(1.9); # 1
# mod：取模
select mod(7,4); # 3
# rand：获取随机数
select rand();
# round：四舍五入，参数表示保留几位小数
select round(2.344,2); # 2.34
```

#### 1.3.2.4 日期函数

```sql
# curdate：当前日期 YYYY-MM-DD
select curdate();

# curtime：当前时间 HH:MM:SS
select curtime();

# now：当前日期和时间 YYYY-MM-DD HH:MM:SS
select now();

# YEAR , MONTH , DAY：当前年、月、日
select YEAR(now());
select MONTH(now());
select DAY(now());

# date_add：增加指定的时间间隔，例：当前时间70天之后的日期
select date_add(now(), INTERVAL 70 DAY );
```

#### 1.3.2.5 流程函数

```sql
# 如果value为true，则返回'Ok'，否则返回'Error'
select if(value, 'Ok', 'Error');

# ifnull值为null则返回'Default'
select ifnull(null,'Default');

# case when then else end，例：查询各科成绩并显示为优秀、及格、不及格
select
	id,
	name,
	(case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end ) '数学',
	(case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格' end ) '英语',
	(case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格' end ) '语文'
from score;
```

### 1.3.3 多表查询

#### 1.3.3.1 连接

```sql
# 笛卡尔积；查询所有数据的组合；
SELECT 字段列表 FROM 表1 , 表2;
# 内连接：查询的是两张表交集部分的数据
# 隐式内连接
SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;
# 显式内连接
SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
# 查询每一个员工的姓名 , 及关联的部门的名称，如果某个员工的部门ID为空，则结果中不存在此员工的数据
select e.name,d.name from emp e , dept d where e.dept_id = d.id;

# 外连接：查询某个表的所有数据及两张表的交集
# 左外连接，表1的全部数据
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
# 右外连接，表2的全部数据
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
# 员工例子中，如果要保证所有员工都显示，应使用左外连接
select e.*, d.name from emp e left join dept d on e.dept_id = d.id;

# 自连接，自己连接自己，相当于连接的特例，需要为表指定别名以区分
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;
# 查询员工及领导的名字
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id;
```

#### 1.3.3.2 联合查询

```sql
# 把多次查询的结果合并起来，形成一个新的查询结果集
# union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重
# 多次查询的结果，所查询的字段数必须一致
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
SELECT 字段列表 FROM 表B ....;
```

#### 1.3.3.3 子查询

SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询。

- 标量子查询:子查询返回的结果是单个值（数字、字符串、日期等）

```sql
select * from emp where dept_id = (select id from dept where name = '销售部');
```

- 列子查询：子查询返回的结果是一列（可以是多行），常用的操作符：`IN` 、`NOT IN` 、 `ANY或SOME` 、`ALL`

```sql
# 查询比研发部其中任意一人工资高的员工信息
select * from emp where salary > any ( select salary from emp where dept_id =(select id from dept where name = '研发部') );
```

- 为行子查询：子查询返回的结果是一行（可以是多列），常用的操作符：`=` 、`<>` 、`IN` 、`NOT IN`

```sql
#  查询与 "张无忌" 的薪资及直属领导相同的员工信息
select * from emp where (salary,managerid) = (select salary, managerid from empwhere name = '张无忌');
```

- 表子查询：子查询返回的结果是多行多列

```sql
# 查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息
select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left join dept d on e.dept_id = d.id ;
```



## 1.4 DCL

*Data Control Language* 数据控制语言，用来管理数据库用户、控制数据库的访问权限

### 1.4.1 管理用户

```sql
# 查询用户
select * from mysql.user;

# 创建用户
# 主机名表示此用户可通过哪个主机访问，为localhost表示本机，为%表示任意主机
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';

# 修改用户密码
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;

# 删除用户
DROP USER '用户名'@'主机名' ;
```

### 1.4.2 权限控制

- `ALL`或`ALL PRIVILEGES` 表示所有权限
- `SELECT、INSERT、UPDATE、DELETE、ALTER、DROP、CREATE`表示对应的功能权限

```sql
# 查询权限
SHOW GRANTS FOR '用户名'@'主机名' ;

# 授予权限
# 多个权限之间，使用逗号分隔
# 数据库名和表名都可以使用 * 进行通配，代表所有库或所有表
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';

# 撤销权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```



## 1.5 事务

MySQL的事务默认是自动提交的，可修改为手动提交。

```sql
# 查看事务提交方式，默认为自动
SELECT @@autocommit ;
# 设置事务提交方式为手动
SET @@autocommit = 0 ;

# 开启事务
START TRANSACTION 或 BEGIN ;
# 提交事务
COMMIT;
# 回滚事务
ROLLBACK;
```



### 1.5.1 事务四大特性 ACID

- 原子性（**A**tomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
- 一致性（**C**onsistency）：事务完成时，必须使所有的数据都保持一致状态。
- 隔离性（**I**solation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。
- 持久性（**D**urability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。



### 1.5.2 并发事务问题

- 赃读：一个事务读到另外一个事务还没有提交的数据。
- 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同。因为再次读取之间，另一个事务对此数据进行了修改并提交。
- 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了 "幻影"。因为插入数据之前，另一个事务插入了数据并提交。

### 1.5.3 事务隔离级别

为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。事务隔离级别越高，数据越安全，但是性能越低。

| 隔离级别              | 脏读 | 不可重复读 | 幻读 |
| --------------------- | :--: | :--------: | :--: |
| Read uncommitted      |  ✓   |     ✓      |  ✓   |
| Read committed        |  ×   |     ✓      |  ✓   |
| Repeatable Read(默认) |  ×   |     ×      |  ✓   |
| Serializable          |  ×   |     ×      |  ×   |

```sql
# 查看事务隔离级别
SELECT @@TRANSACTION_ISOLATION;
# 设置事务隔离级别
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED|READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
```

