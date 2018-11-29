pHibernate注释映射
一、PO类的基本注释
1、@Entity：将pojo类标记成实体，可以指定一个name属性，指定实体类的名称。默认一该类的类名作为实体类的名称

2、@Table：注释改持久化类所映射的表
       属性有① catalog 将持久化类映射的表放入指定的catalog中，如果没指定，则放入默认的catalog
                 ② index为持久化类映射的表设置索引，值是一个@Index注解数组
                ③schema 将持久化类映射的表放入指定的schema中，如果没指定，则放入默认的schema
               ④name 设置持久化类的表名，默认是表名和持久化类的类名一致
             ⑤ uniqueConstraints 为持久化类映射的表设置唯一约束，属性值可以是一个@UniqueConstraints(columnNames属性配置)注释数组

3、@UniqueConstraints：用于数据表的唯一约束 columnNames属性 是一个字符串数组，每一个元素代表一个数据列

4、@Index用于数据库建立索引
属性有 ①、cloumnList(必须要)：设置对那些列表建立索引，可以指定多个数据列的列名
     ②、name ：指定该索引的名字
     ③、unique：设置该索引是否具有唯一性，该属性的值只能是boolean值

5、@Proxy：该注解的proxyClass属性指定一个借口，在延迟加载时作为代理使用，也可以指定该类自己的名字

6、@DynamicInsert：指定用于插入记录的insert语句是否在运行时动态生成。并且只插入那些非空字段。默认值时false

7、@DynamicUpdate：指定用于更新巨鹿的update语句是否在运行是时动态生成，并且只更新那些修改过的字段，默认值是否false
当程序打开了@DynamicUpdate之后持久化注解可以指定如下几种乐观锁定的策略
OptimisiticLockType.VERSION：检查version/timestamp字段
OptimisiticLockType.ALL：查看全部字段
OptimisiticLockType.DIRTY：只检查修改过的字段
OptimisiticLockType.NONE：不使用乐观锁定

8、@SelectBeforeUpdate：制动Hbiernate在更新某个持久化对象之前是否进行一次select（建议false）

9、@PolymorphismType::用于指定是否需要采用隐世多台查询
二、映射属性（针对@Entity的持久化类）
1、@Clounm：指定某个属性所映射的数据列的详细信息(列名,字段长度等)

columnDefinition 属性值时一个代表列定义的SQL字符串(列名后面部分),指定创建该数据列的SQL语句

inserttable：指定该列是否包含在Hibernate生成的的insert语句的列表中，默认值是true

name：指定该列的列名。默认值是成员的变量名

nullable：指定该列是否于必须空，默认值是true

precision：当该列是decimal属性时，该属性指定支持的最大有效位数

scale：当该列为decimal类型时，该列支持的最大小数位数

table：指定该列所属的表名

unique：指定该属性是否具有唯一约束，默认值时false，不具有

updatetable：指定该列是否在Hibernate生成的updare语句的列列表中，默认值为true



2、@Generated：指定该属性映射的书酷烈的值是否由数据库生成，该注解的value属性可以接受GenerationTime.NEVER(不由数据库生成) GenerationTime.INSERT(只在INSERT语句是生成) GenerationTime.ALWAYS(均会生成)



3、@Formula：指定一个SQL表达式，该属性的值根据表达式计算（属性值时动态的，所以无需扒皮存在数据库）

属性值value允许包含对象属性表达式，包括一些求值的SQL函数



4、@Transient：修饰不想持久保存的属性



5、@Enumerated修饰枚举类型的属性(需要一个专门的枚举类配合使用)



6、@Lob,@Basic：用于修饰大数据类型的属性



7、@Temproal：修饰日期类型的属性 value属性取之有 TemporalType.DATA/TemporalType.TIME/TemporalType/TIMESTAMP

三、映射主键（@id）
1、@GeneratedValue 自动生成值
strategy：指定主键列表的生成策略 GenerationType.AUTO/GenerationType.IDENTITY（对于MYSQL/SQL Server自增长的生成策略）/GenerationType.SEQUENCE（对于ORACLE自增长的生成策略，配合@SequenceGenerator）/GenerationType.TABLE（使用辅助表生成主键，配合@TaaleGenerator）
genetator：用于配合GenerationType.SEQUENCE、GenerationType.TABLE使用
四、Hibernate的主键生成策略（@GenericGenerator--定义主键生成器）
两个属性：1、name：设置主键生成器的名称，这个名称可以被@GeneratedValue的generarot引用。
   2、strategy：必须属性，用于设置主键的生成策略
生成策略有  1、increment： 位long、short或者int类型的主键生成唯一标识。（集群不要使用）
     2、identity:：返回的标识属性值是long、short、int类型的
     3、sequence：返回的标识属性值是long、short、int类型的
     4、hilo/sqphilo/uuid/guid/native/assigned/select/foreign
五、映射集合属性(@ElementCollection)
1、@ElementCollection的个属性：1、fetch：指定集合的抓取策略(FetchType.EAGER（立即抓取） Fetch.LAZY（延迟抓取）)
     2、targetClass、指定集合属性中集合元素的类型

2、@CollectionTable注解映射保存集合属性的表
属性：1、name：保存集合属性的数据库的表名
   2、catalog：指定保存集合属性的数据表放在指定的catalog中
   3、scema：指定保存集合属性的数据表放在指定的scema中
   4、indexes：为持久化类所映射的表设置索引，配合@Index注解数组
   5、JoinColumns：配合@JoinColum使用
   6、uniqueConstraints：为持久化类索引社的表设置唯一约束。配合@UniqueConstraint使用
3、@JoinColumn：进行外键映射
属性1、cloumnDefinition：指定Hibernate使用该属性指定的SQL片段来创建外键列
2、name：指定外键列的列名
3、insertable：指定该列是否包含在Hibernate生成的insert语句的列列表中，默认值：true
4、updatable：指定该列是否包含在Hibernate生成的update语句的列列表中，默认值：true
5、nullable
6、table：指定该列所在数据表的表名
7、unique：是否为该列增加唯一约束
8、referencedColumName：指定该列所参照的主键列的列名

4、@OrderColimn：用于定义List集合、数组的索引列
@MapKeyColumn：用于映射MAP集合的索引列

六、关联映射
1.@ManyToOne ：单向N-1关联
cascade：CascadeType.ALL(指定所有的持久化操作都级联到关联实体) CascadeType.MERGE:将MERGE操作级联到关联实体 CascadeType.PERSIST,CascadeType.REFRESH,CascadeType.REMOVE
fetch：指定抓取策略，FetchType.EAGER,立即抓取 FetchType.LAZY:抓取实体时，延迟抓取关联实体
optional：指定关联关系是否可选
targetEntity：指定关联实体的类名(默认反射判断实现，可以不指定)

2、@OneToOne单项1--1关联
cascade：CascadeType.ALL(指定所有的持久化操作都级联到关联实体) CascadeType.MERGE:将MERGE操作级联到关联实体 CascadeType.PERSIST,CascadeType.REFRESH,CascadeType.REMOVE
fetch：指定抓取策略，FetchType.EAGER,立即抓取 FetchType.LAZY:抓取实体时，延迟抓取关联实体
optional：指定关联关系是否可选
mappedBy：该属性合法的属性值为关联实体的属性名，该属性指定关联实体中那个属性可引用到当前实体
orphamRemoval：指定关联关系是否可选
targetEntity：指定关联实体的类名(默认反射判断实现，可以不指定)
3:、@OneToMany：单项1-N关联
cascade：CascadeType.ALL(指定所有的持久化操作都级联到关联实体) CascadeType.MERGE:将MERGE操作级联到关联实体 CascadeType.PERSIST,CascadeType.REFRESH,CascadeType.REMOVE
fetch：指定抓取策略，FetchType.EAGER,立即抓取 FetchType.LAZY:抓取实体时，延迟抓取关联实体
mappedBy：该属性合法的属性值为关联实体的属性名，该属性指定关联实体中那个属性可引用到当前实体
orphamRemoval：指定关联关系是否可选
targetEntity：指定关联实体的类名(默认反射判断实现，可以不指定)
二级缓存（一般使用第三方提供的开源缓存实现）
1.开启二级缓存
<property name = "hibernate.cache.user_second_level_cache">true<property>

 二级缓存有三种缓存实现--ConcurrentHashMap(内存,不推荐), EhCache(内存,磁盘,事务性，支持集群), Infinispan(事务性，支持集群)


2.使用二级缓存（这里使用的是EhCache方式）
第一步：在hibernate.cfg.xml文件中启动二级缓存--设置启用二级缓存，设置二级缓存的实现类
第二步：导入JAR包
第三步：添加二级缓存的配置文件xml（此处不具体介绍）
第四步：设置具体实现类，使用@Cache注释修饰持久化类(同时可以在Cache中设置属性(usage=CacheConcurrentcyStrategy.READ_ONLY/WIRTE))

如果想要开启二级缓存的统计功能，在hibernate.cfg.xml配置下面的配置
<property name = "hibernate.generate.statistics">true<property>
<property name = "hibernate.cache.use_structured_entries">true</property>

如果想开启查询缓存，在hibernate.cfg.xml配置下面的配置
<property name="hibernate.cache.use_query_cache">true</property>
---------------------
作者：二月的博客
来源：CSDN
原文：https://blog.csdn.net/qq193423571/article/details/76371372
版权声明：本文为博主原创文章，转载请附上博文链接！
