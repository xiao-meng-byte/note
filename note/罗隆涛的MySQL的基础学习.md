# MySQL 基础学习笔记
## 一、基础概念

| 概念 | 全称 / 说明 |
|---|---|
| **数据库** | 存储数据的仓库 |
| **关系型数据库** | 使用二维表来维护数据的数据库（如 MySQL、Oracle） |
| **非关系型数据库** | 不用表存储（如 Redis、MongoDB） |
| **DBMS** | Database Management System，数据库管理系统；一个 DBMS 下可以有多个数据库，一个数据库可以有多张表 |
| **SQL** | Structured Query Language，结构化查询语言，用于操作关系型数据库 |

**SQL 基本规则：**
- 语句以分号 `;` 结尾
- **不区分大小写**
- 注释：`-- 内容`、`# 内容`（单行）、`/* 内容 */`（多行）

---

## 二、环境变量配置与 MySQL 服务命令

| 序号 | 命令 | 作用 |
|---|---|---|
| 1 | `mysqld --initialize-insecure` | 初始化数据库，在 mysql 文件夹生成 data 目录 |
| 2 | `mysqld --install mysql` | 注册成 Windows 系统服务 |
| 3 | `net start mysql` | 启动 MySQL 服务 |
| 4 | `net stop mysql` | 停止 MySQL 服务 |
| 5 | `mysqladmin -u root -p password 密码` | 设置 root 密码 |
| 6 | `mysql -uroot -p` | 登录 MySQL（回车后输入密码） |
| 7 | `exit` | 退出 MySQL 连接 |

---

## 三、SQL 四大语句分类

| 缩写 | 全称 | 中文名称 | 作用 | 典型关键字 | 一句话记忆 |
|---|---|---|---|---|---|
| **DDL** | Data Definition Language | 数据定义语言 | 定义库、表、字段等**数据库对象** | `create` `drop` `alter` | 管架子 |
| **DML** | Data Manipulation Language | 数据操作语言 | 操作表里的**行数据**：增删改 | `insert` `delete` `update` | 管数据改动 |
| **DQL** | Data Query Language | 数据查询语言 | 查询表中的数据记录 | `select` | 只查不改 |
| **DCL** | Data Control Language | 数据控制语言 | 创建用户、分配权限、管控访问 | `grant` `revoke` `create user` | 管账号权限 |

> 补充：部分教材还会提到 **TCL**（事务控制语言）：`commit`、`rollback`，用于事务提交与回滚。

**语句分类示例：**
```sql
create database Test01;   -- DDL：创建数据库对象
show databases;           -- DQL：查询
use Test01;               -- 切换当前数据库
```

---

## 四、DDL — 数据库操作

| 操作 | 语法 | 示例 |
|---|---|---|
| 查询所有数据库 | `show databases;` | `show databases;` |
| 查看当前数据库 | `select database();` | `select database();` |
| 创建数据库 | `create database [if not exists] 库名;` | `create database if not exists Test01;` |
| 使用 / 切换数据库 | `use 数据库名;` | `use Test01;` |
| 删除数据库 | `drop database [if exists] 库名;` | `drop database if exists Test01;` |

> `database` 可以用 `schema` 替代，效果相同。

---

## 五、DDL — 表操作

### 5.1 表查询

| 操作 | 语句 |
|---|---|
| 查看当前库所有表 | `show tables;` |
| 查看表结构（字段信息） | `desc 表名;` |
| 查看建表语句 | `show create table 表名;` |

### 5.2 建表语句语法
```sql
create table 表名(
    字段1 字段类型 [约束] [comment '字段1注释'],
    字段2 字段类型 [约束] [comment '字段2注释'],
    ……
    字段n 字段类型 [约束] [comment '字段n注释']
)[comment '表注释'];
```

| 组成部分 | 作用 |
|---|---|
| `create table 表名` | 声明创建一张数据表 |
| 字段名 | 定义列的名称 |
| 字段类型 | 指定该列存储的数据类型 |
| `[约束]` | 可选：主键、非空、唯一、默认值等 |
| `comment 'xxx'` | 注释，**必须加单引号** |

**注意事项：**
1. 多个字段用**逗号**隔开，最后一个字段后**不加逗号**
2. `[]` 表示可选部分
3. 注释字符串必须用**单引号**包裹

**完整示例：**
```sql
create table user(
    id int primary key comment '用户编号',
    name varchar(20) not null comment '用户姓名',
    age int comment '用户年龄'
) comment '用户信息表';
```

### 5.3 修改表结构（ALTER TABLE）

| 功能 | 语法模板 | 示例 |
|---|---|---|
| **添加字段** | `alter table 表名 add 字段名 类型(长度) [comment 注释] [约束];` | `alter table tb_emp add age int comment '年龄';` |
| **修改字段类型**（不改名字） | `alter table 表名 modify 字段名 新类型(长度);` | `alter table tb_emp modify name varchar(20);` |
| **修改字段名+类型** | `alter table 表名 change 旧字段名 新字段名 类型(长度);` | `alter table tb_emp change name realname varchar(10);` |
| **删除字段** | `alter table 表名 drop column 字段名;` | `alter table tb_emp drop column age;` |
| **修改表名** | `rename table 表名 to 新表名;` | `rename table tb_emp to emp;` |
| **删除表** | `drop table [if exists] 表名;` | `drop table if exists tb_emp;` |

### 5.4 modify vs change 辨析

| 关键字 | 能否改字段名 | 能否改类型/约束 | 语法特点 |
|---|:---:|:---:|---|
| `modify` | ❌ | ✅ | 只写一次字段名 |
| `change` | ✅ | ✅ | 旧名、新名都要写（不改名也要写两遍相同的） |

**示例对比：**
```sql
-- modify：只改类型，名字不变
alter table tb_emp modify password varchar(30);

-- change：既改名字又改类型
alter table tb_emp change password pwd varchar(25);
```

### 5.5 修改 / 删除约束（拓展）
| 操作 | 语句 |
|---|---|
| 删除唯一约束 | `alter table 表名 drop index 约束名;` |
| 添加唯一约束 | `alter table 表名 add constraint 约束名 unique(字段名);` |

> ⚠️ **生产环境提醒**：表有大量数据时，`alter` 操作会锁表，尽量不要在高峰期执行。

---

## 六、MySQL 五大约束

| 约束 | 关键字 | 描述 | 一张表数量限制 |
|---|---|---|---|
| 非空约束 | `not null` | 字段值不能为 `null` | 可多个 |
| 唯一约束 | `unique` | 字段值唯一、不重复 | 可多个 |
| 主键约束 | `primary key` | 一行数据的唯一标识，**非空 + 唯一** | **只能 1 个** |
| 默认约束 | `default` | 未指定值时自动填入默认值 | 可多个 |
| 外键约束 | `foreign key` | 两张表建立连接，保证数据一致性 | 可多个 |

### 重点辨析

| 对比项 | 主键 `primary key` | 唯一约束 `unique` |
|---|---|---|
| 是否允许 NULL | ❌ 不允许 | ✅ 允许（NULL 不算重复） |
| 一张表数量 | 只能 1 个 | 可以多个 |
| 作用 | 行的唯一标识 | 保证列值不重复 |

| 对比项 | 说明 |
|---|---|
| `not null` vs 空字符串 | 非空约束只是不能存 `null`，可以存空字符串 `''` |
| `default` 触发条件 | 只有插入数据**不给该字段赋值**时才生效；手动填 `null` 不会触发默认值 |
| 物理外键 vs 逻辑外键 | 物理外键在数据库层面强制约束；开发中常用**逻辑外键**（代码层面控制关联），不开启物理外键 |

**建表示例：**
```sql
create table student(
    id int primary key comment '主键，非空且唯一',
    name varchar(20) not null comment '姓名不能为空',
    phone varchar(11) unique comment '手机号不能重复',
    gender char(1) default '男' comment '不填性别默认男'
);
```

---

## 七、MySQL 常用数据类型

### 7.1 数值类型

| 类型 | 字节 | 有符号范围 | 无符号范围 | 典型场景 |
|---|---:|---|---|---|
| `tinyint` | 1 | -128 ~ 127 | 0 ~ 255 | 状态、年龄、性别标记 |
| `smallint` | 2 | -32768 ~ 32767 | 0 ~ 65535 | 小计数 |
| `mediumint` | 3 | -8388608 ~ 8388607 | 0 ~ 16777215 | 中等计数 |
| `int` | 4 | -21亿 ~ 21亿 | 0 ~ 42亿 | 普通 ID、数量 |
| `bigint` | 8 | -2⁶³ ~ 2⁶³-1 | 0 ~ 2⁶⁴-1 | 大表主键、雪花 ID |
| `float` | 4 | 单精度浮点 | — | ⚠️ 有精度丢失，**不要存金额** |
| `double` | 8 | 双精度浮点 | — | ⚠️ 有精度丢失，**不要存金额** |
| `decimal` | 可变 | 由 M、D 决定 | — | ✅ 金额、价格，`decimal(M,D)` |

> `decimal(5,2)`：一共 5 位数字，2 位小数，最大 `999.99`

### 7.2 字符串类型

| 类型 | 大小 | 特点 | 推荐场景 |
|---|---|---|---|
| `char` | 0~255 byte | 定长，不足补空格，查询快 | 手机号、身份证、性别 |
| `varchar` | 0~65535 byte | 变长，按需占用，省空间 | 姓名、地址、标题 |
| `tinytext` | 0~255 byte | 短文本 | 简短备注 |
| `text` | 0~65535 byte | 长文本 | 评论、摘要 |
| `mediumtext` | 0~16MB | 中等文本 | 文章正文 |
| `longtext` | 0~4GB | 超大文本 | 海量日志、大文档 |
| `blob` 系列 | 对应上面大小 | 二进制存储 | ❌ 不建议存文件，存路径即可 |

### 7.3 日期时间类型

| 类型 | 字节 | 格式 | 取值范围 | 用途 |
|---|---:|---|---|---|
| `date` | 3 | `yyyy-MM-dd` | 1000-01-01 ~ 9999-12-31 | 只存日期（生日） |
| `time` | 3 | `HH:mm:ss` | -838:59:59 ~ 838:59:59 | 只存时间、时长 |
| `datetime` | 8 | `yyyy-MM-dd HH:mm:ss` | 1000-01-01 ~ 9999-12-31 | ✅ 业务记录时间，首选 |
| `timestamp` | 4 | `yyyy-MM-dd HH:mm:ss` | 1970-01-01 ~ 2038-01-19 | ⚠️ 2038 年溢出，自动时区转换 |
| `year` | 1 | `yyyy` | 1901 ~ 2155 | 只存年份 |

---

## 八、开发选型速查表

| 数据场景 | 推荐类型 | 理由 |
|---|---|---|
| 状态、年龄、性别标记 | `tinyint` | 字节最小，够用 |
| 普通 ID、数量 | `int` | 4 字节，范围足够 |
| 大表主键、雪花 ID | `bigint` | 避免溢出 |
| 金额、价格 | `decimal(M,D)` | 无精度丢失 |
| 固定长度字符串（手机号、身份证） | `char` | 定长查询快 |
| 不固定长度（姓名、地址） | `varchar` | 按需占用，省空间 |
| 大段文章、正文 | `mediumtext` | 16MB 足够 |
| 业务创建 / 更新时间 | `datetime` | 范围大，无 2038 问题 |
| 文件、图片 | **存路径字符串** | 不存 blob，数据库只管元数据 |

---

## 九、DML

### 9.1 insert

| 用法类型 | 语法 |
|---|---|
| 指定字段添加数据 | `insert into 表名 (字段名1, 字段名2) values (值1, 值2);` |
| 全部字段添加数据 | `insert into 表名 values (值1, 值2, ...);` |
| 批量添加数据（指定字段） | `insert into 表名 (字段名1, 字段名2) values (值1, 值2), (值1, 值2);` |
| 批量添加数据（全部字段） | `insert into 表名 values (值1, 值2, ...), (值1, 值2, ...);` |

### 9.2 update
用法：`update 表名 set 字段名 = 值1, 字段名 = 值2 [where 条件]`
不加where就是**更新全表**

### 9.3 delete
```sql
delete from tb_emp [where id = 1];
```

---

## 十、DQL

### 1. 基础查询

|功能|sql示例|说明|
| ---- | ---- | ---- |
|查询指定字段|`select name,enrtydate from tb_emp;`|逗号分隔多个字段|
|查询全部字段|`select * from tb_emp;`|不推荐，效率低、可读性差|
|字段起别名|`select name 姓名,enrtydate 入职日期 from tb_emp;`|as可以省略，别名含特殊符号用单引号|
|去重查询|`select distinct job from tb_emp;`|去除重复的数据行|

### 2. 条件查询 where
|功能|sql示例|说明|
| ---- | ---- | ---- |
|等值查询|`select * from tb_emp where name = '韩二';`|字符串值加单引号|
|比较运算|`select * from tb_emp where id <=5;`|支持 > < >= <= = != <>|
|判断null|`select * from tb_emp where job is null;`|不能用 = null|
|判断非null|`select * from tb_emp where job is not null;`|查询字段有值的数据|
|区间between and|`select * from tb_emp where id between 5 and 10;`|闭区间，包含两端数值|
|in多选一|`select * from tb_emp where job in (1,3);`|等价于多个or条件|
|模糊查询下划线|`select * from tb_emp where name like '___';`|一个下划线匹配1个任意字符|
|模糊查询百分号|`select * from tb_emp where name like '张%';`|%匹配任意数量任意字符|

> 逻辑运算符：`and`同时满足，`or`满足其一，`not`取反

### 3. 聚合函数（纵向统计）
|函数|作用|sql示例|备注|
| ---- | ---- | ---- | ---- |
|count|统计行数|`select count(*) from tb_emp;`|count(*)推荐，mysql做优化；count(字段)忽略null|
|min|取最小值|`select min(enrtydate) from tb_emp;`|可用于日期、数字|
|max|取最大值|`select max(enrtydate) from tb_emp;`|可用于日期、数字|
|avg|求平均值|`select avg(id) from tb_emp;`|只计算数值类型|
|sum|求和|`select sum(id) from tb_emp;`|只计算数值类型|

### 4. 分组查询 group by
|功能|sql示例|说明|
| ---- | ---- | ---- |
|简单分组统计|`select gender,count(*) from tb_emp group by gender;`|select后面一般只能放分组字段+聚合函数|
|分组后过滤having|`select job,count(*) from tb_emp where enrtydate <= '2022-06-11' group by job having count(*)>=2;`|where：分组**之前**过滤；having：分组**之后**过滤，可以写聚合函数条件|

### 5. 排序 order by
|功能|sql示例|说明|
| ---- | ---- | ---- |
|降序排序|`select * from tb_emp order by enrtydate desc;`|desc降序；asc升序，asc默认可以省略|
|多字段排序|`select * from tb_emp order by enrtydate,update_time desc;`|先按第一个字段，相等再按后面字段排序|

### 6. 分页 limit
|功能|sql示例|公式|
| ---- | ---- | ---- |
|第1页，每页5条|`select * from tb_emp limit 0,5;`|起始索引从0开始，第一页索引0|
|第2页，每页5条|`select * from tb_emp limit 5,5;`|起始索引 = (页数‑1) * 每页条数|

**完整语法顺序：**
`select ... from 表 where ... group by ... having ... order by ... limit ...`

### 7. 条件函数
|函数|示例|说明|
| ---- | ---- | ---- |
|if三元函数|`select if(gender=1,'男性员工','女性员工'),count(*) from tb_emp group by gender;`|if(条件,true返回值,false返回值)|
|case表达式|```select case job when 1 then '班主任' when 2 then '讲师' else '未分配职位' end as 职位,count(*) as 人数 from tb_emp group by job;```|多分支判断，end不能丢|

---

## 十一、多表关系 & 多表查询
### 11.1 表与表三大关系
| 关系类型 | 说明 | 实现方案 |
| ---- | ---- | ---- |
| **一对多(1:N)** | 部门与员工：1个部门包含多名员工；一名员工归属一个部门 | **多的一方添加外键字段**（员工表 `dept_id`），项目多用逻辑外键 |
| **一对一(1:1)** | 用户 ↔ 用户详情；一张表一条记录对应另一张表一条记录 | 任意一方添加外键，并给外键增加 `unique` 约束 |
| **多对多(M:N)** | 学生 ↔ 课程；一个学生选多门课，一门课多名学生选 | **建立中间关系表**，存放两张主表主键作为外键 |

> 当前案例：`tb_dept`(1) —— `tb_emp`(多)，典型一对多。

### 测试数据表脚本
```sql
-- 部门表
create table tb_dept
(
    id          int unsigned primary key auto_increment comment 'ID',
    name        varchar(10) not null unique comment '部门名称',
    create_time datetime    not null comment '创建时间',
    update_time datetime    not null comment '修改时间'
) comment '部门表';

insert into tb_dept (name, create_time, update_time)
values ('学工部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('教研部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('咨询部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('就业部', '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('人事部', '2022-10-30 14:32:02', '2022-10-30 14:32:02');

-- 员工表（逻辑外键，注释关闭物理外键）
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
    -- constraint `fk_emp_dept_id` foreign key (dept_id) references tb_dept(id)
)
    comment '员工表';

INSERT INTO tb_emp (username, password, name, gender, image, job, entry_date, dept_id, create_time, update_time)
VALUES ('jinyong', '123456', '金庸', 1, '1.jpg', 4, '2000-01-01', 1, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('zhangwuji', '123456', '张无忌', 1, '2.jpg', 4, '2015-01-01', 2, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('yangxiao', '123456', '杨逍', 1, '3.jpg', 2, '2008-05-01', 2, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('weiyixiao', '123456', '韦一笑', 1, '4.jpg', 2, '2007-01-01', 3, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('changyuchun', '123456', '常遇春', 1, '5.jpg', 2, '2012-12-05', 3, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('xiaozhao', '123456', '小昭', 2, '6.jpg', 4, '2013-09-05', 4, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('jixiaofu', '123456', '纪晓芙', 2, '7.jpg', 1, '2005-08-01', 1, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('zhouzhiruo', '123456', '周芷若', 2, '8.jpg', 1, '2014-11-09', 1, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('dingminjun', '123456', '丁敏君', 2, '9.jpg', 1, '2011-03-11', 4, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('zhaomin', '123456', '赵敏', 2, '10.jpg', 4, '2013-09-05', 5, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('luzhangke', '123456', '鹿杖客', 1, '11.jpg', 1, '2007-02-01', 5, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('hebiweng', '123456', '鹤笔翁', 1, '12.jpg', 1, '2008-08-18', 5, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('fangdongbai', '123456', '方东白', 1, '13.jpg', 2, '2012-11-01', 2, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('zhangsanfeng', '123456', '张三丰', 1, '14.jpg', 4, '2002-08-01', 3, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('yulianzhou', '123456', '俞莲舟', 1, '15.jpg', 2, '2011-05-01', 3, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('songyuanqiao', '123456', '宋远桥', 1, '16.jpg', 2, '2010-01-01', 4, '2022-10-30 14:32:02', '2022-10-30 14:32:02'),
       ('chenyouliang', '123456', '陈友谅', 1, '17.jpg', NULL, '2015-03-21', NULL, '2022-10-30 14:32:02', '2022-10-30 14:32:02');
```

### 11.2 笛卡尔积
多张表直接逗号查询，不添加关联条件，所有行两两组合，产生大量无效数据。
```sql
-- 产生笛卡尔积 17员工 ×5部门 =85条无效数据
select * from tb_emp, tb_dept;
```
✅ **必须添加关联条件消除笛卡尔积**
```sql
select * from tb_emp e,tb_dept d where e.dept_id = d.id;
```

### 11.3 连接查询
#### ① 内连接 `inner join`
只查询**两张表能够匹配上的数据（交集）**，没有匹配数据不会展示
- 隐式内连接（逗号写法，where关联）
```sql
select e.name,d.name from tb_emp e,tb_dept d where e.dept_id=d.id;
```
- 显式内连接（`inner join ... on`，推荐）
```sql
select e.name,d.name 
from tb_emp e 
inner join tb_dept d on e.dept_id = d.id;
```
> `inner` 关键字可以省略

#### ② 外连接
- **左外连接 `left join`**：左边表全部数据，匹配右边表数据；无匹配显示`null`
```sql
-- 查询所有员工，关联所属部门（无部门员工依然展示）
select e.name,d.name 
from tb_emp e 
left join tb_dept d on e.dept_id = d.id;
```
- **右外连接 `right join`**：右边表全部数据，匹配左边表数据；无匹配显示`null`
```sql
-- 查询所有部门，关联对应员工（没有员工的部门依然展示）
select e.name,d.name 
from tb_emp e 
right join tb_dept d on e.dept_id = d.id;
```
> 口诀：左连接保左表，右连接保右表；`outer` 可以省略

### 11.4 子查询（嵌套查询）
一条SQL嵌套另一条select语句，分为四类：
1. **标量子查询**：返回**单个值**
```sql
-- 查询教研部所有员工
select * from tb_emp 
where dept_id = (select id from tb_dept where name='教研部');
```
2. **列子查询**：返回**一列多行**，搭配 `in`
```sql
-- 查询教研部、咨询部员工
select * from tb_emp 
where dept_id in (select id from tb_dept where name='教研部' or name='咨询部');
```
3. **行子查询**：返回**一行多列**
```sql
-- 查询和韦一笑入职日期、职位完全相同的员工
select * from tb_emp 
where (entry_date,job) = (select entry_date,job from tb_emp where name='韦一笑');
```
4. **表子查询**：返回**多行多列**，当作临时表使用
```sql
-- 查询2005-08-01之后入职员工，并关联部门名称
select e.*,d.name
from (select * from tb_emp where entry_date > '2005-08-01') e
join tb_dept d on e.dept_id=d.id;
```

### 11.5 多表开发注意点
1. 多表查询尽量给表起别名，简化书写；
2. 区分 `where` 和 `on`：连接条件写`on`，普通过滤条件写`where`；
3. 避免无索引大表关联，极易造成慢查询。

---

## 十二、事务
### 12.1 事务作用
一组DML语句（insert/update/delete）**要么全部执行成功，要么全部失败回滚**，保证数据完整性。
> MySQL InnoDB引擎支持事务；MyISAM不支持事务。
> MySQL**默认自动提交事务**。

### 基础语法
```sql
start transaction; -- 开启事务

delete from tb_dept where id=2;
delete from tb_emp where dept_id=2;

commit;    -- 提交，永久生效
rollback;  -- 回滚，撤销所有操作
```

### 12.2 事务四大特性 ACID
1. **原子性 Atomicity**：事务不可分割，全部成功或全部失败
2. **一致性 Consistency**：事务执行前后，数据整体约束保持合法
3. **隔离性 Isolation**：多个事务并发执行，互相隔离互不干扰
4. **持久性 Durability**：事务提交后，数据永久保存，宕机不丢失

### 并发事务三大问题
- 脏读：一个事务读到另一个事务未提交数据
- 不可重复读：同一事务内，多次读取同一数据，结果不一致（update）
- 幻读：同一事务范围查询，出现新增/消失的数据（insert/delete）

---

## 十三、索引
### 13.1 索引概念
索引是优化查询的数据结构（InnoDB默认 **B+树**），相当于书籍目录。
✅ 优点：大幅提升查询速度
❌ 缺点：占用磁盘空间；增删改操作需要维护索引，速度下降

### 13.2 InnoDB B+树特点
1. 多路平衡树，层级很低，查询磁盘IO少；
2. 非叶子节点只存储索引键，不存完整数据；
3. **所有真实数据存放在叶子节点**；
4. 叶子节点通过双向链表有序相连，范围查询高效。

### 13.3 索引操作语法
```sql
-- 创建普通索引
create index idx_emp_name on tb_emp(name);

-- 创建唯一索引（字段值不能重复）
create unique index idx_emp_username on tb_emp(username);

-- 查看表索引
show index from tb_emp;

-- 删除索引
drop index idx_emp_name on tb_emp;
```

### 13.4 explain 执行计划
使用 `explain` 分析SQL是否走索引，定位慢查询
```sql
explain select * from tb_emp where username='jinyong';
explain select * from tb_emp where gender=1;
explain format = traditional
select id from tb_emp where name = '金庸' or id = (select id from tb_emp where name = '金庸');
```
重点观察字段：`type`、`key`、`rows`，判断索引生效情况。

### 13.5 简单索引规范
1. 频繁作为查询条件的字段建立索引；
2. 主键自带主键索引，无需重复创建；
3. 不要给重复度极高字段（性别、状态）建立索引；
4. 索引不是越多越好，过多索引拖累增删改。

---

## 开发高频避坑汇总
1. 字符串、日期必须使用**单引号**；
2. 判断空值只能 `is null / is not null`，禁止 `= null`；
3. DQL执行顺序：`from → where → group by → having → select → order by → limit`
4. where分组前过滤，**不能使用聚合函数**；having分组后过滤，可以使用聚合函数；
5. 物理外键考试要写，企业开发普遍使用**逻辑外键**；
6. 大表尽量避开高峰期执行`alter table`，防止锁表；
7. 金额统一使用`decimal`，禁止float/double；时间字段新项目优先`datetime`；
8. 多表连接一定要写关联条件，杜绝笛卡尔积；
9. Datagrip快捷键 `Ctrl+Alt+L` 格式化SQL 2。

