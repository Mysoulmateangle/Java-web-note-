### JDBC
>java database Connectivity(java数据库连接技术)

##### 工作原理
JDBC API 定义一系列的接口和类，集成在java.sql和javax.sql包中
DriverManager  管理不同的JDBC驱动
JDBC驱动 负责链接不同类型的数据库，java链接不同的数据库，需要需要导入对应的数据库驱动包
mysql数据库的驱动包是 mysql-connector-java-...jar
##### JDBC查询数据库
需要三个对象
Connection 负责连接库

Statement 负责执行SQL

ResultSet  查询的结果集

##### 一个性能更好的statement:PreparedStatement
优点：提前预编译，性能高
      PreparedStatement不需要拼接SQL，使用？号占位符，防止注入问题
##  两种statement实现增删改查
  ***statement***
  1.查询数据库内容
  ~~~ java
  package com.zhou.statement;


import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

import com.zhou.bean.emper;

public class test1 {
	//使用数据库查询表中数据
	public static void main(String[] args) {
		Connection conn=null;
		ResultSet rs=null;
		Statement stm=null;
		List<emper> list = new ArrayList<emper>();
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动，能够使用DriverManager
			 conn = DriverManager.getConnection
		("jdbc:mysql://127.0.1:3306/javaee1805?useUnicode=true&characterEncoding=utf-8", "root", "123456");//获取连接地址设置编码格式，用户密码
			//useUNcide是用来统一编码是否
			  stm = conn.createStatement();//获取执行sql语句的对象
			 String sql = "select * from emp";
			rs = stm.executeQuery(sql);//获取结果集
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
			}
				for (emper emper : list) {//遍历集合
					System.out.println(emper);
				}

		} catch (Exception e) {

			e.printStackTrace();
		}
		finally {
			try {//关闭连接
				if(rs!=null){
					rs.close();
				}
				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
-----------------------------------------------------------------
bean实体类
package com.zhou.bean;

public class emper {
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
  2.修改数据
  ~~~ java
  package com.zhou.statement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class test4 {
	//使用JDBC进行数据修改
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");
			 conn = DriverManager.getConnection("jdbc:mysql://127.0.1/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			  stm = conn.createStatement();
			  String sql = "update emp set sal=80000 where empno=7369";
			int point = stm.executeUpdate(sql);
			if (point==1) {
				System.out.println("修改成功");
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		finally {
			try {//关闭连接

				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

	}

}

  ~~~
  3.删除数据
  ~~~ java
  package com.zhou.statement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class test3 {
	//用JDBC进行数据的删除
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动
			conn=DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			//建立连接
			 stm = conn.createStatement();//获取操作数据库对象
			 String sql="delete from emp where empno=211";
			int point = stm.executeUpdate(sql);
			if (point==1) {
				System.out.println("删除成功");
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		finally {
			try {//关闭连接

				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}

  ~~~
4.新增数据
~~~ java
package com.zhou.statement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class test2 {
	//使用JDBC进行数据的新增
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动
			conn=DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			 stm = conn.createStatement();
			 String sql = "insert into emp values(211,'dad','dada',321,'2015-8-29',200,100,20)";
			int point = stm.executeUpdate(sql);
			if (point==1) {
				System.out.println("添加成功");
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

~~~
#### JDBC PreparedStatement 进行操作库
~~~ java
1.PreparedStatement 查询数据
package com.zhou.statement;


import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

import com.zhou.bean.emper;

public class test1 {
	//使用数据库查询表中数据
	public static void main(String[] args) {
		Connection conn=null;
		ResultSet rs=null;
		Statement stm=null;
		List<emper> list = new ArrayList<emper>();
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动，能够使用DriverManager
			 conn = DriverManager.getConnection
		("jdbc:mysql://127.0.1:3306/javaee1805?useUnicode=true&characterEncoding=utf-8", "root", "123456");//获取连接地址设置编码格式，用户密码
			//useUNcide是用来统一编码是否
			  stm = conn.createStatement();//获取执行sql语句的对象
			 String sql = "select * from emp";
			rs = stm.executeQuery(sql);//获取结果集
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
			}
				for (emper emper : list) {//遍历集合
					System.out.println(emper);
				}

		} catch (Exception e) {

			e.printStackTrace();
		}
		finally {
			try {//关闭连接
				if(rs!=null){
					rs.close();
				}
				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
-----------------------------------------------------------------
2.PreparedStatement 新增
package com.zhou.statement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class test2 {
	//使用JDBC进行数据的新增
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动
			conn=DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			 stm = conn.createStatement();
			 String sql = "insert into emp values(211,'dad','dada',321,'2015-8-29',200,100,20)";
			int point = stm.executeUpdate(sql);
			if (point==1) {
				System.out.println("添加成功");
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		finally {
		try {//关闭连接

				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
--------------------------------------------------------------
PreparedStatement 修改
package com.zhou.statement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class test4 {
	//使用JDBC进行数据修改
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");
			 conn = DriverManager.getConnection("jdbc:mysql://127.0.1/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			  stm = conn.createStatement();
			  String sql = "update emp set sal=80000 where empno=7369";
			int point = stm.executeUpdate(sql);
			if (point==1) {
				System.out.println("修改成功");
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		finally {
			try {//关闭连接

				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}

		}

	}

}
----------------------------------------------------------------------
PreparedStatement 删除
package com.zhou.statement;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class test3 {
	//用JDBC进行数据的删除
	public static void main(String[] args) {
		Connection conn=null;
		Statement stm=null;
		try {
			Class.forName("com.mysql.jdbc.Driver");//获取驱动
			conn=DriverManager.getConnection("jdbc:mysql://127.0.1:3306/javaee1805?useUncide=true&characterEncoding=utf-8", "root", "123456");
			//建立连接
			 stm = conn.createStatement();//获取操作数据库对象
			 String sql="delete from emp where empno=211";
			int point = stm.executeUpdate(sql);
			if (point==1) {
				System.out.println("删除成功");
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		finally {
			try {//关闭连接

				if(stm!=null){
					stm.close();
				}
				if(conn!=null){
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
~~~
