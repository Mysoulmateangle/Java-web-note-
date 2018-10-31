#### SQL基础操作
##### 注解
-- 内容 单行注解
/*内容*/ 多行注解
-- 区分大小写binary
~~~ java
 select * from stu where binary name="WangGu";
~~~

##### 常用约束
~~~ sql
      主键约束：primary key

			唯一约束：unique [key]

 			非空约束：not null

			默认约束：default

			外键约束：foreign key

			自动增长：auto_increment
~~~
##### sql语句分类
>DDL(数据定义语言，用来定义数据库对象：库，表，列)（definnition）

>DML(数据操作语言，用来定义数据库记录)（manipulation）

>DCL(数据控制语言，用来定义访问权限，安全级别)（control）

>DQL(数据查询语言，用来查询记录)（query）

##### DDL操作
1.操作数据库
~~~ sql
创建数据库，设置编码格式和字符集
create database mydb1 character set utf-8 collate gbk_general_ci;

~~~
~~~ sql
修改数据库的属性
alter database mydb1 character set gbk;
~~~
~~~ sql
删除数据库
drop database mydb1;
查询数据库
select database();
切换数据库
use mydb1;
~~~
2.操作表结构
~~~ sql
创建表
create table employes
  (
    id int not null,
    age int not null,
    first varchar(20),
    primary key(id)
  );
~~~
~~~ sql
删除表
drop table employes;
查看表的字段信息
desc employes;
增加一个image列
alter table employes add image blob;
修改列的属性
alter table employes modify first varchar(60);
删除列
alter table employes drop first;
修改表名
rename table employes to employees;
查看表格的创建细节
show create table employes;
修改表的字符集
alter table employes character set jbk;
修改列名
alter table employes change name username varchar(20);
~~~
#### DML操作
***操作表中数据***
~~~ sql
插入数据
insert into employes(id,name)values(1,"叶健");
  修改数据
  update employes set salary=50000;
删除数据
delete from employes where name="叶剑";
truncate删除是直接将表结构删除掉，然后创建一个相同的表
  truncate table employes;

~~~
### DQL操作
***查询数据***
查询语句书写顺序：select – from- where- groupby- having- order by-limit

查询语句执行顺序：from - where -group by -having - select - order by-limit  

~~~ sql
基本查询
select * from stu;
select * from stu where id=1 and name="周";
select * from stu where id=1 or name="周";
select * from stu where id in (1,2,3);
select * from stu where id not in (1,2,3);
select * from stu where age is not null;//查询不为空
select * from stu where age between 20 and 40;(两边都包含)
select * from stu where gender!="male";

~~~
~~~ sql
模糊查询
/查询由两个任意字母构成的学生记录
select * from stu where sname like "__";
/查询第二个字母为i的数据
select * from stu where sname like "_i%";
/查询以z开头的数据
select * from stu where sanme like "z%";
/查询包含a字母的学生记录
select * from stu where sanme like "%a%";

~~~
~~~ sql
去重
select distinct salary from employes;
用其中的数据进行增加一列
select *,sal+comm from employes;
as 增加的列的名字，不会进入数据库
select *,sal+ifnull(comm,0) as total from employes;(as)
~~~
~~~ sql
排序
desc 降序 asc 升序
select * from stu order by name asc;
select * from stu order by name desc;
select * from employes order by sal desc , id asc;
~~~
~~~ sql
聚合函数
select count(*) as cnt from employes;
select sum(sal+ifnull(comm,0)) from employes;
select avg(sal) from employes;
select max(salary),min(salary) from employes;
~~~
~~~ sql
分组查询
select depno,count(*) from employes where salary>1500 group by depno
~~~
~~~ sql
having与where的区别:

having是在分组后对数据进行过滤,where是在分组前对数据进行过滤             

.having后面可以使用分组函数(统计函数)

 where后面不可以使用分组函数。
select depno,sum(salary) from employes group by depno having sum(salary)>9000;
~~~
~~~ sql
分页查询从第一个开始，查询两个
select * from employes limit 0,2;
每页条数，pagesize,index 开始的页码，第i页
limit(index=(i-1)*pagesize，pagesize)
~~~
##### 时间和字符串的处理
~~~ sql
SELECT NOW();-- 2018-10-16 17:52:32
SELECT SYSDATE();-- 2018-10-16 17:52:22
SELECT CURRENT_DATE();-- 2018-10-16
SELECT CURRENT_TIMESTAMP();-- 当前时间挫 2018-10-16 17:53:15
select datediff('2017-07-21', '2017-07-24'); -- -3
SELECT DATEDIFF(CURRENT_DATE(),'2018-10-21');-- -5
 update student set hire_date='2017/07/21' where sid=1;-- 四种方式都一样
 update student set hire_date='2017-07-21' where sid=1;
 update student set hire_date=CURRENT_DATE() where sid=2;
 update student set hire_date='2017年07月21日' where sid=1;
 select length('hello');  字符串长度
 select length(name) as l,name from student where length(name) >5;
 select strcmp('world','hello'); 字符串比较大小，0表示相同，1表示前面比后面大，-1前面比后面小
 select concat('haah', 'aaa', 'bbb'); 字符串合并
 select substring('hello', 2); 字符串截取，索引从1开始
 select substring('hello', 2, 2); 第三个参数表示截取的长度
 select trim('  hello  ');  去除两端的空格
 select instr('hello', 'el');  子串的查询，返回查到的索引值，如果不存在，返回0
~~~
