#### JDBC高级操作
***封装JDBC工具类,批处理，事务的使用，事务的隔离，不同隔离级别下的并发问题***
##### 使用.properties文件来配置信息
~~~ java
jdbcinfo.properties文件中的内容
{
  driver=com.mysql.jdbc.Driver
  url=jdbc:mysql://localhost:3306/javaee1805
  user=root
  password=123456
}

private static String driver = null;
private static String url = null;
private static String user = null;
private static String password = null;
static {//静态代码块 初始化数据
  Properties pro = new Properties();
  InputStream inStream = rollback.class.getClassLoader().getResourceAsStream("jdbcinfo.properties");


      try {

    pro.load(inStream);
   url = pro.getProperty("url");
   driver = pro.getProperty("driver");

   user = pro.getProperty("user");
   password = pro.getProperty("password");
   try {
    Class.forName(driver);
  } catch (ClassNotFoundException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }
  } catch (IOException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }
}


方式一【采用反射的方式获取】:最常用的方式。常用于读取同一文件夹下的文件，若不在同一文件夹下，需要往上层找
    InputStream inputStream = this.getClass().getResourceAsStream("../../../../config.properties");//相对路径
    InputStream inputStream = this.getClaa().getClassLoader().getResourceAsStream("华融湘江的ID.txt");//获取类加载路径，读取src下的文件
    Properties properties = new Properties();
    properties.load(inputStream);       
    System.out.println(properties.getProperty("password"));;

    方式二【采用ResourceBundle来获取本地资源】：只适用于资源文件在src下面的情况
    ResourceBundle resourceBundle = ResourceBundle.getBundle("config");
    System.out.println(resourceBundle.getString("DMUserName"));


    方式三:以文件流的形式读取
    Properties properties = new Properties();
    properties.load(new FileReader("config.properties"));  
    System.out.println(properties.getProperty("password"));
    System.out.println(URLDecoder.decode(this.getClass().getResource("").getPath(),"utf-8"));//获取当前类所在的项目路径

~~~
##### JDBC 事务
 ***逻辑上的一组操作，组成的这组操作的各个单元，要么全部成功，要不全部不成功***
~~~ java
Connection.setAutoCommit(false); //开启事务(start transaction)  关闭自动提交
Connection.rollback();//回滚事务(rollback)  如果出现异常也可以自动回滚
Connection.commit();//提交事务(commit)
~~~
~~~ java
package com.zhou.rollback;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Savepoint;
import java.util.Properties;

public class rollback {
	// 回滚成功的操作，使用properties文件配置地址信息
	private static String driver = null;
	private static String url = null;
	private static String user = null;
	private static String password = null;
	static {//静态代码块 初始化数据
		Properties pro = new Properties();
		InputStream inStream = rollback.class.getClassLoader().getResourceAsStream("jdbcinfo.properties");

		/*方式一【采用反射的方式获取】:最常用的方式。常用于读取同一文件夹下的文件，若不在同一文件夹下，需要往上层找
        InputStream inputStream = this.getClass().getResourceAsStream("../../../../config.properties");//相对路径
        InputStream inputStream = this.getClaa().getClassLoader().getResourceAsStream("华融湘江的ID.txt");//获取类加载路径，读取src下的文件
        Properties properties = new Properties();
        properties.load(inputStream);
        System.out.println(properties.getProperty("password"));;

        /*方式二【采用ResourceBundle来获取本地资源】：只适用于资源文件在src下面的情况
        ResourceBundle resourceBundle = ResourceBundle.getBundle("config");
        System.out.println(resourceBundle.getString("DMUserName"));


        /*方式三:以文件流的形式读取
        Properties properties = new Properties();
        properties.load(new FileReader("config.properties"));  
        System.out.println(properties.getProperty("password"));
        System.out.println(URLDecoder.decode(this.getClass().getResource("").getPath(),"utf-8"));//获取当前类所在的项目路径
		*/
        try {

			pro.load(inStream);
		 url = pro.getProperty("url");
		 driver = pro.getProperty("driver");

		 user = pro.getProperty("user");
		 password = pro.getProperty("password");
		 try {
			Class.forName(driver);
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}


	public static void main(String[] args) {
//		Savepoint sp = null;//设置回滚点
//		conn.rollback(sp);
//		System.out.println("回滚一波");
		PreparedStatement ps=null;
		Connection conn=null;
		try {

			 conn = DriverManager.getConnection(url, user, password);
			 System.out.println(url);
			conn.setAutoCommit(false);
			String sql = "select * from emp";
			 ps = conn.prepareStatement(sql);
			 ps.executeQuery();


			String sql1 = "select * from emp";
			 ps = conn.prepareStatement(sql1);
			 ps.executeQuery();
			conn.commit();
			System.out.println("查询成功");
		} catch (Exception e) {
			 //TODO Auto-generated catch block

			e.printStackTrace();
		}
	}

}
~~~
##### JDBC preparedStatement对象可以使用输入输出流来提供参数数据，将整个文件放入可以保存大值得数据库列
##### JDBC 批处理操作
批处理允许你将相关的SQL语句分组到批处理中，然后通过对数据库的一次调用提交他们
可以减少数据库连接，关闭的开销，从而提高性能
实现批处理的两种方式：statement 和 preparedstatement
1.statement进行操作数据库语句
优点：可以向数据库发送多条不同的ＳＱＬ语句。
缺点：SQL语句没有预编译。
当向数据库发送多条语句相同，但仅参数不同的SQL语句时，需重复写上很多条SQL语句。例如：
~~~ java
package com.zhou.batch;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class statementBatch {
	//使用statement对象进行批处理操作
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动
		conn=DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
		//建立连接
		stm = conn.createStatement();//创建执行sql语句的对象
		 conn.setAutoCommit(false);//取消自动提交
		 String sql="insert into emp values(211,'dad','dada',321,'2015-8-29',200,100,20)";
		stm.addBatch(sql);//添加到批处理
		String sql1="insert into emp values(212,'dad','dada',321,'2015-8-29',200,100,20)";
		 stm.addBatch(sql1);
		 String sql2="insert into emp values(213,'dad','dada',321,'2015-8-29',200,100,20)";
		 stm.addBatch(sql2);
		 int[] batch = stm.executeBatch();
		 conn.commit();//手动提交，使用连接对象进行提交
		 for (int i : batch) {
			System.out.print(i+"  ");//1  1  1   三个语句三个值
		}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

~~~
2.prepareStatement进行操作数据库语句
优点：发送的是预编译后的SQL语句，执行效率高。
缺点：只能应用在SQL语句相同，但参数不同的批处理中。因此此种形式的批处理经常用于在同一个表中批量插入数据，或批量更新表的数据。
~~~ java
package com.zhou.batch;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

public class preparedStatement {
	//使用preparedStatement 进行批处理操作
	public static void main(String[] args) {
		Connection conn=null;
		PreparedStatement ps=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");
			 conn = DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			String sql="insert into emp values(?,?,?,?,?,?,?,?)";
			 ps = conn.prepareStatement(sql);
			 conn.setAutoCommit(false);
			 ps.setObject(1, 2121);
			 ps.setObject(2, "dadad");
			 ps.setObject(3,"sdad");
			 ps.setObject(4, 231312);
			 ps.setObject(5, "1998-8-29");
			 ps.setObject(6, 3231);
			 ps.setObject(7, 313);
			 ps.setObject(8, 12);
			 ps.addBatch();//加入批处理
			 ps.setObject(1, 22);
			 ps.setObject(2, "dad22ad");
			 ps.setObject(3,"sda2d");
			 ps.setObject(4, 2311312);
			 ps.setObject(5, "1998-8-29");
			 ps.setObject(6, 32311);
			 ps.setObject(7, 3123);
			 ps.setObject(8, 12);
			 ps.addBatch();//加入批处理
			 int[] batch = ps.executeBatch();
			 conn.commit();//提交
			for (int i : batch) {
				System.out.println(i);
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

~~~

##### JDBC utils 工具类
~~~ java
package com.zhou.test;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

import com.zhou.JDBCutils.baseDao;
import com.zhou.bean.emper;

public class test {
	//查询制定的empno的数据数据表信息
	public static void main(String[] args) {
		List<emper> list = new ArrayList<emper>();
		Scanner scanner = new Scanner(System.in);
		System.out.println("请输入要查询的empno");
		String empno1 = scanner.next();
		Object[] parms= {Integer.parseInt(empno1)};
		String sql="select * from emp where empno=?";
		ResultSet rs = baseDao.query(sql, parms);
		try {
			while (rs.next()) {
				int empno = rs.getInt("empno");//获取其中的值
				String job = rs.getString("job");
				int mgr = rs.getInt("mgr");
				String hiredate = rs.getString("hiredate");
				int sal = rs.getInt("sal");
				int comm = rs.getInt("comm");
				int deptno=rs.getInt("deptno");
				String ename=rs.getString("ename");
				emper emper=new emper(empno, ename, job, mgr, hiredate, sal, comm, deptno);//封装成对象
				list.add(emper);//添加进集合
				for (emper emper2 : list) {
					System.out.println(emper2);
				}
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}

}
---------------------------------------------------------------
package com.zhou.JDBCutils;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class baseDao {
	//操作数据库的工具类
	static Connection conn=null;
	static PreparedStatement ps=null;
    static ResultSet rs=null;
	private static void openConn() {//获取数据库连接
		try {
			Class.forName("com.mysql.jdbc.Driver");
			 conn = DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}
	private static void closeConn() {//关闭连接
		try {
			if (rs!=null) {
				rs.close();
			}
			if (ps!=null) {
				ps.close();
			}
			if (conn!=null) {
				conn.close();
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	public static ResultSet query(String sql,Object[] parms) {
		try {//查询数据通过parms进行数据交互
			openConn();
			 ps = conn.prepareStatement(sql);
			 for (int i = 0; i < parms.length; i++) {
				ps.setObject(i+1, parms[i]);
			}
			  rs = ps.executeQuery();

		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		finally {
			//closeConn();在查询数据库的时候不要关闭数据库
		}
		return rs;

	}
	public static int update(String sql,Object[] parms) {
		try {//实现数据的修改，使用parms进行数据的交互
			openConn();
			 ps = conn.prepareStatement(sql);
			for (int i = 0; i < parms.length; i++) {
				ps.setObject(i+1, parms[i]);
			}
			int point = ps.executeUpdate();
			return point;
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return 0;
	}
}
----------------------------------------------------------------------
package com.zhou.bean;

public class emper {
	//实体类
	private int empno;
	private String ename;
	private String job;
	private int mgr;
	private String hiredate;
	private int sal;
	private int comm;
	private int deptno;
	public int getEmpno() {
		return empno;
	}
	public void setEmpno(int empno) {
		this.empno = empno;
	}
	public String getEname() {
		return ename;
	}
	public void setEname(String ename) {
		this.ename = ename;
	}
	public String getJob() {
		return job;
	}
	public void setJob(String job) {
		this.job = job;
	}
	public int getMgr() {
		return mgr;
	}
	public void setMgr(int mgr) {
		this.mgr = mgr;
	}
	public String getHiredate() {
		return hiredate;
	}
	public void setHiredate(String hiredate) {
		this.hiredate = hiredate;
	}
	public int getSal() {
		return sal;
	}
	public void setSal(int sal) {
		this.sal = sal;
	}
	public int getComm() {
		return comm;
	}
	public void setComm(int comm) {
		this.comm = comm;
	}
	public int getDeptno() {
		return deptno;
	}
	public void setDeptno(int deptno) {
		this.deptno = deptno;
	}

	public emper() {
		super();
	}
	public emper(int empno, String ename, String job, int mgr, String hiredate, int sal, int comm, int deptno) {
		super();
		this.empno = empno;
		this.ename = ename;
		this.job = job;
		this.mgr = mgr;
		this.hiredate = hiredate;
		this.sal = sal;
		this.comm = comm;
		this.deptno = deptno;
	}
	@Override
	public String toString() {
		return "emper [empno=" + empno + ", ename=" + ename + ", job=" + job + ", mgr=" + mgr + ", hiredate=" + hiredate
				+ ", sal=" + sal + ", comm=" + comm + ", deptno=" + deptno + "]";
	}

}



~~~
