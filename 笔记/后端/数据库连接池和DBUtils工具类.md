# 数据库连接池
> 数据库连接池的原理：创建一些数据库连接的接口，不用每次去连接，然后关闭，耗时费力

> 开源的数据连接池有***DBCP,c3p0,Druid***
#### DBCP 连接池的使用
创建JavaWeb项目

导入相应jar包

	mysql-jdbc.jar	commons-dbcp.jar    commons-pool.jar

##### 硬编码使用DBCP
~~~ java
@Test
public void test1() throws Exception {
  BasicDataSource source = new BasicDataSource();
  // 设置连接的信息
  source.setDriverClassName("com.mysql.jdbc.Driver");
  source.setUrl("jdbc:mysql://localhost:3306/jdbc-pool");
  source.setUsername("root");
  source.setPassword("123");
  Connection connection = source.getConnection();
  String sql = "select * from student";
  Statement createStatement = connection.createStatement();
  ResultSet executeQuery = createStatement.executeQuery(sql);
  while (executeQuery.next()) {

    System.out.println(executeQuery.getString(2));
  }
  connection.close(); // 回收
}
~~~
##### 软编码使用DBCP
*** ***
***需要先配置连接信息***
>连接设置

项目中添加配置

       文件名称: info.properties
       文件位置: src下
driverClassName=com.mysql.jdbc.Driver

url=jdbc:mysql://localhost:3306/day2
username=root

password=111
#初始化连接
initialSize=10
#最大连接数量
maxActive=50
#最大空闲连接
maxIdle=20
#最小空闲连接
minIdle=5
#超时等待时间以毫秒为单位 6000毫秒/1000等于60秒
maxWait=6000
~~~ java
@Test
	public void testSoft() throws Exception {
		BasicDataSourceFactory factory = new BasicDataSourceFactory();
		Properties properties = new Properties();
		// 配置文件添加到properties对象中 javase
		properties.load(new FileInputStream("src/info.properties"));
		// 生成连接池子 需要配置文件
		DataSource source = factory.createDataSource(properties);
		Connection connection = source.getConnection();
		String sql = "select * from student";
		Statement createStatement = connection.createStatement();
		ResultSet executeQuery = createStatement.executeQuery(sql);
		while (executeQuery.next()) {
			System.out.println(executeQuery.getString(2));
		}
		connection.close(); // 回收
	}

~~~
### C3P0
目前使用它的开源项目有hibernate，spring等

c3p0与dbcp区别

	 1.
		dbcp没有自动回收空闲连接的功能
		c3p0有自动回收空闲连接功能
	 2.
	 	dbcp需要手动设置配置文件（自己写Java代码度配置文件）
	   c3p0不需要手动设置（自动读取配置文件）

#### c3p0的使用
创建项目

导入jar包

    c3p0-0.9.1.2.jar mysql-connector-java-5.0.8.jar
 ##### 添加配置文件
文件位置:

src

文件命名:c3p0-config.xml
~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
	<!-- 默认配置，如果没有指定则使用这个配置 -->
	<default-config>
		<!-- 基本配置 -->
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbc-pool?characterEncoding=utf-8</property>
		<property name="user">root</property>
		<property name="password">123</property>
		<!--扩展配置 -->
		<!-- 连接超过30秒报错 -->
		<property name="checkoutTimeout">30000</property>
		<!--30秒检查空闲连接 -->
		<property name="idleConnectionTestPeriod">30</property>
		<property name="initialPoolSize">10</property>
		<!-- 30秒不适用丢弃 -->
		<property name="maxIdleTime">30</property>
		<property name="maxPoolSize">100</property>
		<property name="minPoolSize">10</property>
	</default-config>

	<!-- 命名的配置 -->
	<named-config name="bruce">
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<property name="jdbcUrl">jdbc:mysql://localhost:3306/day2</property>
		<property name="user">root</property>
		<property name="password">111</property>
		<!-- 如果池中数据连接不够时一次增长多少个 -->
		<property name="acquireIncrement">5</property>
		<property name="initialPoolSize">20</property>
		<property name="minPoolSize">10</property>
		<property name="maxPoolSize">40</property>
	</named-config>
</c3p0-config>
~~~
 ###### c3p0工具类
~~~ java
/**
 * 从连接池子中获取连接！ C3P0的连接池子 0.获取连接池子对象 DBUtils 1.获取连接 2.关闭资源
 */
public class DataSourceUtils {

	private static ComboPooledDataSource dataSource = new ComboPooledDataSource();

	/**
	 * 返回连接池对象方法
	 * @return c3p0连接池子
	 */
	public static ComboPooledDataSource getDataSource() {
		return dataSource;
	}

	/**
	 * 连接池中获取连接的方法
	 * @return 连接
	 */
	public static Connection getConnection() {

		Connection conn = null;
		try {
			conn = dataSource.getConnection();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return conn;
	}

	// 关闭资源
	public static void close(Connection conn) {
		if (conn != null) {
			try {
				conn.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

	public static void close(Statement st) {

		if (st != null) {
			try {
				st.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

	public static void close(ResultSet set) {
		if (set != null) {
			try {
				set.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

	public static void close(Connection conn, Statement st) {
		close(conn);
		close(st);
	}

	public static void close(Connection conn, Statement st, ResultSet rt) {
		close(conn);
		close(st);
		close(rt);
	}
}
~~~
##### c3p0对数据库进行操作
~~~ java
@Test
public void test1() throws Exception {
	// 1.创建C3P0连接池子
	Connection connection = DataSourceUtils.getConnection();
	Statement createStatement = connection.createStatement();
	String sql = "select * from student;";
	ResultSet resultSet = createStatement.executeQuery(sql);
	while (resultSet.next()) {
		System.out.println(resultSet.getString(1)+"--"+resultSet.getString(2));
	}
	DataSourceUtils.close(connection, createStatement, resultSet);
}
~~~
### Druid 数据连接池

Druid 是目前比较流行的高性能的，分布式列存储的OLAP框架(具体来说是MOLAP)。它有如下几个特点：

一. 亚秒级查询

二.实时数据注入
     druid支持流数据的注入，并提供了数据的事件驱动，保证在实时和离线环境下事件的实效性和统一性

三.可扩展的PB级存储
     druid集群可以很方便的扩容到PB的数据量，每秒百万级别的数据注入。即便在加大数据规模的情况下，也能保证时其效性

四.多环境部署
     druid既可以运行在商业的硬件上，也可以运行在云上。它可以从多种数据系统中注入数据，包括hadoop，spark，kafka，storm和samza等

五.丰富的社区
     druid拥有丰富的社区，供大家学习
#### 3.2.1 druid连接池的使用
导入jar包

数据库驱动包！

##### 编写工具类
~~~ java
public class DruidUtils {

	// 声明连接池对象
	private static DruidDataSource ds;
	static {
		// /实例化数据库连接池对象
		ds = new DruidDataSource();
		// 实例化配置对象
		Properties properties = new Properties();
		try {
			// 加载配置文件内容
			InputStream fis = new FileInputStream("src/db.properties");
			properties.load(fis);
			// 设置驱动类全称
			ds.setDriverClassName(properties.getProperty("driverClassName"));
			// 设置连接的数据库
			ds.setUrl(properties.getProperty("url"));
			// 设置用户名
			ds.setUsername(properties.getProperty("username"));
			// 设置密码
			ds.setPassword(properties.getProperty("password"));
			// 设置最大连接数量
			ds.setMaxActive(Integer.parseInt(properties.getProperty("maxActive")));
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	// 获取连接对象
	public static Connection getConnection() {
		try {
			return ds.getConnection();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return null;
	}
}
~~~
##### 测试类
~~~ java
public class Druid_Test {

	@Test
	public void test1() {
		List<Student> list = findAll();
		for (Student student : list) {
			System.out.println(student);
		}
		System.out.println("------------");

		Student s = find("黎明", "12345");
		System.out.println(s);
	}

	public List<Student> findAll() {
		Connection conn = null;
		PreparedStatement pstat = null;
		ResultSet rs = null;
		List<Student> students = new ArrayList<Student>();
		try {
			conn = DruidUtils.getConnection();
			pstat = conn.prepareStatement("select * from student");
			rs = pstat.executeQuery();
			while (rs.next()) {
				int id = rs.getInt("id");
				String username = rs.getString("username");
				String password = rs.getString("password");
				students.add(new Student(id,username,password));
			}
			return students;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	public Student find(String username, String password) {
		Connection conn = null;
		PreparedStatement pstat = null;
		ResultSet rs = null;
		Student student = null;
		try {
			conn = DruidUtils.getConnection();
			pstat = conn.prepareStatement("select * from student where username=? and password=?");
			pstat.setString(1, username);
			pstat.setString(2, password);
			rs = pstat.executeQuery();
			if (rs.next()) {
				int id = rs.getInt("id");
				student=new Student(id,username,password);
			}
			return student;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
}
~~~
###### DBUtils
     Commons DbUtils是Apache组织提供的一个对JDBC进行简单封装的开源工具类库
		 使用它能够简化JDBC应用程序的开发，同时也不会影响程序的性能。DbUtils是Apache组织封装的工具类！
 ***QueryRunner类的主要方法***

		public Object query(Connection conn, String sql, Object[] params, ResultSetHandler rsh) throws SQLException：执行一个查询操作，在这个查询中，对象数组中的每个元素值被用来作为查询语句的置换参数。该方法会自行处理 PreparedStatement 和 ResultSet 的创建和关闭。

		public Object query(String sql, Object[] params, ResultSetHandler rsh) throws SQLException:　几乎与第一种方法一样；唯一的不同在于它不将数据库连接提供给方法，并且它是从提供给构造方法的数据源(DataSource) 或使用的setDataSource 方法中重新获得 Connection。

		public Object query(Connection conn, String sql, ResultSetHandler rsh) throws SQLException : 执行一个不需要置换参数的查询操作。

		public int update(Connection conn, String sql, Object[] params) throws SQLException:用来执行一个更新（插入、更新或删除）操作。

		public int update(Connection conn, String sql) throws SQLException：用来执行一个不需要置换参数的更新操作

测试表
~~~ sql
 /*测试表*/
DROP TABLE IF EXISTS `student`;

CREATE TABLE `student` (

  `id` int(11) NOT NULL DEFAULT '0',

  `username` varchar(20) DEFAULT NULL,

  `password` varchar(20) DEFAULT NULL,

  `age` int(3) DEFAULT NULL,

  `address` varchar(40) DEFAULT NULL,

  PRIMARY KEY (`id`)

) ENGINE=InnoDB DEFAULT CHARSET=utf8;


-- ----------------------------
-- Records of student
-- ----------------------------
INSERT INTO `student` VALUES ('1', '黎明', '12345', null, null);
INSERT INTO `student` VALUES ('2', '张学友', '12345', null, null);
~~~
测试
~~~ java
public class QueryRunnerCRUD {

	@Test
	public void add() throws SQLException {
		// 将数据源传递给QueryRunner，QueryRunner内部通过数据源获取数据库连接
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		System.out.println(qr);
		String sql = "insert into student(id,username,password,age,address) values(null,?,?,?,?)";
		Object params[] = { "小张", "123", 18, "成都" };
		int count = qr.update(sql, params);
		System.out.println(count > 0 ? "成功" : "失败");
	}

	@Test
	public void delete() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "delete from student where id=?";
		int count = qr.update(sql, 3);
		System.out.println(count > 0 ? "成功" : "失败");
	}

	@Test
	public void update() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "update student set username=? where id=?";
		Object params[] = { "哈哈", 2 };
		int count = qr.update(sql, params);
		System.out.println(count > 0 ? "成功" : "失败");
	}

	@Test
	public void find() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student where id=?";
		Object params[] = { 4 };
		Student s = (Student) qr.query(sql, params, new BeanHandler(Student.class));
		System.out.println(s);
	}

	@Test
	public void getAll() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		List<Student> list = (List) qr.query(sql, new BeanListHandler(Student.class));
		for (Student s : list) {
			System.out.println(s);
		}
		System.out.println(list.size());
	}
}
~~~
ResultSetHandler接口使用讲解

该接口用于处理java.sql.ResultSet，将数据按要求转换为另一种形式。

ResultSetHandler接口提供了一个单独的方法：Object handle (java.sql.ResultSet .rs)
5.3.1 ResultSetHandler接口的实现类

ArrayHandler：把结果集中的第一行数据转成对象数组。

ArrayListHandler：把结果集中的每一行数据都转成一个数组，再存放到List中。

BeanHandler：将结果集中的第一行数据封装到一个对应的JavaBean实例中。

BeanListHandler：将结果集中的每一行数据都封装到一个对应的JavaBean实例中，存放到List里。

ColumnListHandler：将结果集中某一列的数据存放到List中。

KeyedHandler(name)：将结果集中的每一行数据都封装到一个Map里，再把这些map再存到一个map里，其key为指定的key。

MapListHandler：将结果集中的每一行数据都封装到一个Map里，然后再存放到List
~~~ java
public class ResultSetHandlerDemo {

	/*
	 * 把结果集中的第一行数据转成对象数组
	 */
	@Test
	public void testArrayHandler() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		Object result[] = (Object[]) qr.query(sql, new ArrayHandler());
		System.out.println(Arrays.asList(result)); // list toString()
	}

	/*
	 * 把结果集中的每一行数据都转成一个数组，再存放到List中。
	 */
	@Test
	public void testArrayListHandler() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		List<Object[]> list = (List) qr.query(sql, new ArrayListHandler());
		for (Object[] o : list) {
			System.out.println(Arrays.asList(o));
		}
	}

	/*
	 * 将结果集中某一列的数据存放到List中。
	 */
	@Test
	public void testColumnListHandler() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		List list = (List) qr.query(sql, new ColumnListHandler("username"));
		System.out.println(list);
	}

	/*
	 * 将结果集中的每一行数据都封装到一个Map里，再把这些map再存到一个map里，其key为指定的key。
	 */
	@Test
	public void testKeyedHandler() throws Exception {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		Map<Integer, Map> map = (Map) qr.query(sql, new KeyedHandler("id"));
		for (Map.Entry<Integer, Map> me : map.entrySet()) {
			int id = me.getKey();
			Map<String, Object> innermap = me.getValue();
			for (Map.Entry<String, Object> innerme : innermap.entrySet()) {
				String columnName = innerme.getKey();
				Object value = innerme.getValue();
				System.out.println(columnName + "=" + value);
			}
			System.out.println("----------------");
		}
	}

	/*
	 * 将结果集中的第一行数据封装到一个Map里，key是列名，value就是对应的值。
	 */
	@Test
	public void testMapHandler() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		Map<String, Object> map = (Map) qr.query(sql, new MapHandler());
		for (Map.Entry<String, Object> me : map.entrySet()) {
			System.out.println(me.getKey() + "=" + me.getValue());
		}
	}

	/*
	 * 将结果集中的每一行数据都封装到一个Map里，然后再存放到List
	 */
	@Test
	public void testMapListHandler() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select * from student";
		List<Map> list = (List) qr.query(sql, new MapListHandler());
		for (Map<String, Object> map : list) {
			for (Map.Entry<String, Object> me : map.entrySet()) {
				System.out.println(me.getKey() + "=" + me.getValue());
			}
		}
	}

	@Test
	public void testScalarHandler() throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql = "select count(*) from student";
		int count = ((Long) qr.query(sql, new ScalarHandler(1))).intValue();
		System.out.println(count);
	}
}
~~~
