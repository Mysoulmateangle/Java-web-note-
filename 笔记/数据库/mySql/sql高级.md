### mysql进阶
#### 数值约束，数据库约束，视图，索引，触发器
##### 多表查询
**1.多表的关系**
>一对多关系(客户和订单，分类和商品，部门和员工)
在多的一方创建一个字段，字段作为外键指向一的一方的主键

>多对多关系（学生和课程）
需要创建第三张表，中间表至少两个字段，这两个字段分别作为外键指向各自一方的主键

>一对一关系
...........

**2.多表查询**

~~~ sql
/*
t1
han	1212312	1
dada	13131	2
231	2323	3
23	32	4
2	2	5
t2
eqe	12	男	1
we	21	女	2
2wqe	212	女	3
dsa	123	女	4
EW	213	男	5
ww	2	女	6
*/
1.合并查询
 -- union查询去重，union all查询不去重
select * from t1 union select * from t2;
select * from t1 union all select * from t2;
 -- 被合并的两个结果必须列数。列类型相同
2.连接查询
***连接查询心得***
**连接查询心得**：
连接不限与两张表，连接查询也可以是三张、四张，甚至N张表的连接查询。通常连接查询不可能需要整个笛卡尔积，而只是需要其中一部分，那么这时就需要使用条件来去除不需要的记录。这个条件大多数情况下都是使用主外键关系去除。
两张表的连接查询一定有一个主外键关系，三张表的连接查询就一定有两个主外键关系，所以在大家不是很熟悉连接查询时，首先要学会去除无用笛卡尔积，那么就是用主外键关系作为条件来处理。如果两张表的查询，那么至少有一个主外键条件，三张表连接至少有两个主外键条件*。*

连接查询就是求出两个表的乘积，例如
select * from t1,t2; -- 每个表6条数据
SELECT COUNT(*) as 总条数 from t1,t2;-- 总共36条
2.1内连接
特点：查询结果必须瞒住条件
[inner] join on
select * from t1 inner join t2 on t1.id=t2.t2id;
/*
han	1212312	1	eqe	12	男	1
dada	13131	2	we	21	女	2
231	2323	3	2wqe	212	女	3
23	32	4	dsa	123	女	4
2	2	5	EW	213	男	5
*/
2.2外连接
特点：查询结果可能不满足条件
left outer join on
左连接是先查询出左表（即以左表为主），然后查询右表，右表中满足条件的显示出来，不满足条件的显示NULL
select * from t1 left outer join t2 on  t1.id=t2.t2id;
/*
han	1212312	1	eqe	12	男	1
dada	13131	2	we	21	女	2
231	2323	3	2wqe	212	女	3
23	32	4	dsa	123	女	4
2	2	5	EW	213	男	5
*/
right outer join on
右连接就是先把右表中所有记录都查询出来，然后左表满足条件的显示，不满足显示NULL
select * from t1 right outer join t2 on  t1.id=t2.t2id;
/*
han	1212312	1	eqe	12	男	1
dada	13131	2	we	21	女	2
231	2323	3	2wqe	212	女	3
23	32	4	dsa	123	女	4
2	2	5	EW	213	男	5
null null null	ww	2	女	6
*/

3.子查询
SELECT * FROM emp WHERE sal > ALL (SELECT sal FROM emp WHERE deptno=30)
~~~
##### 数据约束
 ***数据约束告诉我们存储的值需要满足的规则***
 ~~~ sql
 1.设置默认值 default
 create table preson
   (
     did int,
     dname varchar(10) default "张明阳"
   );
  2.非空
  3.唯一约束 unique
  create table person
    (
      id int unique,
      name varchar(10)
    );
    4.自增 auto increment
    5.主键 primary key
    create table person
      (
        id int primary key,
        name varchar(10)
      );
      6.外键 foreign key
      alter table employes add foreign key ("dsad") references depatment ('if');


 ~~~
##### 视图
通过视图，将某个表或多个表的字段提取出来，形成一个新的表
如果不想让用户看到所有数据（字段，记录），只想让用户看到某些的数据时，此时可以使用视图;当需要减化SQL查询语句的编写时，可以使用视图，但不提高查询效率

视图是一种虚表
视图建立在已有表的基础上, 视图赖以建立的这些表称为基表
向视图提供数据内容的语句为 SELECT 语句,可以将视图理解为存储起来的 SELECT 语句
视图没有存储真正的数据，真正的数据还是存储在基表中
程序员虽然操作的是视图，但最终视图还会转成操作基表，一个基表可以有0个或多个视图

Sql语句：
CREATE
VIEW `NewView`AS
SELECT
…
FROM
…

##### 索引
1 什么是索引
索引是一种快速查询表中内容的机制，使用索引可以提高查询效率，相当于字典的目录
可以将查询过程中经常使用的条件设为索引
2 mysql中索引的类型
1）普通索引
这是最基本的索引类型，而且它没有唯一性之类的限制。普通索引可以通过以下几种方式创建：
创建索引，例如CREATE INDEX <索引的名字> ON tablename (列的列表);
修改表，例如ALTER TABLE tablename ADD INDEX [索引的名字] (列的列表);
创建表的时候指定索引，例如CREATE TABLE tablename ( [...], INDEX [索引的名字] (列的列表) );
2）唯一性索引
这种索引和前面的“普通索引”基本相同，但有一个区别：索引列的所有值都只能出现一次，即必须唯一。唯一性索引可以用以下几种方式创建：
创建索引，例如CREATE UNIQUE INDEX <索引的名字> ON tablename (列的列表);
修改表，例如ALTER TABLE tablename ADD UNIQUE [索引的名字] (列的列表);
创建表的时候指定索引，例如CREATE TABLE tablename ( [...], UNIQUE [索引的名字] (列的列表) );
3）主键索引
主键是一种唯一性索引，也可称为聚集索引，但它必须指定为“PRIMARY KEY”。主键一般在创建表的时候指定，例如“CREATE TABLE tablename ( [...], PRIMARY KEY (列的列表) ); ”。但是，我们也可以通过修改表的方式加入主键，例如“ALTER TABLE tablename ADD PRIMARY KEY (列的列表); ”
4）全文索引
全文索引(全文检索)能够利用分词技术等多种算法，按照一定的算法规则智能地筛选出我们想要的搜索结果。
MySQL从3.23.23版开始支持全文索引和全文检索。在MySQL中，全文索引的索引类型为FULLTEXT。全文索引可以在VARCHAR或者TEXT类型的列上创建。它可以通过CREATE TABLE命令创建，也可以通过ALTER TABLE或CREATE INDEX命令创建。对于大规模的数据集，通过ALTER TABLE（或者CREATE INDEX）命令创建全文索引要比把记录插入带有全文索引的空表更快。
需要注意的是，MySQL自带的全文索引只能用于数据库引擎为MyISAM的数据表，如果是其他数据引擎，则全文索引不会生效。

3 什么情况下创建索引
1）表经常进行 SELECT 操作
2）表很大(记录超多)，记录内容分布范围很广
3）列名经常在 WHERE 子句或连接条件中出现
注意：符合上述某一条要求，都可创建索引，创建索引是一个优化问题，同样也是一个策略问题

4索引的缺点
首先，索引要占用磁盘空间。通常情况下，这个问题不是很突出。但是，如果你创建每一种可能列组合的索引，索引文件体积的增长速度将远远超过数据文件。如果你有一个很大的表，索引文件的大小可能达到操作系统允许的最大文件限制。
第二，对于需要写入数据的操作，比如DELETE、UPDATE以及INSERT操作，索引会降低它们的速度。这是因为MySQL不仅要把改动数据写入数据文件，而且它还要把这些改动写入索引文件。

-- 100万条数据，查询时有无索引，时间差距明显--
select * from user where username='zz345';

##### 触发器
触发器在数据库里以独立的对象存储，触发器是由一个事件来启动运行。即触发器是当某个事件发生时自动地隐式运行，并且，触发器不能接收参数
1 要素
针对哪个表
针对哪些操作触发：insert update delete
什么时候触发： after before
触发什么操作：根据具体需求写相关sql语句

2 创建触发器
CREATE TRIGGER `delTri`
AFTER DELETE ON `detail`
FOR EACH ROW
begin

update goods set stock=stock+old.num where id=old.gid;

end;

new表示新插入的数据
update goods set stock=stock-new.num where id=new.gid;

old表示删除的数据
update goods set stock=stock+old.num where id=old.gid;

更新时，old表示更新前数据，new表示更新后数据
update goods set stock=stock+old.num-new.num where id=new.gid;
