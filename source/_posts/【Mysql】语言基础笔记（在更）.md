---
title: 【Mysql】语言基础笔记（在更）
date: 2021-04-28 17:57:17
author: 黄丕舜
tags: Mysql
categories: Mysql
comments:
---
## 一、基本概念
- 表名是唯一的
- 表中的数据是按行存储的
- 行（别名 记录）
- 主键：一列，其值能够区分表中每个行
	- 每个行都必须有一个主键值（不允许为NULL）
	- 任意两行都不具有相同的主键值
	- 不更新主键列中的值
	- 不在主键列中使用可能会更改的值
- 命令用分号；结束
- 连接到mysql需要以下信息
	- 主机名：本地服务器为localhost
	- 端口：默认为3036
	- 一个合法的用户名
	- 用户口令（如果需要的话）
- `use<数据库名字>`来选择数据库
- `show databases`返回可用数据库的一个列表
- `show tables`返回该数据库内的可用表的一个列表
- `show columns from <表名>` = `describe <表名>`来显示表中的所有列
- 自动增量：Mysql可以自动地为每个行分配下一个可用的编号，需要create语句
- `show status`显示广泛的服务器状态信息
- `show create databases/table`分别用来显示创建特定数据库或表的语句
- `show grants`显示授予用户的安全权限
- `show errors`和`show warnings`显示服务器错误或警告信息

## 二、SELECT语句
- `SELECT <列名> FROM <表名>`在表中检索单个列
>SELECT检索多个列，列名用逗号分隔
>检索所有列，用`*`通配符
- sql语句不区分大小写
- SQL开发人员喜欢对所有SQL关键字使用大写，而对所有列和表名使用小写
- 处理SQL语句空格会被忽略
- `SELECT DISTINCT ... FROM`返回不重复的行（distinct要放在列名前面）
- 使用完全限定的表名`SELECT db.host FROM mysql.db`
- `FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT`是`SELECT`的子句，其出现次序必须为上述顺序，次序不对将产生错误信息
- `ORDER BY`子句取一个或多个列的名字对输出进行排序
>默认为asc升序，在列名后加`DESC`关键字按降序排列，desc关键字只应用到直接位于其前面的列名
- `GROUP BY`子句，分组允许把数据分为多个逻辑组
- `HAVING`和`WHERE`的区别：`HAVING`过滤分组，`WHERE`过滤行
- `WHERE`是`SELECT`的子句（在from子句之后给出）用来指定搜索条件
- **WHERE子句操作符**

|操作符 |说明  |
|:--:|:--:|
| = | 等于 |
| <>| 不等于 |
|!=|不等于|
|BETWEEN|在指定的两个值之间|
- 空值检查(`is NULL`子句)`where...is NULL;`
- `WHERE`子句给出了`AND, OR, IN, NOT`逻辑操作符来进行逻辑操作

## 三、匹配
- 通配符本身实际是SQL的`WHERE`子句中有特殊含义的字符，在搜索子句中使用通配符，必须使用`LIKE`操作符，`LIKE`只是Mysql后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较
- 百分号`%`通配符匹配任意个数的任意字符，下划线`_`通配符匹配任意单个字符
- 使用通配符的技巧
	- 不要过度使用通配符
	- 把通配符置于搜索模式的开始处搜索起来是最慢的
- `WHERE`的子句中`REGEXP`子句使用正则表达式，在`REGEXP`关键字后加上`BINARY`来区分匹配表达式的大小写
例：	
	```
	SELECT Db, User FROM db 
	WHERE Db REGEXP BINARY '.ys';
	```
- 匹配正则表达式内具有特殊意义的所有字符都必须以\\\的方式转义

**常用字符类**

|类|说明  |
|:--:|:--:|
|[:alnum:]  |任意字母和数字（同[a-zA-Z0-9])  |
|[:alpha:] |任意字符（同[a-zA-Z]）|
|[:blank:]|空格和制表|
|[:cntrl:]|ASCII控制字符|
|[:digit:]|任意数字|
|[:graph:]|与[:print:]相同，但不包括空格|
|[:lower:]|任意小写字母|
|[:print:]|任意可打印字符|
|[:space:]|任意空白字符|
|[:upper:]|任意大写字母|
|[:xdigit:]|任意十六进制数|

**重复元字符**

|元字符  |说明  |
|--|--|
| * |0个或多个匹配  |
| +|1个或多个匹配（等于{1,}）|
|?|0个或1个匹配（等于{0,1}）|
|{n}|指定数目的匹配|
|{n,}|不少于指定数目的匹配|
|{n,m}|匹配数目的范围|

**定位元字符**

|元字符  |说明  |
|:--:|:--:|
| ^ | 文本的开始 |
|$|文本的结束|
|[[:<:]]|词的开始|
|[[:>:]]|词的结束|
- 正则表达式中`.`表示匹配任意一个字符
- `LIKE`与`REGEXP`的区别，`LIKE`匹配整个串，`REGEXP`匹配子串
- 简单的正则表达式测试，例：`SELECT 'hello' REGEXP '[0-9]';`
- `SELECT`语句中，`concat()`函数用来拼接字段（与列的意思相同），别名用`AS`关键字赋予，`RTrim()`函数去掉右边的所有空格
例：
	```
	SELECT concat(Db, '(', User, ')') AS link
	FROM db;
	```

## 四、相关函数
- `Soundex()`函数匹配所有发音类似与Y.Lie的联系名
例：
	```
	SELECT cust_name, cust_contact
	FROM customers
	WHERE Soundex(cust_contact) = Soundex('Y. Lie');
	```
**常用数值处理函数**

| 函数 |说明  |
|:--:|:--:|
|  Abs()| 返回一个数的绝对值 |
|Cos()|返回一个角度的余弦|
|Exp()|返回一个数的指数值|
|Mod()|返回余数|
|Pi()|返回圆周率|
|Rand()|返回一个随机数|
|Sin()|返回一个角度的正弦|
|Sqrt()|返回一个角度的平方根|
|Tan()|返回一个角度的正切|

**常用时间和日期处理函数**

|函数  |说明  |
|:--:|:--:|
|AddDate()  |增加一个日期（天、周等) |
|AddTime()|增加一个时间（时、分等）|
|CurDate()|返回当前日期|
|Date()|返回日期时间的日期部分|
|DateDiff()|计算两个日期之差|
|Date_add()|高度灵活的日期运算函数|
|Date_Format()|返回格式化的日期或时间率|
|Day()|返回一个日期的天数部分|
|DayOfWeek()|对于一个日期，返回对应的星期|
|Hour()|返回一个日期的小时部分|
|Minute()|返回一个日期的分钟部分|
|Month()|返回一个日期的月份|
|Now()|返回当前日期和时间|
|Second()|返回一个时间的秒部分|
|Time()|返回一个日期时间的时间部分|
|Year()|返回一个日期的年份部分|
- 子查询，嵌套`SELECT`语句
例：
	```
	SELECT cust_id
	FROM orders
	WHERE order_num IN (SELECT order_num
						FROM orderitems
						WHERE prod_id = 'TNT2');
	```

## 五、联结表
- `WHERE`子句在联结表中起配对的作用（用完全限定列名）
- 没有联结条件的表关系返回的结果为笛卡尔积（检索出的行的数目将是第一个表中的行数乘以第二个表中的行数）
- 应保证所有联结都有`WHERE`子句
- 使用在`WHERE`子句中使用`AND`可以联结多个表
- 等值连接也称为内部联结
- 尽量使用自联结而不用子查询
- **联结的方式**
![联结](https://img-blog.csdnimg.cn/20210428125629111.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2ODAyNzEz,size_16,color_FFFFFF,t_70#pic_center)
- 外键：外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系
- 外键的意义：在于解耦合，定义外键的方法`FOREIGN KEY (主键）references <表名>(另一个表的主键）`

## 六、组合查询
- 组合查询，必须由两条`SELECT`语句组成，语句之间用`UNION`关键字分隔
- `UNION`的每个查询必须包含相同的列，表达式或聚集函数
- 使用`UNION`时重复的行会被自动取消（默认行为），若想返回所有的匹配行可以用`UNION ALL`
- 在使用`UNION`组合查询时，只能使用一次`ORDER BY`子句，它必须出现在最后一条`SELECT`语句之后

## 七、全文本搜索
- 全文本搜索会对结果排序，具有较高优先等级（文本中靠前的等级高）的行先返回，搜索默认不区分大小写
- 在创建表时跟句子句`Fulltext(列名)`对它进行索引，函数`Match()`指定被搜索的列，函数`Against()`指定要使用的搜索表达式，搭配`WHERE`子句进行筛选
- 使用查询扩展`Against('anvils' WITH QUERY EXPANSION)`，找出和搜索相关的其他行

## 八、操作数据
- #### 插入数据
	- `INSERT INTO <表名> (需要插入值的列名) values (给每个列提供一个值)`
	- 没有列出的值默认为NULL
	- `INSERT INTO`搭配`SELECT`可以从旧表复制数据到新表
- #### 更新数据
	- `UPDATE <表名> SET 列=值`
	- 要更新的表
	- 列名和他们的新值
	- 确定要更新行的过滤条件
- #### 删除数据
	- `DELETE FROM <表名> WHERE 匹配的列`
	- 删除表中所有行`TRUNCATE TABLE`语句
- #### 创建表
	- `CREATE TABLE <表名>(行及相应值)`
	- 表的主键可以在创建表时用`PRIMARY KEY`关键字指定，主键值必须唯一，多个主键使用`,`分割
	- 若仅想在一个表不存在时创建它，应该在表名后给出`if not exists`
	- 指定`not null`时在插入或更新行时，该列必须有值
	- `AUTO_INCREMENT`告诉mysql每当增加一行时自动增量
	- 每个表只允许一个`AUTO_INCREMENT`列
- #### 更新表
	- `ALTER TABLE <表名> ADD <列> 列的数据类型`
- #### 删除表
	- `DROP TABLE <表名>`
- #### 重命名表
	- `RENAME TABLE <表名> to <重命名的表名>` 