# MySQL 笔记（仔细版）

> 本文为本人所写用于MYSQL的笔记和复习所用

## 什么是数据库

- **数据库**：**DataBase**（`DB`），是**存储**和**管理数据**的仓库
- 数据库连接，语法：**mysql -u 用户名 -p 密码 [-h 数据库服务器IP地址 -P 端口号]**
- 数据库模型：
  - **关系型数据库（RDBMS）**：建立在关系模型基础上，由多张相互连接的二维表组成的数据库

### MySQL 客户端工具 — 图形化工具

| 工具 | 优点 | 缺点 | 适用人群 | 价格 |
|------|------|------|----------|------|
| **SQLyog** | 轻量、启动快、MySQL 专用、操作简单 | 仅支持 MySQL、界面较老 | 中小型项目、DBA | 免费 / 付费版 |
| **Navicat Premium** | 支持多数据库（MySQL、PostgreSQL、Oracle 等）、功能强大、界面美观 | 占用资源多、付费 | 多数据库开发者 | 付费（有试用） |
| **DataGrip** | JetBrains 出品、代码补全智能、支持多数据库、IDE 集成好 | 启动慢、占用内存大 | Java / 后端开发者 | 付费（有免费试用） |

**免费替代方案**：

- **DBeaver**（免费开源、支持多数据库）
- **MySQL Workbench**（官方免费、功能完整）
- **HeidiSQL**（开源免费、轻量）
- **phpMyAdmin**（Web 界面、无需安装）

## 环境变量配置与 MySQL 服务命令

| 序号 | 命令 | 作用 |
|:---:|------|------|
| 1 | `mysqld --initialize-insecure` | 初始化数据库，在 `mysql` 文件夹生成 `data` 目录 |
| 2 | `mysqld --install mysql` | 注册成 `Windows` 系统服务 |
| 3 | `net start mysql` | 启动 `MySQL` 服务 |
| 4 | `net stop mysql` | 停止 `MySQL` 服务 |
| 5 | `mysqladmin -u root -p password 密码` | 设置 `root` 密码 |
| 6 | `mysql -uroot -p` | 登录 `MySQL`（回车后输入密码） |
| 7 | `exit` | 退出 `MySQL` 连接 |

## SQL 四类语句

- **DDL**：数据定义语言，用于定义数据库对象（数据库、表、字段）
- **DML**：数据操作语言，用来对数据库中的数据进行修改
- **DQL**：数据查询语言，用于查询数据库中表的记录
- **DCL**：数据控制语言，用于创建数据库用户、控制数据库的访问权限

### DDL 基础语句

- **查看所有数据库**：`show databases;`
- **创建数据库**：`create database [if not exists] 数据库名;`
- **删除数据库**：`drop database [if exists] 数据库名;`
- **查询当前数据库**：`select database();`
- **使用数据库**：`use 数据库名;`

#### 创建表结构

```mysql
create table 表名 (
    字段1 字段类型 【约束】【comment 字段1注释】,
    字段2 字段类型 【约束】【comment 字段2注释】,
    字段3 字段类型 【约束】【comment 字段3注释】,
    .............
    字段N 字段类型 【约束】【comment 字段N注释】
) 【comment 表注释】;
```

#### 在表中添加内容

> 在插入内容前或者建表前要：`use 数据库名;`

##### 插入单条数据

```mysql
insert into 表名 (字段1, 字段2, ...) values (值1, 值2, ...);
```

##### 插入多条数据

```mysql
insert into 表名 (字段1, 字段2, ...) values
    (值1, 值2, ...),
    (值1, 值2, ...),
    (值1, 值2, ...);
```

比如说：

```mysql
-- 插入员工数据
insert into employees (name, age, birthday, password, phonenum, job, gender, entrdate) values
    ('罗隆涛', 19, '2007-06-08', '12345', '13054063125', '全能开发师', '男', '2025-09-22'),
    ('柴桂伦', 19, '2006-08-28', '12345', '19074374596', '算法设计师', '男', '2025-09-22'),
    ('唐宏', 20, '2006-10-09', '12345', '15774068032', '算法设计师', '男', '2025-09-22'),
    ('夏周勤', 19, '2007-11-15', '12345', '18773372001', 'web工程师', '男', '2025-09-22'),
    ('孟芸霄', 20, '2006-02-27', '12345', '19173937980', '数据库维护师', '男', '2025-09-22');
```

#### 约束的描述

| 约束 | 描述 | 关键字 | 示例 |
|------|------|--------|------|
| 非空约束 | 限制该字段值不能为 `null` | `NOT NULL` | `name VARCHAR(50) NOT NULL` |
| 唯一约束 | 保证字段的所有数据都是唯一、不重复的 | `UNIQUE` | `email VARCHAR(100) UNIQUE` |
| 主键约束 | 主键是一行数据的唯一标识，要求非空且唯一 | `PRIMARY KEY` | `id INT PRIMARY KEY` |
| 默认约束 | 保存数据时，如果未指定该字段值，则采用默认值 | `DEFAULT` | `status INT DEFAULT 0` |
| 外键约束 | 让两张表的数据建立连接，保证数据的一致性和完整性 | `FOREIGN KEY` | `FOREIGN KEY (user_id) REFERENCES users(id)` |

#### 数据库的数据类型

##### 1. 数值类型

| 分类 | 类型 | 大小（byte） | 有符号范围 | 无符号范围 | 描述 | 备注 |
|------|------|:---:|------------|------------|------|------|
| 整数类型 | `tinyint` | 1 | -128 ~ 127 | 0 ~ 255 | 极小整数 | 常用于状态字段，无符号写法：`unsigned` |
| 整数类型 | `smallint` | 2 | -32768 ~ 32767 | 0 ~ 65535 | 小整数 | 常用于小型数值 |
| 整数类型 | `mediumint` | 3 | -8388608 ~ 8388607 | 0 ~ 16777215 | 中等整数 | 较少使用 |
| 整数类型 | `int` | 4 | -2147483648 ~ 2147483647 | 0 ~ 4294967295 | 标准整数 | 最常用整数类型 |
| 整数类型 | `bigint` | 8 | -2^63 ~ 2^63-1 | 0 ~ 2^64-1 | 大整数 | 大数据场景使用 |
| 浮点类型 | `float` | 4 | ±1.175E-38 ~ ±3.403E+38 | 0 ~ ±3.403E+38 | 单精度浮点数 | 精度较低，可设定精度：`float(5,2)` 表示 5 个数字长度、2 个小数位 |
| 浮点类型 | `double` | 8 | ±2.225E-308 ~ ±1.798E+308 | 0 ~ ±1.798E+308 | 双精度浮点数 | 精度较高，可设定精度：`double(5,2)` 表示 5 个数字长度、2 个小数位 |
| 定点类型 | `decimal` | 可变 | 依赖于精度和标度 | 依赖于精度和标度 | 精确小数值 | 财务计算推荐使用，可设定精度：`decimal(5,2)` 表示 5 个数字长度、2 个小数位 |
| 位类型 | `bit` | 1~8 | 无 | 无 | 位字段 | 存储二进制位 |

##### 2. 字符串类型

**`char` 和 `varchar` 的区别**：

| 类型 | 示例 | 存储方式 | 性能 | 空间 |
|------|------|----------|------|------|
| `char(10)`：最多存 10 个字符，不足 10 个字符，占用 10 个字符空间 | ABC | 固定长度，补空格填充 | 性能高（无需计算长度） | 浪费空间（固定分配） |
| `varchar(10)`：最多存 10 个字符，不足 10 个字符，按实际长度存储 | ABC | 变长，按实际长度 +1 字节 | 性能低（需计算长度） | 节省空间（按需分配） |

| 分类 | 类型 | 大小 | 描述 |
|------|------|------|------|
| 字符串类型 | `char` | 0-255 bytes | 定长字符串 |
| 字符串类型 | `varchar` | 0-65535 bytes | 变长字符串 |
| 二进制类型 | `tinyblob` | 0-255 bytes | 不超过 255 个字符的二进制数据 |
| 文本类型 | `tinytext` | 0-255 bytes | 短文本字符串 |
| 二进制类型 | `blob` | 0-65535 bytes | 二进制形式的长文本数据 |
| 文本类型 | `text` | 0-65535 bytes | 长文本数据 |
| 二进制类型 | `mediumblob` | 0-16777215 bytes | 二进制形式的中等长度文本数据 |
| 文本类型 | `mediumtext` | 0-16777215 bytes | 中等长度文本数据 |
| 二进制类型 | `longblob` | 0-4294967295 bytes | 二进制形式的极大文本数据 |
| 文本类型 | `longtext` | 0-4294967295 bytes | 极大文本数据 |

##### 3. 日期时间类型

| 日期类型 | 大小 | 范围 | 格式 | 描述 | 使用建议 |
|----------|:---:|------|------|------|----------|
| `date` | 3 | 1000-01-01 至 9999-12-31 | `YYYY-MM-DD` | 日期值 | 仅需日期时使用，如生日 |
| `time` | 3 | -838:59:59 至 838:59:59 | `HH:MM:SS` | 时间值或持续时间 | 仅需时间时使用 |
| `year` | 1 | 1901 至 2155 | `YYYY` | 年份值 | 仅需年份时使用，节省空间 |
| `datetime` | 8 | 1000-01-01 00:00:00 至 9999-12-31 23:59:59 | `YYYY-MM-DD HH:MM:SS` | 混合日期和时间值 | 范围广，但占用空间大 |
| `timestamp` | 4 | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 | `YYYY-MM-DD HH:MM:SS` | 混合日期和时间值，时间戳 | 自动更新，但范围有限 |

#### 表结构的查询、修改等操作

- **查询当前数据库的所有表**：`show tables;`
- **查询表结构**：`desc 表名;`
- **查询建表语句**：`show create table 表名;`
- **添加表中字段**：`alter table 表名 add 字段名 类型（长度）【comment 注释】【约束】;`
- **修改字段名和字段类型**：`alter table 表名 modify 字段名 新数据类型（长度）`
- **修改字段名和字段类型**：`alter table 表名 change 旧字段名 新字段名 类型（长度）【comment 注释】【约束】;`
- **删除字段**：`alter table 表名 drop column 字段名;`
- **修改表名**：`rename table 表名 to 新表名;`
- **删除表**：`drop table 【if exists】表名;`

#### 扩展：MODIFY vs CHANGE 详细辨析

| 对比项 | `MODIFY` | `CHANGE` |
|--------|----------|----------|
| 能否改字段名 | ❌ 不能 | ✅ 可以 |
| 能否改数据类型 | ✅ 可以 | ✅ 可以 |
| 能否改约束（NOT NULL / DEFAULT） | ✅ 可以 | ✅ 可以 |
| 能否改字段顺序（位置） | ✅ 可以（AFTER / FIRST） | ✅ 可以（AFTER / FIRST） |
| 语法复杂度 | 简单（只需写一次字段名） | 复杂（旧名、新名都要写） |
| 不改名时 | 直接写 `modify 字段名` | 必须写两次相同字段名 |
| 推荐场景 | 只改类型 / 约束，不改名 | 需要改名，或同时改类型 + 改名 |

比如说：

```mysql
-- ========== MODIFY ==========
-- 1. 只改数据类型
alter table tb_emp modify password varchar(30);
-- 2. 改数据类型 + 约束（NOT NULL）
alter table tb_emp modify password varchar(30) not null;
-- 3. 改数据类型 + 默认值
alter table tb_emp modify age int default 0;
-- 4. 改字段位置（移到最前面）
alter table tb_emp modify id int first;
-- 5. 改字段位置（移到某个字段后面）
alter table tb_emp modify name varchar(50) after id;

-- ========== CHANGE ==========

-- 1. 只改名，不改类型（类型要重复写一遍）
alter table tb_emp change password pwd varchar(20);
-- 2. 改类型，不改名（名字要重复写两遍）
alter table tb_emp change password password varchar(30);
-- 3. 改名 + 改类型 + 改约束
alter table tb_emp change password pwd varchar(30) not null;
-- 4. 改名 + 改位置
alter table tb_emp change password pwd varchar(20) after id;
```

**使用场景说明**：

| 场景 | 推荐 | 示例 |
|------|------|------|
| 只改数据类型 | `MODIFY` | `modify age tinyint` |
| 只改约束（NOT NULL / DEFAULT） | `MODIFY` | `modify age int default 0` |
| 只改字段位置 | `MODIFY` | `modify age int after name` |
| 只改字段名（类型不变） | `CHANGE` | `change age user_age int` |
| 改名 + 改类型 | `CHANGE` | `change age user_age tinyint` |
| 同时改多种属性 | `CHANGE` | `change age user_age tinyint not null default 0 after id` |

**易错提醒**：

| 错误写法 | 原因 | 正确写法 |
|----------|------|----------|
| `alter table tb_emp modify password pwd varchar(30);` | `modify` 不能改名 | 用 `change password pwd varchar(30)` |
| `alter table tb_emp change password varchar(30);` | `change` 必须写旧名和新名 | 写 `change password password varchar(30)` |
| `alter table tb_emp change password pwd;` | `change` 必须写数据类型 | 加上类型 `varchar(30)` |

**常见约束查询**：

| 操作 | 语句 |
|------|------|
| 删除主键 | `alter table 表名 drop primary key;` |
| 添加主键 | `alter table 表名 add primary key(字段名);` |
| 删除唯一约束 | `alter table 表名 drop index 约束名;` |
| 添加唯一约束 | `alter table 表名 add constraint 约束名 unique(字段名);` |
| 删除外键 | `alter table 表名 drop foreign key 约束名;` |
| 添加外键 | `alter table 表名 add constraint 约束名 foreign key(字段名) references 主表(主键);` |
| 删除默认值 | `alter table 表名 alter column 字段名 drop default;` |
| 设置默认值 | `alter table 表名 alter column 字段名 set default 值;` |
| 删除 NOT NULL | `alter table 表名 modify 字段名 类型 null;` |
| 添加 NOT NULL | `alter table 表名 modify 字段名 类型 not null;` |

### DML 基础语句

#### insert 语句

- **指定字段添加数据**：`insert into 表名 (字段名1, 字段名2) values (值1, 值2);`
- **全部字段添加数据**：`insert into 表名 values (值1, 值2, ...);`
- **批量添加数据（指定字段）**：`insert into 表名 (字段名1, 字段名2) values (值1, 值2), (值1, 值2);`
- **批量添加数据（全部字段）**：`insert into 表名 values (值1, 值2, ...), (值1, 值2, ...);`

比如说：

```mysql
use chaiguilun;                                      -- 切换到 chaiguilun 数据库
insert into body(name, age, weight, height, BMI) values
    ('罗隆涛', 19, 78.50, 1.71, 26.8),
    ('柴桂伦', 19, 58.50, 1.77, 18.6),
    ('唐宏', 20, 66.00, 1.71, 22.5),
    ('夏周勤', 19, 64.00, 1.74, 21.1),
    ('孟芸霄', 20, 63, 1.78, 19.8);               -- 插入 5 条员工数据
alter table body add entry date comment '入职日期';  -- 新增一列：入职日期
update body set entry = curdate();                   -- 把所有员工的入职日期设为今天
```

#### update 语句

- **修改数据**：`update 表名 set 字段名1=值1, 字段名2=值2, ... 【where 条件】;`

比如说：

```mysql
use chaiguilun;                                         -- 切换到 chaiguilun 数据库
select * from body;                                     -- 查看表中所有数据
alter table body add id tinyint unsigned comment '编号'; -- 新增一列：编号（无符号整数）
update body set id = 1 where name = '罗隆涛';            -- 给罗隆涛设编号为 1
update body set id = 2 where name = '柴桂伦';           -- 给柴桂伦设编号为 2
update body set id = 3 where name = '唐宏';              -- 给唐宏设编号为 3
update body set id = 4 where name = '夏周勤';            -- 给夏周勤设编号为 4
update body set id = 5 where name = '孟芸霄';            -- 给孟芸霄设编号为 5
```

#### delete 语句

- **删除数据**：`delete from 表名 【where 条件】;`

**注意事项**：

- DELETE 语句的条件可以有，也可以没有。如果没有条件，则会删除整张表的所有数据。
- DELETE 语句不能删除某一个字段的值（如果要操作，可以使用 UPDATE，将该字段的值设为 NULL）。

比如说：

```mysql
use chaiguilun;                        -- 切换到 chaiguilun 数据库
delete from body where id = 1;         -- 删除 body 表中编号为 1 的记录（罗隆涛）
```

### DQL 基础语句

#### 知识点总述

| 语法关键字 | 作用 | 说明 |
|------------|------|------|
| `SELECT` | 查询字段 | 指定要查询的列名，可以用 `*` 表示全部字段 |
| `FROM` | 指定表 | 要查询的数据表名 |
| `WHERE` | 条件筛选 | 在分组**之前**筛选数据行 |
| `GROUP BY` | 分组 | 按指定字段对数据进行分组 |
| `HAVING` | 分组后筛选 | 在分组**之后**筛选数据，配合聚合函数使用 |
| `ORDER BY` | 排序 | 对结果集进行排序（`ASC` 升序 / `DESC` 降序） |
| `LIMIT` | 分页 | 限制返回的记录数，用于分页查询 |

#### 常见查询类型

| 查询类型 | 关键词 | 示例 |
|----------|--------|------|
| 基本查询 | `SELECT` / `FROM` | `select * from body;` |
| 条件查询 | `WHERE` | `select * from body where age > 19;` |
| 分组查询 | `GROUP BY` | `select age, count(*) from body group by age;` |
| 排序查询 | `ORDER BY` | `select * from body order by age desc;` |
| 分页查询 | `LIMIT` | `select * from body limit 0, 3;` |

#### select 语句

- **查询多个字段**：`select 字段1, 字段2, 字段3 from 表名;`
- **查询所有字段（通配符）**：`select * from 表名;`
- **设置别名**：`select 字段1 【as 别名1】, 字段2 【as 别名2】 from 表名;`
- **去除重复记录**：`select distinct 字段列表 from 表名;`

比如说：

```mysql
-- 不推荐（不直观，性能低）
select * from body;
-- 这样也是查询所有（显式列出所有字段，更规范）
select name, age, weight, height, BMI, entry, id from body;
-- 查询指定字段并起别名（name 显示为"姓名"，entry 显示为"入职日期"）
select name as 姓名, entry as '入职日期' from body;
-- 去重查询：查看 body 表中有哪些不同的姓名
select distinct name from body;
```

#### 条件查询

`select 字段列表 from 表名 where 条件列表;`

**比较运算符**：

| 比较运算符 | 功能 |
|------------|------|
| `>` | **大于** |
| `>=` | **大于等于** |
| `<` | **小于** |
| `<=` | **小于等于** |
| `=` | **等于** |
| `<>` 或 `!=` | **不等于** |
| `between ... and ...` | **在某个范围之内（含最小、最大值）** |
| `in(...)` | **在 `in` 之后的列表中的值，多选一** |
| `like 占位符` | **模糊匹配（`_` 匹配单个字符，`%` 匹配任意个字符）** |
| `is null` | **是 null** |

**逻辑运算符**：

| 逻辑运算符 | 功能 |
|------------|------|
| `and` 或 `&&` | **并且（多个条件同时成立）** |
| `or` 或 `||` | **或者（多个条件任意一个成立）** |
| `not` 或 `!` | **非，不是** |

比如说：

```mysql
-- ========== 基础条件查询 ==========
-- 查询姓名为"罗隆涛"的记录
select * from body where name = '罗隆涛';
-- 查询 BMI >= 20.0 的记录
select * from body where BMI >= 20.0;
-- 查询年龄为 19 岁的记录
select * from body where age = 19;

-- ========== 范围查询 ==========
-- 查询入职日期在 2026-08-01 到 2026-09-01 之间的记录（方法一：使用 and）
select * from body where entry >= '2026-08-01' and entry <= '2026-09-01';
-- 查询入职日期在 2026-08-01 到 2026-09-01 之间的记录（方法二：使用 between...and...）
select * from body where entry between '2026-08-01' and '2026-09-01';

-- ========== 多值查询 ==========
-- 查询 id 为 1 或 3 或 4 的记录（方法一：使用 or）
select * from body where id = 1 or id = 3 or id = 4;
-- 查询 id 为 1 或 3 或 4 的记录（方法二：使用 in）
select * from body where id in (1, 3, 4);

-- ========== 模糊查询（like） ==========
-- 查询姓名为两个字的记录（两个下划线，匹配两个字符）
select * from body where name like '__';
-- 查询姓名为三个字的记录（三个下划线，匹配三个字符）
select * from body where name like '___';
-- 查询姓"罗"的记录（罗% 表示以"罗"开头的任意长度）
select * from body where name like '罗%';
```

#### 聚合函数

将一列数据作为一个整体，进行纵向计算。

语法：`select 聚合函数(字段列表) from 表名;`

**常见的聚合函数**：

| 函数 | 功能 |
|------|------|
| `count` | 统计数量 |
| `max` | 最大值 |
| `min` | 最小值 |
| `avg` | 平均值 |
| `sum` | 求和 |

比如说：

```mysql
-- ========== count：统计数量 ==========
-- 统计 id 不为 NULL 的记录数
select count(id) from body;
-- 统计 entry 不为 NULL 的记录数
select count(entry) from body;
-- 统计总记录数（常量 1，等效于 count(*)）
select count(1) from body;
-- 统计总记录数（推荐写法，最直观）
select count(*) from body;

-- ========== 其他聚合函数 ==========
-- 查询最早的入职日期
select min(entry) from body;
-- 查询所有人的平均 BMI
select avg(BMI) from body;
-- 查询所有人的 BMI 总和
select sum(BMI) from body;
```

**注意事项**：

- `null` 值不参与所有聚合函数的计算。
- 统计数量可以使用：`count(*)`、`count(字段)`、`count(常量)`，推荐使用 `count(*)`。

#### 分组查询

`select 字段列表 from 表名 【where 条件】 group by 分组字段名 【having 分组后的过滤条件】;`

比如说：

```mysql
-- ========== 按年龄分组 ==========
-- 查询每个年龄的员工人数
select age, count(*) from body group by age;

-- ========== 按入职日期分组 ==========
-- 查询 2026-08-01 之后入职的员工，按入职日期分组，统计每天人数
select entry, count(*) from body where entry >= '2026-08-01' group by entry;

-- ========== 分组后筛选 ==========
-- 查询 2026-08-01 之后入职的员工，按入职日期分组，筛选出人数 >= 1 的日期
select entry, count(*) from body where entry >= '2026-08-01' group by entry having count(*) >= 1;
```

**where 与 having 区别**：

- **执行时机不同**：`where` 是分组之前进行过滤，不满足 `where` 条件，不参与分组；而 `having` 是分组之后对结果进行过滤。
- **判断条件不同**：`where` 不能对聚合函数进行判断，而 `having` 可以。

**注意事项**：

- 分组之后，查询的字段一般为**聚合函数**和**分组字段**，查询其他字段无任何意义。
- 执行顺序：**where** > **聚合函数** > **having**。

#### 排序查询

`select 字段列表 from 表名 【where 条件列表】【group by 分组字段】 order by 字段1 排序方式1, 字段2 排序方式2 ...;`

- **ASC**：升序（默认）
- **DESC**：降序

比如说：

```mysql
-- ========== 多字段排序 ==========
-- 按入职日期升序排列，入职日期相同则按年龄降序排列
select * from body order by entry asc, age desc;
-- 按多个字段排序（entry 升序、age 升序、BMI 升序、weight 降序、height 降序、id 升序）
select * from body order by entry, age, BMI, weight desc, height desc, id;
```

#### 分页查询

`select 字段列表 from 表名 limit 起始索引, 查询记录数;`

比如说：

```mysql
-- 查询第 1 页（每页 5 条）：从第 0 条开始，取 5 条
select * from body limit 0, 5;
-- 查询第 2 页（每页 5 条）：从第 5 条开始，取 5 条
select * from body limit 5, 5;
-- 查询第 3 页（每页 5 条）：从第 10 条开始，取 5 条
select * from body limit 10, 5;
```

**注意事项**：

- 起始索引从 `0` 开始，`起始索引 =（查询页码 - 1）× 每页显示记录数`。
- 分页查询是数据库的方言，不同的数据库有不同的实现，**MySQL** 中是 `LIMIT`。
- 如果查询的是第一页数据，起始索引可以省略，直接简写为 `limit 10`。

#### 综合查询

```mysql
-- 查询姓名3个字、男性、入职日期在2025-06-01到2026-09-02之间的员工
-- 按年龄升序、BMI降序、id升序排列，取第1页（每页5条）
select * from body
where name like '___'                                    -- 姓名长度为3个字
  and sex = '男'                                        -- 只查男性
  and entry between '2025-06-01' and '2026-09-02'       -- 日期范围
order by age,                                            -- 年龄升序（小→大）
         BMI desc,                                       -- BMI降序（高→低）
         id                                             -- id升序（小→大）
limit 0, 5;                                             -- 第1页，每页5条
```

#### SQL 条件判断函数 / 表达式对比

**if 和 case 的语法**：

- **if(表达式, tvalue, fvalue);**：当表达式为 true 时，取值 tvalue；当表达式为 false 时，取值 fvalue
- **case expr when value1 then result1 【when value2 then value2 ...】【else result】 end;**

**二者的说明**：

| 函数 / 表达式 | 语法结构 | 示例 | 说明 |
|--------------|----------|------|------|
| `IF()` 三元函数 | `IF(条件, 真值, 假值)` | `select if(gender=1,'男性员工','女性员工'), count(*) from tb_emp group by gender;` | MySQL 特有函数，适用于**二选一**场景，简单直观 |
| `CASE` 表达式（简单写法） | `CASE 字段 WHEN 值1 THEN 结果1 WHEN 值2 THEN 结果2 ... ELSE 默认结果 END` | `select case job when 1 then '班主任' when 2 then '讲师' else '未分配职位' end as 职位, count(*) as 人数 from tb_emp group by job;` | SQL 标准语法，适用于**等值匹配**的多分支判断。注意：`END` 不能丢 |
| `CASE` 表达式（搜索写法） | `CASE WHEN 条件1 THEN 结果1 WHEN 条件2 THEN 结果2 ... ELSE 默认结果 END` | `select name, BMI, case when BMI < 18.5 then '偏瘦' when BMI < 24 then '正常' else '超重' end as 体型 from body;` | SQL 标准语法，适用于**复杂条件判断**（支持 `>`、`<`、`LIKE`、`BETWEEN` 等）。注意：`END` 不能丢 |

**补充注意**：

| 对比项 | `IF()` | `CASE` |
|--------|--------|--------|
| 是否 SQL 标准 | ❌ 仅 MySQL 支持 | ✅ 所有数据库都支持 |
| 适用场景 | 二选一 | 多分支、复杂条件 |
| 嵌套支持 | 支持但可读性差 | 支持且可读性好 |
| 能否用在 `WHERE` | 可以 | 可以 |
| 能否用在 `GROUP BY` | 可以 | 可以 |
| 能否用在 `ORDER BY` | 可以 | 可以 |

比如说：

```mysql
-- 按年龄段分组统计员工人数（方法一）
select
    -- 使用 IF 函数判断年龄范围，返回对应的年龄段标签
    if(age < 20, '20岁以下',                                     -- 年龄小于 20 → '20岁以下'
       if(age < 30, '20-29岁',                                   -- 年龄 20-29 → '20-29岁'
          if(age < 40, '30-39岁',                                -- 年龄 30-39 → '30-39岁'
             if(age < 50, '40-49岁', '50岁以上')))) as 年龄段,  -- 年龄 40-49 → '40-49岁'，其余 → '50岁以上'
    count(*) as 人数                                             -- 统计每个年龄段的人数
from body
group by 年龄段                                                  -- 按年龄段分组
order by 年龄段;                                                 -- 按年龄段名称排序（中文按拼音首字母排）

-- 按年龄段分组统计员工人数（方法二）
select
    -- 使用 CASE 表达式判断年龄范围，返回对应的年龄段标签
    case
        when age < 20 then '20岁以下'     -- 年龄小于 20 → '20岁以下'
        when age < 30 then '20-29岁'     -- 年龄 20-29 → '20-29岁'
        when age < 40 then '30-39岁'     -- 年龄 30-39 → '30-39岁'
        when age < 50 then '40-49岁'     -- 年龄 40-49 → '40-49岁'
        else '50岁以上'                   -- 其余（50 及以上）→ '50岁以上'
    end as 年龄段,                        -- 将结果命名为"年龄段"
    count(*) as 人数                      -- 统计每个年龄段的人数
from body
group by 年龄段                          -- 按年龄段分组
order by 年龄段;                         -- 按年龄段名称排序（中文按拼音首字母）

-- 查询每个人的姓名、BMI值和体型分类（不加分组，展示明细）（写法一）
select
    name,                                 -- 姓名
    BMI,                                  -- BMI值（体重/身高²）
    -- 使用 CASE 表达式根据 BMI 数值范围判断体型
    case
        when BMI < 18.5 then '偏瘦'               -- BMI 小于 18.5 → 偏瘦
        when BMI >= 18.5 and BMI < 24 then '正常' -- 18.5 ≤ BMI < 24 → 正常
        when BMI >= 24 and BMI < 28 then '超重'   -- 24 ≤ BMI < 28 → 超重
        else '肥胖'                                -- BMI ≥ 28 → 肥胖
    end as 体型                            -- 将结果命名为"体型"
from body
order by BMI;                            -- 按 BMI 从小到大排序

-- 按 BMI 分组统计不同体型的人数（写法二）
select
    -- 使用 IF 嵌套判断 BMI 范围，返回对应的体型标签
    -- 从外到内依次判断：BMI<18.5 → 偏瘦，BMI<24 → 正常，BMI<28 → 超重，否则 → 肥胖
    if(BMI < 18.5, '偏瘦',                     -- 条件1：BMI < 18.5 返回'偏瘦'
       if(BMI < 24, '正常',                    -- 条件2（嵌套）：BMI < 24 返回'正常'
          if(BMI < 28, '超重', '肥胖')))         -- 条件3（嵌套）：BMI < 28 返回'超重'，否则'肥胖'
    as 健康指数,                               -- 将结果命名为"健康指数"
    count(*) as 人数                           -- 统计每个体型的人数
from body
group by 健康指数;                           -- 按健康指数分组
```



## 多表关系&多表查询

### 表的关系综述

| 关系类型        | 说明                                                  | 实现方案                                                     |
| --------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| **一对多(1:N)** | 部门与员工：1个部门包含多名员工；一名员工归属一个部门 | **多的一方添加外键字段**（员工表 `dept_id`），项目多用逻辑外键 |
| **一对一(1:1)** | 用户 ↔ 用户详情；一张表一条记录对应另一张表一条记录   | 任意一方添加外键，并给外键增加 `unique` 约束                 |
| **多对多(M:N)** | 学生 ↔ 课程；一个学生选多门课，一门课多名学生选       | **建立中间关系表**，存放两张主表主键作为外键                 |

###  一对多关系

* 业务需求：根据页面原型及需求文档，完成**部门**及**员工**模块的表结构设计
* 比如说：

| 维度 | 详细说明 |
| ---- | ---- |
| **关系定义** | 表A的一条记录，对应表B的多条记录；表B的一条记录，只对应表A的一条记录。 |
| **典型业务场景** | • 部门 ↔ 员工（一个部门多名员工）<br>• 商品分类 ↔ 商品（一个分类下多个商品）<br>• 用户 ↔ 订单（一个用户多个订单）<br>• 文章 ↔ 评论（一篇文章多条评论） |
| **实现方案（核心原则）** | **在“多”的一方（子表/从表）添加外键列**，指向“一”的一方（父表/主表）的主键。 |
| **建表 SQL 示例** | ```sql<br>-- 主表（一的一方）<br>CREATE TABLE dept (<br>    id INT PRIMARY KEY AUTO_INCREMENT COMMENT '部门ID',<br>    name VARCHAR(50) NOT NULL COMMENT '部门名称'<br>) COMMENT '部门表';<br><br>-- 从表（多的一方）<br>CREATE TABLE emp (<br>    id INT PRIMARY KEY AUTO_INCREMENT COMMENT '员工ID',<br>    name VARCHAR(50) NOT NULL COMMENT '员工姓名',<br>    dept_id INT COMMENT '所属部门ID（外键列，放在多的一方）'<br>) COMMENT '员工表';<br>``` |
| **物理外键（数据库级约束）写法** | ```sql<br>-- 建表时添加物理外键（强一致性，但高并发下不推荐）<br>ALTER TABLE emp ADD CONSTRAINT fk_emp_dept<br>FOREIGN KEY (dept_id) REFERENCES dept(id)<br>ON DELETE RESTRICT ON UPDATE CASCADE;<br>``` |
| **逻辑外键（应用级维护）写法** | 只保留 `dept_id` 字段，**不创建** `FOREIGN KEY` 约束。由业务代码（Service/事务）保证数据一致性。**这是目前互联网项目的主流做法**。 |
| **索引强制要求** | **必须在 `dept_id` 上创建普通索引**（`CREATE INDEX idx_emp_dept ON emp(dept_id);`），因为“按部门查员工”（`WHERE dept_id=?`）是最高频查询，无索引会导致全表扫描。 |
| **级联操作（仅物理外键生效）** | • `ON DELETE CASCADE`：删除部门时自动删除该部门所有员工（风险高，慎用）<br>• `ON DELETE SET NULL`：删除部门时，员工 `dept_id` 自动置为 NULL（保留员工数据）<br>• `ON DELETE RESTRICT` / `NO ACTION`：若部门下有员工，禁止删除该部门（最安全，推荐） |
| **业务层删除父表数据的正确姿势** | 使用逻辑外键时，删除部门前必须先检查是否还有员工关联：<br>```sql<br>-- 先查询<br>SELECT COUNT(*) FROM emp WHERE dept_id = ?;<br>-- 若无关联，再删除部门；若有，则提示“该部门下存在员工，无法删除”<br>``` |
| **常见坑点** | ① 忘记在外键列建索引，导致关联查询性能急剧下降<br>② 使用物理外键 + `CASCADE` 导致误删大量数据<br>③ 逻辑外键下，业务代码未处理孤儿数据（员工 `dept_id` 指向已删除的部门） |
| **最佳实践总结** | • 优先使用逻辑外键，由应用层保证一致性<br>• 外键列必须建索引<br>• 删除主表数据前，先校验从表是否有关联<br>• 若必须用物理外键，级联删除慎用 `CASCADE` |

* 需要解决的问题：部门的数据可以直接删除，然而还有部分员工归属于该部门，这个时候就会出现数据不完整，不一致的问题

* 解决办法，将两张表建立关联：

  * **外键约束**：

    * 外键语法：

    ```mysql
    -- 创建表时指定
    create table 表名(
        字段名  数据类型,
        ...
        [constraint] [外键名称] foreign key (外键字段名) references 主表(字段名)
    );

    -- 建完表后，添加外键
    alter table 表名 add constraint 外键名称 foreign key (外键字段名) references 主表(字段名);
    ```

    * 比如说：

      ```mysql
      ALTER TABLE emp ADD CONSTRAINT fk_emp_tbemp 
      FOREIGN KEY (tbid) REFERENCES tbemp(id)
      ON DELETE CASCADE    -- 删除部门时自动删除该部门所有员工（慎用！）
      ON UPDATE CASCADE;   -- 更新部门ID时自动同步员工表中的 tbid
      ```

      在删除的时候要注意的是：

      | 级联选项              | 说明                                      | 风险等级           |
      | --------------------- | ----------------------------------------- | ------------------ |
      | `ON DELETE CASCADE`   | 删除主表记录，自动删除从表关联记录        | ⚠️ 高危，易误删数据 |
      | `ON DELETE SET NULL`  | 删除主表记录，从表外键字段自动置为` NULL` | ⚠️ 会产生孤儿数据   |
      | `ON DELETE RESTRICT`  | 若有关联记录，禁止删除主表记录（默认）    | ✅ 最安全           |
      | `ON DELETE NO ACTION` | 与 `RESTRICT` 类似                        | ✅ 安全             |

    * 和逻辑外键的区别：

      * **逻辑外键**不在数据库层面添加` FOREIGN KEY `约束**，只保留普通字段（如 `tbid`），由**应用程序代码来保证数据的一致性。
      * 比如说：

      ```mysql
      -- 部门表
      CREATE TABLE tbemp (
          id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT COMMENT '部门ID',
          name VARCHAR(10) COMMENT '部门名称',
          createtime DATETIME COMMENT '创建时间',
          updatetime DATETIME COMMENT '更新时间'
      ) COMMENT '部门表';

      -- 员工表（只保留字段，不加 FOREIGN KEY 约束）
      CREATE TABLE emp (
          id INT PRIMARY KEY AUTO_INCREMENT COMMENT '员工ID',
          name VARCHAR(20) COMMENT '员工姓名',
          age INT COMMENT '年龄',
          birthday DATE COMMENT '生日',
          password VARCHAR(32) DEFAULT '12345' COMMENT '密码',
          phonenum CHAR(13) COMMENT '手机号',
          job VARCHAR(50) COMMENT '职位',
          gender CHAR(1) DEFAULT '男' COMMENT '性别',
          entrydate DATE COMMENT '入职日期',
          qq VARCHAR(13) COMMENT 'QQ号',
          tbid INT UNSIGNED COMMENT '所属部门ID（逻辑外键，不加约束）'
      ) COMMENT '员工表';
      ```

      * 二者的总对比：

      | 对比维度          | 物理外键                             | 逻辑外键                                   |
      | ----------------- | ------------------------------------ | ------------------------------------------ |
      | **实现方式**      | `FOREIGN KEY` 数据库约束             | 只保留字段，不加约束                       |
      | **数据一致性**    | 数据库强制保证                       | 应用层代码保证                             |
      | **增删改性能**    | ❌ 低（每次检查）                     | ✅ 高（无检查）                             |
      | **分布式支持**    | ❌ 不支持                             | ✅ 完全支持                                 |
      | **分库分表**      | ❌ 无法使用                           | ✅ 可正常使用                               |
      | **死锁风险**      | ❌ 高并发下容易死锁                   | ✅ 无死锁风险                               |
      | **表结构迁移**    | ❌ 困难                               | ✅ 灵活方便                                 |
      | **代码工作量**    | ✅ 无需额外编码                       | ❌ 需要编写校验代码                         |
      | **数据安全性**    | ✅ 极高（数据库兜底）                 | ⚠️ 依赖代码质量                             |
      | **适用场景**      | 金融、财务等强一致性、低并发单体项目 | 互联网项目、微服务、高并发系统             |
      | **推荐场景**      | 单体/低并发/强一致性要求极高         | ✅ 互联网项目/高并发/微服务/分库分表/分布式 |
      | **学习/个人项目** | 可以尝试                             | 可以尝试                                   |

### 一对一关系

* 业务需求：`用户`与`身份证信息`的关系
* 关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他字段放在另一张表中，以提升操作效率

| 概念 | 说明 |
| ---- | ---- |
| **关系定义** | 表A的一条记录**唯一对应**表B的一条记录，反之亦然 |
| **方向性** | 双向唯一对应，不存在"一对多"的情况 |
| **实现核心** | 在任意一方添加外键，指向另一方主键，并给外键加上 **`UNIQUE`** 约束 |
| **与一对多的区别** | 一对多外键列**不加** `UNIQUE`（允许重复）；一对一外键列**必须加** `UNIQUE`（不允许重复） |


* 实现：在任意一方加入外键，关联另一方的外键，并且设置外键为`唯一的`（**UNIQUE**）

  比如说：

  ```mysql
  -- ==================== 主表（user）====================
  CREATE TABLE user (
      id          INT UNSIGNED PRIMARY KEY AUTO_INCREMENT COMMENT '用户ID（主键）',
      username    VARCHAR(20)  NOT NULL UNIQUE COMMENT '用户名',
      name        VARCHAR(10)  NOT NULL COMMENT '真实姓名',
      gender      CHAR(1)      DEFAULT '男' COMMENT '性别',
      phonenumber VARCHAR(13)  NOT NULL COMMENT '手机号'
  ) COMMENT '用户基本信息表（主表）';

  -- ==================== 从表（user_detail）====================
  -- 方案一：外键 + UNIQUE 约束（最常用）
  CREATE TABLE user_detail (
      id           INT UNSIGNED PRIMARY KEY AUTO_INCREMENT COMMENT '详情ID',
      user_id      INT UNSIGNED UNIQUE NOT NULL COMMENT '用户ID（外键 + 唯一约束）',
      birthday     DATE COMMENT '出生日期',
      idcard       VARCHAR(18) COMMENT '身份证号',
      address      VARCHAR(200) COMMENT '住址',
      -- 添加物理外键（可选，一般用逻辑外键）
      CONSTRAINT fk_user_detail_user FOREIGN KEY (user_id) REFERENCES user(id)
  ) COMMENT '用户详情表（从表）';

  -- ==================== 方案二：主键即外键（性能更优）====================
  -- 从表的主键直接引用主表的主键，天然保证 1:1
  CREATE TABLE user_detail (
      user_id      INT UNSIGNED PRIMARY KEY COMMENT '用户ID（主键，同时是外键）',
      birthday     DATE COMMENT '出生日期',
      idcard       VARCHAR(18) COMMENT '身份证号',
      address      VARCHAR(200) COMMENT '住址'
      -- 物理外键：可选
      -- CONSTRAINT fk_user_detail_user FOREIGN KEY (user_id) REFERENCES user(id)
  ) COMMENT '用户详情表（从表，主键即外键）';
  ```

### 多对多关系

* 业务需求：学生与课程的关系（即一个学生可以选修多门课程，一门课程也可以供多个学生选择）

| 概念 | 说明 |
| ---- | ---- |
| **关系定义** | 表A的一条记录对应表B的多条记录，同时表B的一条记录也对应表A的多条记录 |
| **实现核心** | 必须创建**中间表（关联表）**，存放两张主表的主键作为外键 |
| **与一对多的区别** | 一对多只需要在"多"方加一个外键列；多对多需要单独建一张中间表 |


* 所以我们要建立三张表：

| 表名 | 字段 | 说明 |
| ---- | ---- | ---- |
| `student` | `id`（主键）, `name`, `studentid` | 学生表 ✅ |
| `course` | `id`（主键）, `course_name` | 课程表 ✅ |
| `s_link_c` | `id`（主键）, `sid`（外键）, `cid`（外键） | 中间表 ✅ |

关系图如下：

| 表名       | 键名        | 键类型      | 字段        | 关联目标       |
| ---------- | ----------- | ----------- | ----------- | -------------- |
| `student`  | `PRIMARY`   | 主键 (PK)   | `id`        | —              |
| `student`  | `studentid` | 唯一键 (UK) | `studentid` | —              |
| `course`   | `PRIMARY`   | 主键 (PK)   | `id`        | —              |
| `s_link_c` | `PRIMARY`   | 主键 (PK)   | `id`        | —              |
| `s_link_c` | `fk_sc_sid` | 外键 (FK)   | `sid`       | → `student.id` |
| `s_link_c` | `fk_sc_cid` | 外键 (FK)   | `cid`       | → `course.id`  |

即：

```bash
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│  student (学生表)                    course (课程表)                          │
│  ┌───────────────────────┐          ┌───────────────────────┐               │
│  │ PK : id               │          │ PK : id               │               │
│  │ UK : studentid        │          │                       │               │
│  │     name              │          │     course_name       │               │
│  └──────────┬────────────┘          └──────────┬────────────┘               │
│             │                                  │                            │
│             │ 1                                │ 1                          │
│             ▼                                  ▼                            │
│  ┌─────────────────────────────────────────────────────┐                    │
│  │              s_link_c (中间表)                       │                    │
│  │  ┌─────────────────────────────────────────────┐    │                    │
│  │  │ PK : id                                    │     │                    │
│  │  │ FK : sid  ────────────▶ student.id         │     │                    │
│  │  │ FK : cid  ────────────▶ course.id          │     │                    │
│  │  └─────────────────────────────────────────────┘    │                    │
│  └─────────────────────────────────────────────────────┘                    │
│                                                                             │
│  索引：                                                                      │
│  ┌─────────────────────────────────────────────────────┐                    │
│  │  idx_sid  (普通索引)   → 加速 WHERE sid = ? 查询   │                        │
│  │  idx_cid  (普通索引)   → 加速 WHERE cid = ? 查询   │                        │
│  └─────────────────────────────────────────────────────┘                    │
└─────────────────────────────────────────────────────────────────────────────┘
```

建表语句如下：

```mysql
-- ==================== 创建数据库 ====================
CREATE DATABASE IF NOT EXISTS school;
USE school;

-- ==================== 1. 学生表 ====================
CREATE TABLE student (
    id        INT UNSIGNED NOT NULL PRIMARY KEY COMMENT '学生ID（主键）',
    name      VARCHAR(50)  NOT NULL COMMENT '学生姓名',
    studentid INT UNSIGNED NOT NULL UNIQUE COMMENT '学号（唯一键）'
) COMMENT '学生表';

-- ==================== 2. 课程表 ====================
CREATE TABLE course (
    id          INT UNSIGNED NOT NULL PRIMARY KEY COMMENT '课程ID（主键）',
    course_name VARCHAR(50)  NOT NULL COMMENT '课程名称'
) COMMENT '课程表';

-- ==================== 3. 中间表（关联表） ====================
CREATE TABLE s_link_c (
    id  INT UNSIGNED NOT NULL PRIMARY KEY COMMENT '关联记录ID（主键）',
    sid INT UNSIGNED NOT NULL COMMENT '学生ID（外键）',
    cid INT UNSIGNED NOT NULL COMMENT '课程ID（外键）',
    -- 业务上建议加联合唯一键，防止同一学生重复选同一门课
    -- UNIQUE KEY uk_sid_cid (sid, cid),
    -- 加普通索引加速反向查询（查某课程被哪些学生选了）
    -- INDEX idx_cid (cid)
) COMMENT '学生-课程关联表（中间表）';

-- ==================== 4. 添加物理外键（可选） ====================
-- 如果要用物理外键，取消下面注释执行
-- ALTER TABLE s_link_c ADD CONSTRAINT fk_sc_sid FOREIGN KEY (sid) REFERENCES student(id);
-- ALTER TABLE s_link_c ADD CONSTRAINT fk_sc_cid FOREIGN KEY (cid) REFERENCES course(id);
-- ALTER TABLE s_link_c ADD UNIQUE KEY uk_sid_cid (sid, cid);
-- CREATE INDEX idx_cid ON s_link_c(cid);
```



### 多表关系&三种关系总结

* 表格总结：


| 对比维度 | 一对多 (1:N) | 一对一 (1:1) | 多对多 (M:N) |
| ---- | ---- | ---- | ---- |
| **关系说明** | 部门 ↔ 员工 | 用户 ↔ 用户详情 | 学生 ↔ 课程 |
| **外键位置** | “多”方表（从表） | 任意一方（通常从表） | 中间表 |
| **外键数量** | 1 个 | 1 个 | 2 个 |
| **是否需要 UNIQUE** | ❌ 不需要 | ✅ **必须加** | 联合主键防重 |
| **是否需要中间表** | ❌ 不需要 | ❌ 不需要 | ✅ **必须需要** |
| **外键值是否可重复** | ✅ 可重复 | ❌ 不可重复 | 联合主键保证不重复 |
| **关系属性存储位置** | “多”方表 | 从表 | 中间表 |
| **索引建议** | 外键列建普通索引 | 主键即外键，性能最优 | 联合主键 + 反向外键单独建索引 |
| **物理外键推荐度** | 不推荐（性能差） | 不推荐 | 不推荐 |
| **逻辑外键推荐度** | ✅ 强烈推荐 | ✅ 强烈推荐 | ✅ 强烈推荐 |
| **典型场景** | 部门-员工、分类-商品 | 用户-详情、商品-参数 | 学生-课程、用户-角色 |

**关系表**如下：
* **一对多 (1:N)**：

| 主表 | 关系 | 从表 | 说明 |
| ---- | ---- | ---- | ---- |
| `dept` | **1 : N** | `emp` | 一个部门有多名员工 |


| 表名 | 字段 | 键类型 | 说明 |
| ---- | ---- | ---- | ---- |
| `dept` | `id` | `PK` | 部门ID |
| `dept` | `name` | — | 部门名称 |
| `emp` | `id` | PK | 员工ID |
| `emp` | `name` | — | 员工姓名 |
| `emp` | `dept_id` | FK | 所属部门（外键 → dept.id） |
  * **一对一 (1:1)**：
| 主表 | 关系 | 从表 | 说明 |
| ---- | ---- | ---- | ---- |
| `user` | **1 : 1** | `user_detail` | 一个用户对应一条详情 |

| 表名 | 字段 | 键类型 | 说明 |
| ---- | ---- | ---- | ---- |
| `user` | `id` | `PK` | 用户ID |
| `user` | `username` | — | 用户名 |
| `user_detail` | `id` | `PK` | 详情ID |
| `user_detail` | `user_id` | `UK + FK` | 用户ID（唯一，外键 → user.id） |
| `user_detail` | `address` | — | 地址 |
  * **多对多 (M:N)**：

| 主表1 | 关系 | 中间表 | 关系 | 主表2 | 说明 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| `student` | **M : N** | `student_course` | **M : N** | `course` | 学生与课程多对多 |

| 表名 | 字段 | 键类型 | 说明 |
| ---- | ---- | ---- | ---- |
| `student` | `id` | `PK` | 学生**ID** |
| `student` | `name` | — | 学生姓名 |
| `course` | `id` | `PK` | 课程**ID** |
| `course` | `course_name` | — | 课程名称 |
| `student_course` | `sid` | `FK` | 学生**ID**（外键 → student.id） |
| `student_course` | `cid` | `FK` | 课程**ID**（外键 → course.id） |
| `student_course` | `score` | — | 成绩（关系属性） |

### 实践一下

* 题目描述

  某教育培训机构需要设计**一套数据库系统**，包含以下业务：

     $1.$每个学生属于**一个**班级，一个班级有**多名学生**

     $2.$每个学生有**一个**学习档案（包含入学成绩、家长联系方式等）

     $3.$每个学生可以选**多门课程**，每门课程可以被多名学生选择，选课需要**记录考试成绩**

* 设计表结构：

  * 首先思考的思路是：
  对于这里，其实最直接的是去理清关系，对业务就会有更清晰的理解，在这里关系如下：
  
| 实体 A | 实体 B | 关系判断 | 判断依据 |
| ---- | ---- | ---- | ---- |
| 班级 | 学生 | **一对多** ($1:N$) | 一个班级有多个学生，一个学生只属于一个班级 |
| 学生 | 学习档案 | **一对一** ($1:1$) | 一个学生只有一个档案，一个档案只属于一个学生 |
| 学生 | 课程 | **多对多** ($M:N$) | 一个学生选多门课，一门课被多个学生选 |

  然后确定了关系后就可以着手建表：

  ```mysql
-- ============================================================
-- 数据库：xiazhouqing
-- 说明：以下为完整建表语句，包含三种关系：
--       一对多 (班级 ↔ 学生)、一对一 (学生 ↔ 档案)、多对多 (学生 ↔ 课程)
-- ============================================================

-- ============================================================
-- 1. 班级表 (class)
-- 作用：存放班级基本信息，作为"一"的一方
-- 关系：与 student 表构成 一对多 (1:N) 关系
--       一个班级可以对应多个学生
-- ============================================================
use xiazhouqing;
create table class(
    id    tinyint unsigned not null primary key auto_increment comment '班级编号（主键，自增）',
    name  varchar(50)      not null comment '班级名称，如：一班、二班',
    grade varchar(50)      not null comment '年级，如：高一、高二'
) comment '班级表 —— 存放班级基本信息（一对多中的"一"）';


-- ============================================================
-- 2. 学生表 (student)
-- 作用：存放学生基本信息，作为"多"的一方
-- 关系1：与 class 表构成 一对多 (1:N) 关系
--        通过 cid 外键指向 class.id，表示所属班级
-- 关系2：与 sprofile 表构成 一对一 (1:1) 关系
--        被 sprofile.sid 引用
-- 关系3：与 course 表通过 s_linked_c 中间表构成 多对多 (M:N) 关系
-- ============================================================
use xiazhouqing;
create table student(
    id       tinyint unsigned not null primary key auto_increment comment '学生编号（主键，自增）',
    name     varchar(20)      not null comment '学生姓名',
    gender   char(1)          default '男' comment '学生性别，默认：男',
    birthday date comment '学生出生日期，格式：YYYY-MM-DD',
    cid      tinyint unsigned comment '所属班级编号（外键），指向 class.id'
) comment '学生表 —— 存放学生基本信息（一对多中的"多"，也作为一对一和多对多的关联方）';


-- ============================================================
-- 3. 学习档案表 (sprofile)
-- 作用：存放学生的扩展信息（入学成绩、家长电话等）
-- 关系：与 student 表构成 一对一 (1:1) 关系
--       通过 sid 外键指向 student.id，且 sid 加了 UNIQUE 约束保证一对一
-- ============================================================
use xiazhouqing;
create table sprofile(
    id       tinyint unsigned primary key auto_increment comment '档案编号（主键，自增）',
    sid      tinyint unsigned unique not null comment '学生编号（外键 + 唯一约束），指向 student.id',
    score    double(3,1)      not null comment '学生入学成绩，如：95.5',
    phonenum char(13)         not null comment '学生家长联系电话，如：13800000001',
    remark   varchar(500) comment '备注信息，如：品学兼优、数学特长等'
) comment '学习档案表 —— 存放学生扩展信息（一对一中的"从表"，sid 唯一）';


-- ============================================================
-- 4. 课程表 (course)
-- 作用：存放课程基本信息
-- 关系：与 student 表通过 s_linked_c 中间表构成 多对多 (M:N) 关系
--       一门课程可以被多名学生选择
-- ============================================================
use xiazhouqing;
create table course(
    id         tinyint unsigned not null primary key auto_increment comment '课程编号（主键，自增）',
    coursename varchar(50)      not null comment '课程名称，如：语文、数学、英语',
    credit     tinyint unsigned default 1 comment '学分数，默认：1',
    teacher    varchar(25)      not null comment '任课老师姓名'
) comment '课程表 —— 存放课程基本信息（多对多中的主表之一）';


-- ============================================================
-- 5. 选课成绩表 (s_linked_c)  —— 中间表（关系表）
-- 作用：记录学生选课及考试成绩，是实现多对多的桥梁
-- 关系：与 student 表、course 表构成 多对多 (M:N) 关系
--       联合主键 (sid, cid) 防止同一学生重复选同一门课
--       额外字段 score、create_time 存储"关系属性"
-- ============================================================
use xiazhouqing;
create table s_linked_c(
    sid         tinyint unsigned not null comment '学生编号（外键），指向 student.id',
    cid         tinyint unsigned not null comment '课程编号（外键），指向 course.id',
    score       double(3,1)      not null comment '考试成绩，如：92.5',
    create_time datetime         default current_timestamp comment '选课时间，默认当前时间',
    primary key (sid, cid) comment '联合主键，防止重复选课'
) comment '选课成绩表 —— 中间表，记录学生选课及成绩（多对多关系表，含关系属性）';
  ```

在建立了数据表后，就知道他们的关系和作用了：

* 表格图：


| 表名 | 作用 | 关系类型 | 关联说明 |
| ---- | ---- | ---- | ---- |
| `class` | 存放班级信息 | `一对多` (**1:N**) | 作为"一"方，被 `student.cid` 引用 |
| `student` | 存放学生信息 | `一对多` (**1:N**) | 作为"多"方，通过 `cid` 指向 `class` |
| `sprofile` | 存放学生档案 | `一对一` (**1:1**) | 通过 `sid` 指向 `student.id`，加 `UNIQUE` |
| `course` | 存放课程信息 | `多对多` (**M:N**) | 作为主表之一，被中间表引用 |
| `s_linked_c` | 选课成绩（中间表） | `多对多` (**M:N**) | 存放 `sid`、`cid` 及关系属性（成绩） |

* 关系图：

```
class (班级表)                    sprofile (学习档案表)
┌──────────────┐                 ┌─────────────────────┐
│ id (PK)      │                 │ id (PK)             │
│ name         │                 │ sid (UK,FK) ────────┤
│ grade        │                 │ score               │
└──────┬───────┘                 │ phonenum            │
       │                         │ remark              │
       │ 1                       └─────────────────────┘
       │ ∫
       │ N
       ▼
student (学生表)                  course (课程表)
┌──────────────────────┐         ┌─────────────────────┐
│ id (PK)              │         │ id (PK)             │
│ name                 │         │ coursename          │
│ gender               │         │ credit              │
│ birthday             │         │ teacher             │
│ cid (FK) ────────────┘         └──────────┬──────────┘
└──────────┬───────────┘                    │
           │ 1                              │ 1
           │ ∫                              │ ∫
           │ N                              │ N
           ▼                                ▼
┌──────────────────────────────────────────────────────┐
│              s_linked_c (中间表)                      │
│  ┌────────────────────────────────────────────┐      │
│  │ sid (PK, FK) ──→ student.id                │      │
│  │ cid (PK, FK) ──→ course.id                 │      │
│  │ score                                      │      │
│  │ create_time                                │      │
│  └────────────────────────────────────────────┘      │
└──────────────────────────────────────────────────────┘
```



然后实现**主键**和**外键**的连接：

```mysql
-- 1. 学生表 → 班级表（一对多）
ALTER TABLE student ADD CONSTRAINT fk_student_class
FOREIGN KEY (cid) REFERENCES class(id);

-- 2. 档案表 → 学生表（一对一）
ALTER TABLE sprofile ADD CONSTRAINT fk_sprofile_student
FOREIGN KEY (sid) REFERENCES student(id);

-- 3. 选课中间表 → 学生表（多对多）
ALTER TABLE s_linked_c ADD CONSTRAINT fk_slc_student
FOREIGN KEY (sid) REFERENCES student(id);

-- 4. 选课中间表 → 课程表（多对多）
ALTER TABLE s_linked_c ADD CONSTRAINT fk_slc_course
FOREIGN KEY (cid) REFERENCES course(id);
```



###  多表查询

#### 前言

* 我们知道**select * from 表名;** 可以查询一个表内的所有内容，那么我们是不是可以**select * from 表名，表名...表名;** 来实现**多表查询** 呢，来实践一下：

  * 首先先建表并插入内容：

  ```mysql
  use test;
  create table tb_dept
  (
      id          int unsigned primary key auto_increment comment 'ID',
      name        varchar(10) not null unique comment '部门名称',
      create_time datetime    not null comment '创建时间',
      update_time datetime    not null comment '修改时间'
  ) comment '部门表';

  -- 插入5条部门数据（id自动生成：1、2、3、4、5，和员工dept_id对应）
  insert into tb_dept (name, create_time, update_time)
  values ('学工部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('教研部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('咨询部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('就业部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('人事部', '2022-10-30 14:32:02', '2022-10-30 14:32:02');

  -- 2.创建【子表】员工表，并添加外键约束
  create table tb_emp
  (
      id          int auto_increment comment '主键唯一标识'
          primary key,
      username    varchar(20)                  not null comment '用户名',
      password    varchar(20)                  null comment '密码',
      name        varchar(20)                  not null comment '姓名',
      gender      tinyint unsigned default '1' null comment '性别  1男 2女',
      image       varchar(300)                 null comment '图片 路径 url',
      job         tinyint unsigned             null comment '职位 1普通员工 2专员 3主管 4部门负责人',
      entry_date  date                         null comment '入职日期',
      dept_id     int unsigned comment '归属部门的ID',
      create_time datetime                     not null comment '创建时间',
      update_time datetime                     not null comment '修改时间',
      constraint `uk_emp_username`
          unique (username)
      -- 外键约束：员工dept_id 关联部门表id   物理外键不要       通常使用逻辑外键
  #     constraint `fk_emp_dept_id` foreign key (dept_id) references tb_dept(id)
  )
      comment '员工表';

  -- 3.最后插入员工数据
  INSERT INTO tb_emp (username, password, name, gender, image, job, entry_date, dept_id, create_time, update_time)
  VALUES ('jinyong', '123456', '金庸', 1, '1.jpg', 4, '2000-01-01', 1, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('zhangwuji', '123456', '张无忌', 1, '2.jpg', 4, '2015-01-01', 2, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('yangxiao', '123456', '杨逍', 1, '3.jpg', 2, '2008-05-01', 2, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('weiyixiao', '123456', '韦一笑', 1, '4.jpg', 2, '2007-01-01', 3, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('changyuchun', '123456', '常遇春', 1, '5.jpg', 2, '2012-12-05', 3, '2022-10-30 14:32:02',
          '2022-10-30 14:32:02'),
         ('xiaozhao', '123456', '小昭', 2, '6.jpg', 4, '2013-09-05', 4, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('jixiaofu', '123456', '纪晓芙', 2, '7.jpg', 1, '2005-08-01', 1, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('zhouzhiruo', '123456', '周芷若', 2, '8.jpg', 1, '2014-11-09', 1, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('dingminjun', '123456', '丁敏君', 2, '9.jpg', 1, '2011-03-11', 4, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('zhaomin', '123456', '赵敏', 2, '10.jpg', 4, '2013-09-05', 5, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('luzhangke', '123456', '鹿杖客', 1, '11.jpg', 1, '2007-02-01', 5, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('hebiweng', '123456', '鹤笔翁', 1, '12.jpg', 1, '2008-08-18', 5, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
         ('fangdongbai', '123456', '方东白', 1, '13.jpg', 2, '2012-11-01', 2, '2022-10-30 14:32:02',
          '2022-10-30 14:32:02'),
         ('zhangsanfeng', '123456', '张三丰', 1, '14.jpg', 4, '2002-08-01', 3, '2022-10-30 14:32:02',
          '2022-10-30 14:32:02'),
         ('yulianzhou', '123456', '俞莲舟', 1, '15.jpg', 2, '2011-05-01', 3, '2022-10-30 14:32:02',
          '2022-10-30 14:32:02'),
         ('songyuanqiao', '123456', '宋远桥', 1, '16.jpg', 2, '2010-01-01', 4, '2022-10-30 14:32:02',
          '2022-10-30 14:32:02'),
         ('chenyouliang', '123456', '陈友谅', 1, '17.jpg', NULL, '2015-03-21', NULL, '2022-10-30 14:32:02',
          '2022-10-30 14:32:02');
  ```

  * 然后运行：

  ```mysql
  use test;
  select * from tb_emp,tb_dept;
  ```

  * 最后得到结果：**tb_emp**表中的每个内容都被显示了5次，这是为啥呢？

     原因就在于这是**笛卡尔积** ：

  相当于把两张表强行拼在一起，没有加任何关联条件。**MySQL** 的做法是：

  > 拿 `tb_emp` 的每一行，去匹配 `tb_dept` 的**所有行**。

  | `tb_emp` 行数 | × | `tb_dept` 行数 | = | 结果行数 |
  | ---- | ---- | ---- | ---- | ---- |
  | 17 行 | `×` | `5 行` | `=` | **85 行** |

  所以每个员工会出现 5 次，分别配上 `tb_dept` 的 5 个部门（学工部、教研部、咨询部、就业部、人事部）。

* 所以正确的做法是：


```mysql
select *
from tb_emp, tb_dept
where tb_emp.dept_id = tb_dept.id;
```

或者用标准的 **JOIN 写法**（更推荐）：

```mysql
select *
from tb_emp
join tb_dept on tb_emp.dept_id = tb_dept.id;
```

这样查询结果就是 **员工 + 他所属部门的名称**，而不是乱拼的笛卡尔积

而对于以上查询，可以总结为以下几点：

- **连接查询**
  - **内连接**：相当于查询 A、B 交集部分数据

    * **隐式内连接**：**select 字段列表 from 表1，表2，where 条件...;**

    比如：

    ```mysql
    select tb_emp.name,tb_dept.name from tb_emp,tb_dept where tb_emp.dept_id=tb_dept.id;
    ```

    * **显式内连接**：**select 字段列表 from 表1【inner】 join 表2 on 连接条件...;** 

    比如：

    ```mysql
    select tb_emp.name,tb_dept.name from tb_emp join tb_dept on tb_emp.dept_id = tb_dept.id;
    ```

  - **外连接**

    - **左外连接**：查询左表所有数据（包括两张表交集部分数据）,语法：**select 字段列表 from 表1 left 【outer】 join 表2 on 连接条件...;** 

    比如：

    ```mysql
    -- 查询所有员工，关联所属部门（无部门员工依然展示）
    select e.name,d.name from tb_emp e left join tb_dept d on e.dept_id = d.id;
    ```

    - **右外连接**：查询右表所有数据（包括两张表交集部分数据）,语法：**select 字段列表 from 表1 right 【outer】 join 表2 on 连接条件...;** 

    比如：

    ```mysql
    -- 查询所有部门，关联对应员工（没有员工的部门依然展示）
    select e.name,d.name from tb_emp e right join tb_dept d on e.dept_id=d.id;
    ```

    - 总述：

      | SQL | 连接类型 | 结果说明 |
      | ---- | ---- | ---- |
      | 第一条 | 左外连接 (LEFT JOIN) | 以 `tb_emp`（左表）为主，所有员工都会出现，没有部门的员工（`dept_id = NULL`）也会列出，部门名显示为 `NULL` |
      | 第二条 | 右外连接 (RIGHT JOIN) | 以 `tb_dept`（右表）为主，所有部门都会出现，即使没有员工的部门也会列出，员工名显示为 `NULL` |
      

  - **内外连接的区别和应用场景**：

  | 连接类型     | 关键字                                | 结果范围                            | 主表       | 未匹配到的数据如何处理          | 结果行数                    | 能否相互转换                       | 典型应用场景                                       | 举例SQL                                                      |
  | ------------ | ------------------------------------- | ----------------------------------- | ---------- | ------------------------------- | --------------------------- | ---------------------------------- | -------------------------------------------------- | ------------------------------------------------------------ |
  | **内连接**   | `INNER JOIN`（可省略 `INNER`）        | 只返回两张表**能匹配上**的数据      | 无主次之分 | 直接丢弃，不返回                | 行数 ≤ 左表行数，≤ 右表行数 | 不可转换                           | 查询**必须有关联关系**的数据，不需要看空值         | `select * from emp join dept on emp.dept_id = dept.id;`      |
  | **左外连接** | `LEFT JOIN`（或 `LEFT OUTER JOIN`）   | 返回**左表全部** + 右表匹配到的数据 | 左表是主表 | 保留左表数据，右表字段填 `NULL` | 行数 ≥ 左表行数             | `A LEFT JOIN B` = `B RIGHT JOIN A` | 以左表为主，想看左表**全部**数据，哪怕右边没有匹配 | `select * from emp left join dept on emp.dept_id = dept.id;` |
  | **右外连接** | `RIGHT JOIN`（或 `RIGHT OUTER JOIN`） | 返回**右表全部** + 左表匹配到的数据 | 右表是主表 | 保留右表数据，左表字段填 `NULL` | 行数 ≥ 右表行数             | `A RIGHT JOIN B` = `B LEFT JOIN A` | 以右表为主，想看右表**全部**数据，哪怕左边没有匹配 | `select * from emp right join dept on emp.dept_id = dept.id;` |

- **子查询**

  * 顾名思义：子查询就是SQL语句中嵌套select 语句，称为嵌套查询，又称为子查询
  * 语法格式：**select * from t1 where column1 =(select column1 from t2...);**
  * 注意：子查询的外部语句可以是**insert**/**update**/**select** 的任意一个 ，最常见的是**select**
  * 子查询的分类：

  | 子查询类型     | 返回结果             | 特点                                           | 常见运算符                                  |
  | -------------- | -------------------- | ---------------------------------------------- | ------------------------------------------- |
  | **标量子查询** | 单个值（一行一列）   | 返回一个具体值，如：一个数字、一个字符串       | `=`、`>`、`<`、`>=`、`<=`、`<>`             |
  | **列子查询**   | 一列数据（一列多行） | 返回一列值，常用于 `IN`、`ANY`、`ALL` 判断     | `IN`、`NOT IN`、`ANY`、`SOME`、`ALL`        |
  | **行子查询**   | 一行数据（一行多列） | 返回一条记录的多个字段，用于行级比较           | `=`、`<>`、`IN`，`not in`（需匹配多个字段） |
  | **表子查询**   | 多行多列（一张表）   | 返回一张临时表，常用于 `FROM` 子句中作为数据源 | 通常用于 `FROM` 子句，配合别名使用          |

  *   比如说：

  ```mysql
  -- 查询教研部所有员工(标量子查询，返回单个值)
  select * from tb_emp 
  where dept_id = (select id from tb_dept where name='教研部');

  -- 查询教研部、咨询部员工(列子查询，返回一列多行，搭配in)
  select * from tb_emp 
  where dept_id in (select id from tb_dept where name='教研部' or name='咨询部');

  -- 查询和韦一笑入职日期、职位完全相同的员工(行子查询，返回一行多列)
  select * from tb_emp 
  where (entry_date,job) = (select entry_date,job from tb_emp where name='韦一笑');

  -- 查询2005-08-01之后入职员工，并关联部门名称(表子查询，返回多行多列，当作临时表使用)
  select e.*,d.name
  from (select * from tb_emp where entry_date > '2005-08-01') e
  join tb_dept d on e.dept_id=d.id;
  ```

**小总结**：


| 写法 | 结果 | 说明 |
| ---- | ---- | ---- |
| `select * from A, B;` | ❌ 笛卡尔积 | 无关联条件，数据全乱拼，基本没意义 |
| `select * from A, B where A.xxx = B.xxx;` | ✅ 正确 | `老式写法`，能查出关联数据 |
| `select * from A join B on A.xxx = B.xxx;` | ✅ 正确 | `标准写法`，推荐使用 |

| 分类         | 连接类型   | 关键字                                | 结果范围                            | 主表       | 未匹配数据                      | 典型应用场景                                                 | SQL示例                                                      |
| ------------ | ---------- | ------------------------------------- | ----------------------------------- | ---------- | ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **连接查询** | 内连接     | `INNER JOIN`（可省略 `INNER`）        | 只返回两张表**能匹配上**的数据      | 无主次之分 | 直接丢弃                        | 查询必须有关联关系的数据，如：查询有部门归属的员工           | `select * from student s join class c on s.cid = c.id;`      |
| **连接查询** | 左外连接   | `LEFT JOIN`（或 `LEFT OUTER JOIN`）   | 返回**左表全部** + 右表匹配到的数据 | 左表是主表 | 保留左表数据，右表字段填 `NULL` | 以左表为主，想看左表全部数据，如：查询所有员工及其部门（含无部门员工） | `select * from student s left join class c on s.cid = c.id;` |
| **连接查询** | 右外连接   | `RIGHT JOIN`（或 `RIGHT OUTER JOIN`） | 返回**右表全部** + 左表匹配到的数据 | 右表是主表 | 保留右表数据，左表字段填 `NULL` | 以右表为主，想看右表全部数据，如：查询所有部门及其员工（含无人部门） | `select * from student s right join class c on s.cid = c.id;` |
| **子查询**   | 标量子查询 | `=`、`>`、`<`、`>=`、`<=`、`<>`       | 返回单个值（一行一列）              | —          | —                               | 查询比罗隆涛年龄更大的学生                                   | `select * from student where birthday < (select birthday from student where name = '罗隆涛');` |
| **子查询**   | 列子查询   | `IN`、`NOT IN`、`ANY`、`SOME`、`ALL`  | 返回一列数据（一列多行）            | —          | —                               | 查询属于一班或二班的学生                                     | `select * from student where cid in (select id from class where name in ('一班', '二班'));` |
| **子查询**   | 行子查询   | `=`、`<>`、`IN`（多字段匹配）         | 返回一行数据（一行多列）            | —          | —                               | 查询和罗隆涛同班且同生日的学生                               | `select * from student where (cid, birthday) = (select cid, birthday from student where name = '罗隆涛');` |
| **子查询**   | 表子查询   | 通常用于 `FROM` 子句，配合别名使用    | 返回多行多列（一张临时表）          | —          | —                               | 统计每个班的平均年龄                                         | `select * from (select id, name from student) as t where t.id > 1;` |

####  实践一下

$1$.你接手了一个培训机构的数据库，里面有以下 4 张表（结构见下方）。现在教务主任让你帮他出一份数据统计报告，他需要拿到 **11 个问题的答案**，请你根据他的口述需求写出对应的 SQL。

$2$.结构表参考：

```mysql
-- 班级表 (class)
class.id        → 班级编号
class.name      → 班级名称（一班、二班…）
class.grade     → 年级（高一、高二）

-- 学生表 (student)
student.id      → 学生编号
student.name    → 学生姓名
student.cid     → 所属班级编号（外键 → class.id）

-- 课程表 (course)
course.id       → 课程编号
course.coursename → 课程名称
course.teacher  → 任课老师

-- 选课成绩表 (s_linked_c)
s_linked_c.sid  → 学生编号（外键 → student.id）
s_linked_c.cid  → 课程编号（外键 → course.id）
s_linked_c.score → 考试成绩
```

$3$.问题需求：

- **需求一（常规名单类）**

  $1$."帮我把所有学生和他们的班级信息列出来，我要看每个人属于哪个班。"

  ```mysql
  -- ============================================================
  -- 查询所有学生及其班级信息
  -- ============================================================
  select s.id, s.name, s.gender, s.birthday, c.name as class_name, c.grade
  from student s
  join class c on s.cid = c.id;
  ```

  ​

  $2$."有些学生还没分班，但我也要看他们，名字别漏掉了。"

  ```mysql
  -- ============================================================
  -- 查询所有学生及其班级信息（含未分班的学生）
  -- ============================================================
  select s.id, s.name, s.gender, s.birthday, c.name as class_name, c.grade
  from student s
  left join class c on s.cid = c.id;
  ```

  ​

  $3$."反过来，我要看每个班底下有哪些学生，一个都不能少，空班也要显示。"

  ```mysql
  -- ============================================================
  -- 查询所有班级及其学生（空班也要显示）
  -- ============================================================
  select c.id, c.name as class_name, c.grade, s.id as student_id, s.name as student_name
  from class c
  left join student s on c.id = s.cid
  order by c.id;
  ```

  ​

- **需求二（比较类)**

  $1$."查一下比罗隆涛出生日期更早的学生有哪些。"

  ```mysql
  -- ============================================================
  -- 查询比罗隆涛出生日期更早的学生
  -- ============================================================
  select *
  from student
  where birthday < (select birthday from student where name = '罗隆涛');
  ```

  ​

  $2$."语文成绩超过全体语文平均分的学生名单和分数。"

  ```mysql
  -- ============================================================
  -- 查询语文成绩超过全体语文平均分的学生名单和分数
  -- ============================================================
  select s.name, sc.score
  from student s
  join s_linked_c sc on s.id = sc.sid
  join course c on sc.cid = c.id
  where c.coursename = '语文'
    and sc.score > (select avg(score) from s_linked_c where cid = (select id from course where coursename = '语文'));
  ```

  ​

- **需求三（存在性/归属类）**

  $1$."选了`语文`课的学生，把名字列出来。"

  ```mysql
  -- ============================================================
  -- 查询选了"语文"课的学生名单
  -- ============================================================
  select s.name
  from student s
  join s_linked_c sc on s.id = sc.sid
  join course c on sc.cid = c.id
  where c.coursename = '语文';
  ```

  ​

  $2$."属于`一班`或者`二班`的学生都有谁？"

  ```mysql
  -- ============================================================
  -- 查询属于"一班"或者"二班"的学生
  -- ============================================================
  select s.*
  from student s
  join class c on s.cid = c.id
  where c.name in ('一班', '二班');
  ```

- **需求四（多条件精确匹配类）**

  $1$."有没有人和罗隆涛是**同一个班级，并且出生日期也完全一样**的？"

  ```mysql
  -- ============================================================
  -- 查询和罗隆涛同一个班级且同一天生日的学生（不含自己）
  -- ============================================================
  select s.*
  from student s
  where s.cid = (select cid from student where name = '罗隆涛')
    and s.birthday = (select birthday from student where name = '罗隆涛')
    and s.name != '罗隆涛';
  ```

  ​

- **需求五（统计分析类）**

  $1$."每个班分别有多少人？"

  ```mysql
  -- ============================================================
  -- 查询每个班分别有多少人
  -- ============================================================
  select
      c.id,
      c.name as class_name,
      (select count(*) from student s where s.cid = c.id) as student_count
  from class c;
  ```

  ​

  $2$."每个学生的平均成绩是多少？按平均分从高到低排。"

  ```mysql
  -- ============================================================
  -- 查询每个学生的平均成绩，按平均分从高到低排
  -- ============================================================
  select
      s.id,
      s.name,
      (select avg(score) from s_linked_c sc where sc.sid = s.id) as avg_score
  from student s
  where (select avg(score) from s_linked_c sc where sc.sid = s.id) is not null
  order by avg_score desc;
  ```

  ​

- **需求六（反向排查类）**

  $1$."有没有哪门课一直没人选？"

   ```mysql
  -- ============================================================
  -- 查询没人选的课程（方法一：子查询）
  -- ============================================================
  select *
  from course c
  where c.id not in (select distinct cid from s_linked_c);

  -- ============================================================
  -- 查询没人选的课程（方法二：左外连接）
  -- ============================================================
  select c.*
  from course c
  left join s_linked_c sc on c.id = sc.cid
  where sc.cid is null;​
   ```


## 事务

### 前言

对于我们的员工表**tb_emp** 表和部门表**tb_dept**表，如果有朝一日我有一个部门要解散了，那么我们就要删除对应的部门和员工，对应的指令如下：

```mysql
use test;
-- 删除部门
delete from test.tb_dept where id=1;

-- 删除部门下的员工
delete from test.tb_emp where dept_id =1;
```

这样我们就可以成功删除需要删除的部门，那如果我的删除命令一不下心打错了，导致只删除了要删除的部门，但是员工却还保留，这样数据就不一致了，在这里我们就要引入**事务**。

###  事务的综述

* **事务（Transaction）** 是一组操作的集合，它是一个不可分割的工作单位。事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作 **要么同时成功，要么同时失败。**

* 注意事项：默认MySQL的事务是自动提交的，也就是说，当执行一条DML语句，MySQL会立即隐式的提交事务。

* 事务的的控制

  * 开启一个事务：**start transaction; / begin ;**
  * 提交事务：**commit;**
  * 回滚事务：**rollback;** 
  * 比如说：

  ```mysql
  -- 开启事务
  start transaction ;

  use test;
  -- 删除部门
  delete from test.tb_dept where id=1;

  -- 删除部门下的员工
  delete from test.tb_emp where dept_id =1;

  -- 提交事务(执行了前两步事务后没运行这一条，表内的数据暂时不会变)
  commit;

  -- 回滚事务(如果前面的命令失败了话）
  rollback ;

  ```




* 事务的四大特性


| 特性 | 英文 | 核心含义 | 说明 |
|------|------|----------|------|
| **原子性** | `Atomicity` | 事务是不可分割的最小单元，要么全部成功，要么全部失败 | 事务中的所有操作作为一个整体提交或撤销，不存在部分执行的情况。如果事务执行过程中发生错误，已执行的操作会被回滚到事务开始前的状态 |
| **一致性** | `Consistency` | 事务完成时，必须使所有的数据都保持一致状态 | 事务执行前后，数据库的完整性约束没有被破坏。事务将数据库从一个一致状态转换到另一个一致状态，任何事务的提交都必须保证数据满足所有约束条件（如外键、唯一性、触发器） |
| **隔离性** | `Isolation` | 数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行 | 多个事务并发执行时，每个事务都感觉不到其他事务的存在，彼此隔离。隔离性通过**锁机制**或**MVCC**实现，不同隔离级别（READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ、SERIALIZABLE）控制并发访问的程度 |
| **持久性** | `Durability` | 事务一旦提交或回滚，它对数据库中数据的改变就是永久的 | 事务提交后，其对数据库的修改会持久化到磁盘存储中，即使系统发生故障（如断电、崩溃），已提交的数据也不会丢失。通常通过**日志（WAL）**和**数据备份**机制来保证 |


| 特性 | 实现技术 | 常见问题 |
|------|----------|----------|
| 原子性 | `Undo Log`（回滚日志） | 事务执行中发生错误，需要回滚已执行操作 |
| 一致性 | 约束（主键、外键、唯一性、检查约束）+ 触发器 + 业务逻辑 | 事务前后数据不满足业务规则 |
| 隔离性 | `锁 + MVCC`（多版本并发控制） | 脏读、不可重复读、幻读 |
| 持久性 | `Redo Log`（重做日志）+ 双写缓冲 | 提交后数据因故障丢失 |

##  索引

### 前言

如果在数据库的某张表中有**几百万**条数据，那么我们要查询的时候如果单纯的全表扫描的话，这样的效率就会非常慢，那在现实业务中，这样会浪费很多时间，那这个时候我们想到啥呢，当然是**数据结构** 中的树结构，**BST** 二叉搜索树等，**哈希表**等等，而这里为了优化查询效率，就建立**树形结构**。 

### 索引 

* **索引** 是帮组数据库**高效获取数据** 的**数据结构**（InnoDB默认 **B+树**） ， 索引的优缺点
  * ✅ 优点：大幅提升查询速度
  * ❌ 缺点：占用磁盘空间；增删改操作需要维护索引，速度下降
* InnoDB B+树的**原理和特点** 

首先我们可以以代码为例：

```java
import java.util.*;
/**
 * InnoDB B+树简化实现
 * 特点：
 * 1. 叶子节点存储数据（键值对）
 * 2. 内部节点只存索引
 * 3. 叶子节点形成双向链表（支持范围查询）
 * 4. 页分裂机制（模仿InnoDB）
 */
public class InnoDBBPlusTree<K extends Comparable<K>, V> {
    // 节点类（页）
    private abstract class Node {
        // 父节点
        Node parent;
        // 节点中的键列表（有序）
        List<K> keys;
        // 是否叶子节点
        abstract boolean isLeaf();
        // 节点大小（页容量）
        int getMaxKeys() { return pageSize - 1; }
    }
    // 内部节点
    private class InternalNode extends Node {
        // 子节点列表（长度 = keys.size() + 1）
        List<Node> children;
        
        InternalNode() {
            this.keys = new ArrayList<>();
            this.children = new ArrayList<>();
        }
        @Override
        boolean isLeaf() { return false; }
    }
    // 叶子节点
    private class LeafNode extends Node {
        // 存储键值对
        List<V> values;
        // 双向链表指针
        LeafNode prev;
        LeafNode next;
        LeafNode() {
            this.keys = new ArrayList<>();
            this.values = new ArrayList<>();
            this.prev = null;
            this.next = null;
        }
        @Override
        boolean isLeaf() { return true; }
    }
    // B+树属性
    private Node root;
    private final int pageSize;  // 每页最多存储的键数（类似InnoDB的16KB）
    private int size;            // 总记录数
    // 构造函数
    public InnoDBBPlusTree() {
        this(4); // 默认每页4个键（演示用，InnoDB实际是16KB/键大小 ≈ 1170）
    }
    public InnoDBBPlusTree(int pageSize) {
        if (pageSize < 3) throw new IllegalArgumentException("pageSize至少为3");
        this.pageSize = pageSize;
        this.root = new LeafNode(); // 初始时根节点是叶子
        this.size = 0;
    }
    // ==================== 插入 ====================
    public void insert(K key, V value) {
        if (key == null || value == null) {
            throw new IllegalArgumentException("键和值不能为空");
        }
        LeafNode leaf = findLeafNode(key);
        insertIntoLeaf(leaf, key, value);
        size++;
    }
    // 查找键所在的叶子节点
    private LeafNode findLeafNode(K key) {
        Node node = root;
        while (!node.isLeaf()) {
            InternalNode internal = (InternalNode) node;
            int pos = findInsertPos(internal.keys, key);
            // 如果 key 大于等于某个键，走向右边的子节点
            if (pos < internal.keys.size() && key.compareTo(internal.keys.get(pos)) >= 0) {
                pos++;
            }
            node = internal.children.get(pos);
        }
        return (LeafNode) node;
    }
    // 在叶子节点中插入
    private void insertIntoLeaf(LeafNode leaf, K key, V value) {
        int pos = findInsertPos(leaf.keys, key);
        // 检查是否已存在
        if (pos < leaf.keys.size() && leaf.keys.get(pos).compareTo(key) == 0) {
            leaf.values.set(pos, value); // 更新
            return;
        }
        // 插入键值
        leaf.keys.add(pos, key);
        leaf.values.add(pos, value);
        // 如果叶子节点满了，需要分裂
        if (leaf.keys.size() > pageSize) {
            splitLeafNode(leaf);
        }
    }
    // 分裂叶子节点
    private void splitLeafNode(LeafNode leaf) {
        // 找到中点（右半部分）
        int mid = pageSize >>1;
        // 创建新叶子节点
        LeafNode newLeaf = new LeafNode();
        // 将右半部分移到新节点
        for (int i = mid; i < leaf.keys.size(); i++) {
            newLeaf.keys.add(leaf.keys.get(i));
            newLeaf.values.add(leaf.values.get(i));
        }
        // 删除原节点右半部分
        leaf.keys.subList(mid, leaf.keys.size()).clear();
        leaf.values.subList(mid, leaf.values.size()).clear();
        // 更新双向链表
        newLeaf.next = leaf.next;
        if (leaf.next != null) {
            leaf.next.prev = newLeaf;
        }
        leaf.next = newLeaf;
        newLeaf.prev = leaf;
        // 将新节点的第一个键提升到父节点
        K firstKeyOfNew = newLeaf.keys.get(0);
        // 如果叶子节点是根节点，创建新的根
        if (leaf.parent == null) {
            InternalNode newRoot = new InternalNode();
            newRoot.keys.add(firstKeyOfNew);
            newRoot.children.add(leaf);
            newRoot.children.add(newLeaf);
            leaf.parent = newRoot;
            newLeaf.parent = newRoot;
            root = newRoot;
        } else {
            // 否则插入到父节点
            insertIntoInternal((InternalNode) leaf.parent, firstKeyOfNew, leaf, newLeaf);
        }
    }
    // 插入到内部节点
    private void insertIntoInternal(InternalNode parent, K key, Node leftChild, Node rightChild) {
        int pos = findInsertPos(parent.keys, key);
        // 将键和右孩子插入
        parent.keys.add(pos, key);
        // 注意：左孩子已经存在，只需插入右孩子
        parent.children.add(pos + 1, rightChild);
        
        // 如果内部节点满了，分裂
        if (parent.keys.size() > pageSize) {
            splitInternalNode(parent);
        }
    }
    // 分裂内部节点
    private void splitInternalNode(InternalNode node) {
        int mid = pageSize >>1;
        // 创建新内部节点
        InternalNode newNode = new InternalNode();
        // 将右半部分的键移到新节点（注意：mid位置的键要上升到父节点）
        for (int i = mid + 1; i < node.keys.size(); i++) {
            newNode.keys.add(node.keys.get(i));
        }
        // 右半部分的子节点
        for (int i = mid + 1; i < node.children.size(); i++) {
            newNode.children.add(node.children.get(i));
            node.children.get(i).parent = newNode;
        }
        // 获取要上升的键（mid位置的键）
        K promoteKey = node.keys.get(mid);
        // 删除原节点右半部分
        node.keys.subList(mid, node.keys.size()).clear();
        node.children.subList(mid + 1, node.children.size()).clear();
        // 如果当前节点是根节点，创建新根
        if (node.parent == null) {
            InternalNode newRoot = new InternalNode();
            newRoot.keys.add(promoteKey);
            newRoot.children.add(node);
            newRoot.children.add(newNode);
            node.parent = newRoot;
            newNode.parent = newRoot;
            root = newRoot;
        } else {
            // 插入到父节点
            insertIntoInternal((InternalNode) node.parent, promoteKey, node, newNode);
        }
    }
    // ==================== 查询 ====================
    
    // 查询单个值
    public V search(K key) {
        if (key == null) return null;
        LeafNode leaf = findLeafNode(key);
        int pos = findInsertPos(leaf.keys, key);
        if (pos < leaf.keys.size() && leaf.keys.get(pos).compareTo(key) == 0) {
            return leaf.values.get(pos);
        }
        return null;
    }
    // 范围查询 [startKey, endKey]
    public List<V> rangeQuery(K startKey, K endKey) {
        List<V> result = new ArrayList<>();
        if (startKey == null || endKey == null || startKey.compareTo(endKey) > 0) {
            return result;
        }
        // 找到起始键所在的叶子节点
        LeafNode leaf = findLeafNode(startKey);
        while (leaf != null) {
            for (int i = 0; i < leaf.keys.size(); i++) {
                K key = leaf.keys.get(i);
                if (key.compareTo(startKey) >= 0 && key.compareTo(endKey) <= 0) {
                    result.add(leaf.values.get(i));
                }
                if (key.compareTo(endKey) > 0) {
                    return result;
                }
            }
            leaf = leaf.next;
        }
        return result;
    }
    // ==================== 辅助方法 ====================
    // 查找插入位置（二分查找）
    private int findInsertPos(List<? extends Comparable<K>> keys, K key) {
        int left = 0, right = keys.size() - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int cmp = keys.get(mid).compareTo(key);
            if (cmp == 0) return mid;
            if (cmp < 0) left = mid + 1;
            else right = mid - 1;
        }
        return left;
    }
    // 获取树的高度（B+树层级）
    public int getHeight() {
        int height = 0;
        Node node = root;
        while (!node.isLeaf()) {
            height++;
            node = ((InternalNode) node).children.get(0);
        }
        return height + 1;
    }
    // 获取总记录数
    public int size() {
        return size;
    }
    // ==================== 遍历和打印 ====================
    // 中序遍历（从最小键开始）
    public void printInOrder() {
        System.out.println("=== B+树中序遍历 ===");
        LeafNode leaf = findLeftmostLeaf();
        int count = 0;
        while (leaf != null) {
            for (int i = 0; i < leaf.keys.size(); i++) {
                System.out.print(leaf.keys.get(i) + "(" + leaf.values.get(i) + ") ");
                count++;
            }
            leaf = leaf.next;
        }
        System.out.println("\n总记录数: " + count);
    }
    // 打印树结构（层次遍历）
    public void printTree() {
        System.out.println("=== B+树结构 ===");
        System.out.println("高度: " + getHeight());
        System.out.println("总记录数: " + size);
        if (root == null) return;
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        int level = 0;
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            System.out.print("第" + level + "层: ");
            for (int i = 0; i < levelSize; i++) {
                Node node = queue.poll();
                if (node.isLeaf()) {
                    LeafNode leaf = (LeafNode) node;
                    System.out.print("[叶子: ");
                    for (int j = 0; j < leaf.keys.size(); j++) {
                        System.out.print(leaf.keys.get(j) + ":" + leaf.values.get(j));
                        if (j < leaf.keys.size() - 1) System.out.print(", ");
                    }
                    System.out.print("] ");
                } else {
                    InternalNode internal = (InternalNode) node;
                    System.out.print("[内部: ");
                    for (int j = 0; j < internal.keys.size(); j++) {
                        System.out.print(internal.keys.get(j));
                        if (j < internal.keys.size() - 1) System.out.print(", ");
                    }
                    System.out.print("] ");
                    
                    // 将子节点加入队列
                    for (Node child : internal.children) {
                        queue.offer(child);
                    }
                }
            }
            System.out.println();
            level++;
        }
    }
    // 查找最左叶子节点
    private LeafNode findLeftmostLeaf() {
        Node node = root;
        while (!node.isLeaf()) {
            node = ((InternalNode) node).children.get(0);
        }
        return (LeafNode) node;
    }
    // ==================== 测试 ====================
    public static void main(String[] args) {
        System.out.println("=== InnoDB B+树测试 ===\n");
        // 创建B+树（页容量4）
        InnoDBBPlusTree<Integer, String> tree = new InnoDBBPlusTree<>(4);
        // 插入数据
        System.out.println("1. 插入数据:");
        int[] keys = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110, 120};
        String[] values = {"A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L"};
        for (int i = 0; i < keys.length; i++) {
            tree.insert(keys[i], values[i]);
            System.out.println("  插入 " + keys[i] + " -> " + values[i]);
        }
        System.out.println("\n2. 树结构:");
        tree.printTree();
        System.out.println("\n3. 中序遍历:");
        tree.printInOrder();
        System.out.println("\n4. 单个查询:");
        System.out.println("  查询 50 -> " + tree.search(50));
        System.out.println("  查询 55 -> " + tree.search(55));
        System.out.println("  查询 100 -> " + tree.search(100));
        System.out.println("\n5. 范围查询:");
        System.out.println("  范围 [30, 80]: " + tree.rangeQuery(30, 80));
        System.out.println("  范围 [65, 115]: " + tree.rangeQuery(65, 115));
        System.out.println("\n6. 更新已存在的键:");
        tree.insert(50, "UPDATED");
        System.out.println("  更新 50 -> UPDATED");
        System.out.println("  查询 50 -> " + tree.search(50));
        System.out.println("\n7. 树高度: " + tree.getHeight());
        System.out.println("  总记录数: " + tree.size());
    }
}
```

从上面的代码中，我们可以看出**B+树** 的特点有：

- **多路搜索**：每一个节点可以存储多个 key（**有 n 个 key，就有 n 个指针**），相比二叉树大幅降低了树的高度，减少磁盘 I/O 次数。

- **数据与索引分离**：所有的数据都存储在叶子节点，非叶子节点仅用于索引数据（存储键值和指针），不存储实际数据，因此每个内部节点可以容纳更多的索引条目。

- **有序链表**：叶子节点形成了一颗双向链表，便于数据的排序及区间范围查询（如 `BETWEEN`、`>`、`<` 等操作），无需回溯父节点即可顺序遍历。


它和**BST**和红黑树的区别在于：

* 表格对比：


| 对比维度 | **B+树** | **BST（二叉搜索树）** | **红黑树** |
|----------|----------|----------------------|------------|
| **数据结构类型** | 多路平衡搜索树（M-way平衡树） | 二叉树（每个节点最多2个子节点） | 近似平衡的二叉搜索树（带颜色标记） |
| **节点存储** | 每个节点可存储**多个键**（n个键，n个指针） | 每个节点存储**1个键** + 左/右孩子指针 | 每个节点存储**1个键** + 左/右/父指针 + 颜色位 |
| **数据存储位置** | **所有数据存储在叶子节点**，内部节点仅存索引 | 数据存储在**所有节点**（含内部节点） | 数据存储在**所有节点**（含内部节点） |
| **叶子节点结构** | 叶子节点形成**双向链表**，支持顺序遍历 | 叶子节点为普通二叉树叶子，无横向指针 | 叶子节点为普通二叉树叶子（NIL节点），无横向指针 |
| **树的高度** | **很低**（通常2~4层），可存储亿级数据 | **可能很高**（最坏O(n)），退化为链表 | **O(log₂n)**，高度约为 2log₂(n+1) |
| **查询复杂度** | **O(logₘn)**，m为节点阶数（通常m很大） | **平均 O(log₂n)，最坏 O(n)** | **稳定 O(log₂n)** |
| **插入复杂度** | **O(logₘn)**，可能需要页分裂 | **平均 O(log₂n)，最坏 O(n)** | **O(log₂n)**，可能需要旋转+变色 |
| **删除复杂度** | **O(logₘn)**，可能需要页合并 | **平均 O(log₂n)，最坏 O(n)** | **O(log₂n)**，可能需要旋转+变色 |
| **范围查询效率** | **极高**，通过叶子链表一次遍历即可 | **低**，需要中序遍历回溯 | **低**，需要中序遍历回溯 |
| **磁盘I/O次数** | **极少**（树矮 + 节点大），适合磁盘存储 | **较多**（树高），适合内存存储 | **中等**（树高约log₂n），适合内存存储 |
| **缓存友好性** | **高**（顺序存储，预读效果好） | **低**（节点分散，缓存命中率低） | **中等**（节点分散但树高可控） |
| **适用场景** | **数据库索引（MySQL InnoDB）、文件系统** | 算法教学、简单数据存储（数据量小） | **Java TreeMap、C++ STL map、Linux内核调度** |
| **平衡机制** | 通过**页分裂/页合并**维持平衡 | **无自动平衡机制** | 通过**旋转 + 重新着色**维持平衡 |
| **节点利用率** | **高**（每个节点存储多个键，空间利用率高） | **低**（每个节点仅1个键，大量指针开销） | **低**（每个节点仅1个键 + 颜色位） |
| **实现复杂度** | **复杂**（需处理分裂、合并、双向链表维护） | **简单**（基础数据结构） | **中等**（需处理旋转和变色逻辑） |
| **空节点处理** | 无特殊空节点 | 直接用 `null` | 使用 **NIL叶子节点**（统一哨兵） |
| **是否支持并发** | InnoDB支持MVCC + 行锁 | 通常不支持（需外部加锁） | 通常不支持（需外部加锁） |
| **典型应用** | MySQL InnoDB索引、PostgreSQL索引、HBase | 算法演示、简单内存缓存 | Java TreeMap、C++ std::map、epoll红黑树 |
| **内存占用** | **较大**（每个节点多键 + 指针数组） | **较小**（每节点单键 + 2指针） | **中等**（每节点单键 + 3指针 + 颜色位） |

* 示意图对比：

  ```bash
  B+树                   
                     [30, 60]                 ← 内部节点（仅索引）
                     /    |    \
                [10,20] [40,50] [70,80]        ← 内部节点（仅索引）
                /  |  \  /  |  \  /  |  \
           [1,2] [..] [..] [..] [..] [..] [..] ← 叶子节点（存数据）
              ↓     ↓     ↓     ↓     ↓     ↓
            1↔2↔...↔...↔...↔...↔...↔...↔80   ← 双向链表
            
  BST（二叉搜索树）
                      [50]
                     /    \
                 [30]      [70]
                /    \    /    \
             [20]   [40][60]   [80]
            /  \
         [10]  [25]
         
  红黑树
                      [50⚫]
                     /      \
                [30🔴]      [70⚫]
               /      \    /      \
           [20⚫]   [40⚫][60🔴]  [80⚫]
          /    \
       [10🔴] [25🔴]

  ```

* 在数据库中的应用

  * 索引的语法：

    * 创建索引： **create [unique] index 索引名 on 表名 （字段名,..）;**
    * 查看索引：**show index from 表名;**
    * 删除索引：**drop index 索引名 on 表名;**
    * 比如说：

   ```mysql
   -- 创建普通索引
   create index idx_emp_name on tb_emp(name);

    -- 创建唯一索引（字段值不能重复）
    create unique index idx_emp_username on tb_emp(username);

    -- 查看表索引
    show index from tb_emp;

    -- 删除索引
    drop index idx_emp_name on tb_emp;
   ```

   * 注意事项：
     - 主键字段，在建表时，会自动创建主键索引。
     - 添加唯一约束时，数据库实际上会添加唯一索引。 
  

## Explain 执行计划分析

### 核心字段说明

| 字段 | 含义 | 好坏判断 |
|------|------|----------|
| **type** | 访问类型 | `ref/range` ✅  \| `ALL` ❌ |
| **key** | 实际使用的索引 | 有值 ✅  \| `NULL` ❌ |
| **rows** | 预估扫描行数 | 越小越好 |

---

### SQL 1：用户名查询

```sql
EXPLAIN SELECT * FROM tb_emp WHERE username = 'jinyong';
```

| 字段 | 走索引         | 不走索引 |
| ---- | -------------- | -------- |
| type | `ref` ✅        | `ALL` ❌  |
| key  | `idx_username` | `NULL`   |
| rows | 1              | 全表行数 |

---

### SQL 2：性别查询

```sql
EXPLAIN SELECT * FROM tb_emp WHERE gender = 1;
```

| 字段 | 值       | 说明                         |
| ---- | -------- | ---------------------------- |
| type | `ALL` ❌  | 性别区分度低，优化器放弃索引 |
| key  | `NULL`   | 即使有索引也可能不走         |
| rows | 全表行数 | 扫描全部数据                 |

**优化：** 建组合索引 `(gender, username)` 或添加更多条件

---

### SQL 3：OR + 子查询

```sql
EXPLAIN FORMAT = TRADITIONAL
SELECT id FROM tb_emp 
WHERE name = '金庸' OR id = (SELECT id FROM tb_emp WHERE name = '金庸');
```

| 查询   | type    | key        | rows | 问题            |
| ------ | ------- | ---------- | ---- | --------------- |
| 主查询 | `ALL` ❌ | `NULL`     | 全表 | OR 导致索引失效 |
| 子查询 | `ref` ✅ | `idx_name` | 1    | 正常            |

**优化：** 改用 `UNION`

```sql
SELECT id FROM tb_emp WHERE name = '金庸'
UNION
SELECT id FROM tb_emp WHERE id = (SELECT id FROM tb_emp WHERE name = '金庸');
```

### 规范提醒


| 规范 | 详细说明 | 原因 | 反例 |
|------|----------|------|------|
| **① 频繁作为查询条件的字段建立索引** | `WHERE`、`JOIN`、`ORDER BY`、`GROUP BY` 中高频出现的字段优先建索引 | 索引的本质是加速查询，不用的字段建索引纯属浪费空间 | ❌ 给从来不查的 `remark` 字段建索引 |
| **② 主键自带主键索引，无需重复创建** | `PRIMARY KEY` 自动生成聚簇索引，再建普通索引是重复劳动 | 重复索引浪费磁盘空间，且增删改需维护两份 | ❌ `CREATE INDEX idx_id ON user(id);` |
| **③ 不要给重复度极高的字段建索引** | 性别（男/女）、状态（0/1）、类型（少数几类）等区分度低的字段 | 区分度 < 10% 时，优化器大概率放弃使用索引，全表扫描反而更快 | ❌ `CREATE INDEX idx_gender ON user(gender);` |
| **④ 索引不是越多越好** | 单表索引建议 ≤ 5 个，组合索引字段 ≤ 3 个 | 每多一个索引，`INSERT`/`UPDATE`/`DELETE` 都要同步维护，拖慢写入性能 | ❌ 一张表建了 20 个索引 |


