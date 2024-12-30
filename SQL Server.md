# SQL Server

数据库概念： 管理数据的仓库

创建数据库如下

~~~sql
if exists(select * from sys.databases where name = 'DBTEST')
drop database DBTEST

--创建数据库
create database DBTEST
on --数据文件
(
	name = 'DBTEST', -- 逻辑名称
	filename = 'D:\DBDATATEST\DBTEST.mdf',--物理路径和名称
	size =5MB,--文件初始大小
	filegrowth = 2MB --文件的增长方式可以写大小，也可以写百分比
)
Log on --日志文件
(
	name = 'DBTEST_log', -- 逻辑名称
	filename = 'D:\DBDATATEST\DBTEST_log.ldf',--物理路径和名称
	size =5MB,--文件初始大小
	filegrowth = 2MB --文件的增长方式可以写大小，也可以写百分比
)
~~~

表创建如下

~~~sql
-- 判断表是否存在
if exists(select * from sys.objects where name = 'Department'and type = 'U')
drop table Department
-- 创建表
create Table Department(
   	-- 部门编号
	DepartmentID int primary key identity(1,1),
   	-- 部门名称 
	DepartmentName varchar(50) not null,
	-- 部门描述
	DEpartmentRemark text
)
~~~

## 数据类型

​	微软文档： 

[微软数据类型官方文档]: https://learn.microsoft.com/zh-cn/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver16	"数据类型"



- char : 定长
- varchar： 变长
- text： 长文本
  - char varchar text 前边加n 存储Unicode字符，对中文友好
  - varchar(100) 存储100个字母或者50个汉字
  - nvarchar(100) 存储一百个汉字或字母

## 表操作

~~~sql
-- 添加列
alter table Employee add EmployeeMail varchar(200)
-- 删除列
alter table Employee drop column EmployeeMail
-- 修改列
alter table Employee alter column EmployeeMail varchar(200) -- 不建议使用会截断字符串


-- 维护约束（删除、添加）
-- 删除约束
-- 删除月薪的约束
alter table Employee drop constraint CK__Employee__Employ__48CFD27E
-- 添加约束（check约束）
-- 添加工资字段约束 在1000-10000之间
alter table Employee add constraint CK__Employee__Employ__48CFD27E check(EmployeeSalary>=1000 and EmployeeSalary <=1000000)

~~~



## 三范式

1.原子性

~~~sql
-- 没有将邮箱和qq分开
create table Student( --学生表
StuId varchar(20)primary key,--学号
StuName varchar(20) not null,--学生姓名
StuContact varchar(50) not null, --联系方式
)

insert into Student(StuId,StuName,StuContact)
values('001','刘备','QQ:185699887;Tel:13885874587')

select * from Student;
~~~

2. 唯一性

~~~sql
~~~

3. 不能有冗余



`业务需求说明
-模拟银行业务，设计简易版的银行数据库表结构，要求可以完成以下基本功能需求
-1.银行开户(注册个人信息)及开卡(办理银行卡)(一个人可以办理多张银行卡，但是最多只能办理3张
--2.存钱
-3.查询余额
--4.取钱
--5.转账
.-6.查看交易记录
-7.账户挂失
--8.账户注销
表设计--------
1.账户信息表，
-2.银行卡表
-3.交易信息表(存储存钱和取钱的记录 )
.-4.转账信息表(存储转账信息记录 )
.-5.状态信息变化表(存储银行卡状态1:正常,2:挂失,3:冻结,4:注销)`





## T-SQL

```
declare  声明变量
set 设置变量指定的值
select 赋值表中查询的值 如果查询结果有多个，取最后一条数据

go 等待之前的代码执行完毕才可以执行后边的语句（2）作为一个批处理结束的标志
```



### 常用全局变量



> 1. @@ERROR
>    - **解释**：返回执行的上一个 Transact - SQL 语句的错误号。如果前一个语句成功执行，返回 0；若发生错误，则返回错误号（非零值）。这有助于在事务处理或批处理操作中检测错误，并根据错误情况采取相应的措施，如回滚事务。
>    - **应用场景示例**：在存储过程中用于错误处理，当执行多个可能出错的 SQL 操作时，通过检查`@@ERROR`的值来判断是否需要回滚事务以保持数据一致性。
> 2. @@IDENTITY
>    - **解释**：**返回最后插入的标识值**。通常用于在插入数据到包含标识列（例如具有`IDENTITY`属性的列）的表后，获取刚刚插入行的标识列的值。
>    - **应用场景示例**：在插入新记录到具有自动递增主键的表后，可立即使用`@@IDENTITY`获取新记录的主键值，以便用于后续操作，如插入关联表中的记录。
> 3. @@ROWCOUNT
>    - **解释**：返回受上一个语句影响的行数。对于`INSERT`、`UPDATE`、`DELETE`等操作，可以通过它来确定实际操作影响的记录数量，这在需要验证操作范围或进行进一步处理（如日志记录）时非常有用。
>    - **应用场景示例**：在执行批量更新操作后，使用`@@ROWCOUNT`来统计更新的记录数，根据这个数量决定是否需要发送通知或者进行其他关联操作。
> 4. @@SERVERNAME
>    - **解释**：返回运行 SQL Server 实例的服务器名称。可以用于在多服务器环境中区分不同的服务器，或者在配置相关操作（如分布式事务）时获取服务器标识。
>    - **应用场景示例**：在分布式数据库系统中，通过`@@SERVERNAME`来确定当前操作是在哪个服务器上执行的，以便进行适当的路由或数据同步操作。
> 5. @@TRANCOUNT
>    - **解释**：返回当前连接的活动事务数。用于确定是否处于事务嵌套中以及嵌套的深度，有助于正确地管理事务的提交和回滚。
>    - **应用场景示例**：在复杂的事务处理逻辑中，尤其是涉及嵌套事务的情况下，通过`@@TRANCOUNT`来判断事务的层次结构，确保事务的正确提交和回滚顺序。
> 6. @@VERSION
>    - **解释**：返回 SQL Server 实例的详细版本信息，包括版本号、处理器体系结构、版权信息等。这对于了解数据库系统的功能支持、兼容性以及安全更新等方面非常重要。
>    - **应用场景示例**：在系统升级或部署新功能之前，通过查询`@@VERSION`来确定当前 SQL Server 版本是否满足新功能的要求，或者用于记录系统环境信息。
> 7. @@LANGUAGE
>    - **解释**：返回当前使用的语言名称。SQL Server 支持多种语言设置，这个变量可以显示当前会话所使用的语言设置，语言设置会影响日期格式、系统消息等内容。
>    - **应用场景示例**：在跨国企业的数据库环境中，不同地区的用户可能使用不同的语言设置，通过`@@LANGUAGE`可以了解当前用户会话的语言环境，以便提供相应语言的错误消息或日期格式。
> 8. @@DATEFIRST
>    - **解释**：返回当前会话中设置的一周的第一天是星期几。它的值可以是 1（星期一为一周的第一天）到 7（星期日为一周的第一天）之间的整数，这会影响与日期相关的函数和计算。
>    - **应用场景示例**：在进行基于星期的统计分析或日期范围计算时，需要考虑`@@DATEFIRST`的值，以确保日期计算的准确性符合业务需求。
> 9. @@DBTS
>    - **解释**：返回当前数据库的时间戳值。时间戳用于在数据库中记录数据的修改顺序，它可以帮助跟踪数据库的更改情况，不过在高并发环境下可能不太可靠。
>    - **应用场景示例**：在简单的数据库变更跟踪系统中，`@@DBTS`可以作为一个辅助手段来大致了解数据库中数据的修改顺序，用于简单的审计或数据同步检查。



### 运算符

```
算术运算符：+、-、*、/、%
比较运算符：=、<>、!=、>、<、>=、<=
逻辑运算符：AND、OR、NOT EXIST、NOT、ALL、ANY
位运算符：&、|、^、~
字符串连接运算符：+
赋值运算符：=
其他运算符：BETWEEN...AND、IN、LIKE
```

### 常用函数

```sql
convert(varchar(10),要转换的变量) --前边是要转换的类型，后边是变量名
cast(@area as varchar(10)) cast函数也可以用来转换

开窗dense_rank() over(partition by sth order by sth)，先分组然后排序取前三
rank 是跳次排序 1,1,1,4, dense_rank是不跳次排序1,1,1,2, row_number是顺序1,2,3,4

dense_rank() over(partition by departmentId order by salary desc) ranks --partition by分组进行， order by 排序
```



### 分页

~~~sql
--- 两种办法

--- 第一种
select top 5 * --- 读取五行数据
from "StatusInfo"
where "StatusID" not  in (select top 10 "StatusID"  from "StatusInfo") --- 读取不在前十行的数据，类似于跳过的页

--- 第二种
SELECT * FROM Employees
ORDER BY EmployeeID
OFFSET 10 FETCH NEXT 10 ROWS ONLY;
---
OFFSET 10 -- 表示跳过十行数据
FETCH NEXT 10 ROWS ONLY; --表示读取跳过页数后的十行数据
--- 第三种
select * from
(select ROW——NUMBER() over(order by StuId) RowId, * from Student)
where between 1 and 5
~~~

#### 事务

~~~sql
begin transaction

commit 
rollback
~~~

### 索引

1. 聚集索引
   - 定义：决定表数据物理存储顺序，一个表只有一个。
   - 应用场景：用于范围查询和排序操作频繁的列。
   - 示例：以员工编号为聚集索引，数据按员工编号排序存储。
2. 非聚集索引
   - 定义：独立于数据行存储，包含索引列值和指向数据行的指针，一个表可有多个。
   - 应用场景：常用于查询频繁但不用于排序或范围查询的列。
   - 示例：在员工姓名列创建非聚集索引用于快速查找员工。
3. 唯一索引
   - 定义：确保索引列值唯一，可为聚集或非聚集索引。
   - 应用场景：用于保证数据唯一性的列，如身份证号。
   - 示例：在用户电子邮件列创建唯一索引防重复注册。
4. 全文索引
   - 定义：用于文本数据的全文搜索，提取关键词建立索引。
   - 应用场景：适合含大量文本的表，用于模糊或关键词搜索。
   - 示例：在博客文章内容列创建全文索引来查找主题文章。
5. 复合索引
   - 定义：基于多个列创建，按指定顺序包含多列值。
   - 应用场景：查询条件常涉及多列组合时使用。
   - 示例：在订单表对客户 ID、订单日期和总金额创建复合索引。

~~~.sql
--- 创建索引
create UNIQUE NONCLUSTERED index index_CIDNumber
on dbo.AccountInfo(CustomerIDNumber)
--- 查询索引
select * from sys.indexes where name = 'index_CIDNumber'
--- 删除索引
drop index index_CIDNumber on AccountInfo
--- 查询 通过索引
select * from AccountInfo with(index = index_CIDNumber)
where CustomerIDNumber = '110101199001011234'
~~~

### 视图

~~~sql
--- 创建视图
create view View_Account_Card
as
    select b."CardID" as 卡号, b."CardNumber" as 身份证, a."CustomerName" as 姓名 , b."Balance" as 余额
    from "AccountInfo" a inner JOIN "BankCard" b on a."AccountID" = b."AccountID" 
go
~~~

### 游标

#### 单列游标

~~~sql
--- 创建游标
DECLARE mycur CURSOR SCROLL
for  select CustomerName from AccountInfo

--- 打开游标
OPEN mycur

--- 提取某行数据
FETCH first from mycur -- 第一行
FETCH last from mycur -- 最后一行
fetch absolute 2 from mycur -- 第二行
fetch relative 2 from mycur -- 当前行向下两行
fetch next from mycur -- 下移一行
fetch prior from mycur -- 上移一行

-- 提取游标数据存入变量，进行查询所有列信息
declare @acc varchar(20)
fetch absolute 2 from mycur into @acc
select * from AccountInfo where CustomerName  = @acc

-- 遍历游标
declare @acc varchar(20)

fetch absolute 1 from mycur into @acc
-- @fetch_status: 0 提取成功 , -1 失败，-2 不存在
while @@FETCH_STATUS = 0
	begin
		print '提取成功 '+ @acc
		fetch next from mycur into @acc
	end
-- 利用游标进行数据的修改和删除
select * from AccountInfo
fetch absolute 2 from mycur
update AccountInfo set CustomerContact = '13812345678' where current of mycur -- 修改游标所在行的手机号
fetch last from mycur
delete from AccountInfo where current of mycur -- 删除游标所在行的内容

--- 关闭标游
close mycur
--- 删除游标
DEALLOCATE mycur
~~~

#### 多列游标

~~~sql
--- 创建某行指向多列的游标，循环显示多列数据
DECLARE mycur CURSOR SCROLL
for  select Account, pwd, name  from member;
-- 打开游标
open mycur
-- 遍历游标
declare @acc varchar(20)
declare @pwd varchar(20)
declare @name varchar(20)
fetch absolute 1 from mycur into @acc, @pwd,@name
-- @fetch_status: 0 提取成功 , -1 失败，-2 不存在
while @@FETCH_STATUS = 0
	begin
		print '用户名:'+ @acc+'密码:'+@pwd+'名字:'+@name
		fetch next from mycur into @acc, @pwd, @name
	end

-- 关闭游标
close mycur
-- 删除游标
DEALLOCATE mycur
~~~



#### 完整性

- 域完整性

  - 检查约束

  - 默认约束

  - 非空约束

- 实体完整性

  - 主键约束
  - 唯一约束
  - 标识列

- 引用完整性

  - 外键约束

- 自定义完整性

  - 触发器

#### 数据库操作语句

- 增

~~~sql server
insert into 表名(列1，列2，列3...) values(值1，值2...)
~~~

- 删	

~~~sqlserver
delete from 表名 where 条件
~~~

- 改

~~~sql server
Update 表名 set 列1 = 新值 , 列2 = 新值
~~~

- 查

~~~sql server
select 列1 , 列2 
from 表名
where 条件
group by 列
having条件
order by 列[ASC/DESC]
~~~

#### 聚合函数

- max(列)
- min()
- sum()
- avg()
- count()



### 触发器

```

```



# 变量

- **全局变量（Global Variable)** @@
- **局部变量(Local Variable)**@