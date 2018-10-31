#### Servlet
>进行java web 应用程序开发的技术

>web服务器收到客户端的http协议，会针对每次请求创建一个用于代表请求的request对象，和代表响应的response对象

##### Servlet创建方式
1.继承httpServlet类 2实现servlet接口 3.继承GenericServlet类
##### servlet周期
在servlet中要在开始就加载，在web.xml中Servlet中加
 <load-on-startup>1</load-on-startup> 1为优先级顺序，越大越高
Servlet对象一旦创建就不会被销毁，直到关服务器，或者重新加载应用，说明为单例的，但是在多线程下运行
分为四个阶段：
创建对象， 初始化（init 初始化数据） ，服务（处理请求，发送响应） ，销毁
初始化
~~~ xml
（在web.xml中配置init-param
    <init-param>
  		<param-name>user</param-name>
  		<param-value>zhangsan</param-value>
  	</init-param>
）
~~~
##### web.xml的约束注册

由于浏览器客户端是通过URL地址访问web服务器中的资源，所以Servlet程序若想被外界访问，必须把servlet程序映射到一个URL地址上，这个工作在web.xml文件中使用<servlet>元素和<servlet-mapping>元素完成。
<servlet>元素用于注册Servlet，它包含有两个主要的子元素：<servlet-name>和<servlet-class>，分别用于设置Servlet的注册名称和Servlet的完整类名。
<servlet-mapping>元素用于映射已注册的Servlet的对外访问路径，它包含有两个子元素：<servlet-name>和<url-pattern>

##### Servlet的配置方式
1.第一种是注解方式配置
@WebServlet(value = "/hello", loadOnStartup = 1)//需要在Servlet3.0以后
2.web.xml文件配置
~~~ xml
<!--Servlet的第二种配置 -->
<!--Servlet配置 -->
<servlet>
  <!--名称 -->
  <servlet-name>hello2</servlet-name>
  <!--Servlet的全称类名 -->
  <servlet-class>com.qf.web.servlet.HelloServlet</servlet-class>
  <!--启动的优先级，数字越小越先起作用 -->
  <load-on-startup>1</load-on-startup>
</servlet>
<!--映射配置 -->
<servlet-mapping>
  <!--名称 -->
  <servlet-name>hello2</servlet-name>
  <!--资源的匹配规则：精确匹配 -->
  <url-pattern>/hello2.do</url-pattern>
</servlet-mapping>
~~~
##### 请求资源路径
何为请求资源路径
在浏览器输入：http://127.0.0.1:8080/Web_Day12/HelloServlet
Web_Day12/HelloServlet就是请求资源路径

##### 浏览器中地址访问服务器资源地址的流向
>
例如，访问：http://localhost:8080/W09_Servlet3/hello
Tomcat服务器截取到访问的资源名称/hello,然后从web.xml的<url-pattern>中找匹配的数据，找到后，
再查找相关的servlet-name；然后从<servlet>节点中找匹配的servlet-name，找到后，就找到了对应的servlet-class，
最后创建对象，执行其中的方法
##### Servlet中的路径问题

Servlet中，"/"代表Web应用的跟目录。和物理路径的相对表示。例如："./" 代表当前目录,
"../"代表上级目录。这种类似的表示，也是属于相对路径
服务器端的相对地址指的是相对于你的web应用的地址，这个地址是在服务器端解析的


##### servlet的映射地址问题

Servlet映射多个地址，url web。xml文件中 <servlet-name>可以是同一个serlet注册名
~~~ xml
<servlet-mapping>
   <servlet-name>hhh</servlet-name>
   <url-pattern>/Hello</url-pattern>
   <!-- 要求匹配的资源后缀必须是.do,*表示通配符，任意内容都可以 ,不能写成/*.do-->
   <!-- <url-pattern>*.do</url-pattern> -->
   <!-- 匹配任意资源  不能直接写*-->
   <!-- <url-pattern>/*</url-pattern> -->
   <!-- 下面写法错误 -->
   <!-- <url-pattern>/a/*.do</url-pattern> -->
 </servlet-mapping>
 <!-- 一个<servlet>可以对应多个<servlet-mapping> -->
 <servlet-mapping>
   <servlet-name>hhh</servlet-name>
   <url-pattern>/hello</url-pattern>
 </servlet-mapping>
 ~~~

##### servletconfig
一个servlet对应一个ServletConfig对象
当servlet配置了初始化参数后，web容器在创建servlet实例对象时，会自动将这些初始化参数封装到ServletConfig对象中，
并在调用servlet的init方法时，将ServletConfig对象传递给servlet。进而，程序员通过ServletConfig对象就可以得到当前servlet的初始化参数信息。
##### servletContext
作用：
1.获取真实路径
2.获取容器的附加信息
request.getServletContext().getServletInfo();
3.全局的容器
~~~ java
每一个servletContext对应一个web应用
通过rquest获取应用路径
request.getContextPath()
获取servletContext对象
方式一:GenericServlet提供了getServletContext()方法。
方式二:ServletConfig提供了getServletContext()方法。
方式三:HttpSession提供了getServletContext()方法。
方式四:HttpServletRequest提供了getServletContext()方法。
ServletContext servletContext = this.getServletContext();
针对整个应用初始化数据
<context-param>
   <param-name>AAA</param-name>
   <param-value>aaa</param-value>
 </context-param>
 String v1 = servletContext.getInitParameter("AAA");
 //向servletcontext域中写入数据
  //在整个web应用的不同的资源之间共享数据
  （四大作用域 ：servletContext cookie session request 作用域就是作用的范围不同 ，基本是用来传值的额）
 ServletContext servletContext = this.getServletContext();

		servletContext.setAttribute("name", "zhangsan");

		Dog d = new Dog();
		d.setAge(2);
		d.setName("wangcai");

		servletContext.setAttribute("dog", d);
~~~
##### 转发和重定向
~~~ java
转发 （网页中地址不会变）
转发时，服务内部做的转发，重定向相当于服务器告诉浏览器重新发送请求；
通过重定向可以跳转到其他web应用的资源；
重定向时，路径中的/ 相对于服务器；转发时，/ 相对web应用
request.getRequestDispatcher("/ForwardServlet2").forward(request, response);
重定向
	response.sendRedirect(request.getContextPath() + "/RedirectServlet2");
  重定向和转发区别：
1)重定向的访问过程结束后，浏览器地址栏中显示的URL会发生改变；转发过程结束后，浏览器地址栏保持初始的URL地址不变。
2)重定向时，通过响应的结果告诉浏览器去重新发出请求访问另外一个资源；而转发是在服务器端内部将请求转发给另外一个资源。
3)转发时，调用者与被调用者之间共享相同的request对象和response对象，它们属于同一个访问请求和响应过程；而重定向时调用者与被调用者使用各自的request对象和response对象，它们属于两个独立的访问请求和响应过程。
4)重定向时，重定向的URL以“/”开头，它是相对于整个WEB站点的根目录；转发时的URL以“/”开头，它是相对于当前WEB应用程序的根目录。
5)转发只能将请求转发给同一个WEB应用中的资源；而重定向还可以重定向到同一个站点上的其他应用程序中的资源，甚至是使用绝对URL重定向到其他站点的资源。
~~~



***HttpServletRquest***
*代表客户端的请求 http协议访问服务器 http请求头中的所有信息封装在这个对象中，可以获取请求相关信息*
***HttpServletResponse***
*HttpServletResponse对象服务器的响应。该对象中封装了向客户端发送数据、发送响应头，发送响应状态码的方法*

##### 刷新 (进行页面的刷新时让倒计时的秒数跟着一起变)
~~~ java
package com.zhou.servlet1;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


public class test5 extends HttpServlet {
	//定时刷新网页
	private static final long serialVersionUID = 1L;


    public test5() {
        super();
        // TODO Auto-generated constructor stub
    }
    int count=3;
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			response.setContentType("text/html;charset=utf-8");
		response.getWriter().write("在"+count+"秒后进行跳转到百度");


		response.setHeader("refresh", "3;url=index.html");//3秒后刷新，然后定向到index.html网页

	}


	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}

~~~

##### 获取请求参数
>特别注意在使用get.post进行获取requset请求参数字符串的时候，必须form表单必须填相应的get.post方法
后面的在doget()中写dopost()是没用的,通过getparameter进行获取请求参数不存在

0.通过getparameter进行获取请求参数，get,post都能用
~~~ java
package com.zhou.servlet1;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


public class test4 extends HttpServlet {
	//通过getparameter获取request请求参数
	private static final long serialVersionUID = 1L;


    public test4() {
        super();
        // TODO Auto-generated constructor stub
    }


	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("utf-8");//设置编码格式 编码格式必须放在最前面
		response.setContentType("text/html;charset=utf-8");//设置响应的编码格式
		//这里的respose为什么用setContent,因为content是能作用在整个application下的
		String username = request.getParameter("username");//通过标签中的name属性进行获取reqest请求中的值
		String password = request.getParameter("password");

		System.out.println(username+password);
		response.getWriter().write(username+password);
		//输出结果 八戒admin1234
		//网页中输出的值 ??admin1234 所以还需要进行设置网页的编码
		//response.setContentType("text/html;charset=utf-8");设置后的结果
		//网页面输出的值 八戒admin1234
	}


	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}


~~~
1.通过get方法获取请求参数
~~~ java
package com.zhou.servlet1;

import java.io.IOException;
import java.net.URLEncoder;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


public class test2 extends HttpServlet {
	//通过get方法进行接收请求参数获取参数字符串
	private static final long serialVersionUID = 1L;


    public test2() {
        super();
        // TODO Auto-generated constructor stub
    }


	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

		String queryString = request.getQueryString();//获取请求的值
	  queryString=URLDecoder.decode(queryString, "utf-8");//设置中文编码问题
		System.out.println(queryString);
		response.getWriter().write(queryString);//
		//获取的值
		//username%3D%25E5%2585%25AB%25E6%2588%2592%26password%3Dadmin1234
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);//让post请求通过get请求进行处理
	}

}
~~~
2.通过post请求进行获取参数字符串
~~~ java
package com.zhou.servlet1;

import java.io.IOException;
import java.net.URLDecoder;


import javax.servlet.ServletException;
import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


public class test3 extends HttpServlet {
  //使用post方法进行获取请求参数
	private static final long serialVersionUID = 1L;


    public test3() {
        super();
        // TODO Auto-generated constructor stub
    }


	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			ServletInputStream inputStream = request.getInputStream();//获取request对象的输入流,包含post请求参数
		byte[] b=	new byte[1024];
			int len = inputStream.read(b);	//读取输入流中的数据
			String string = new String(b,0,len);
			string=URLDecoder.decode(string, "utf-8");//设置中文乱码
			System.out.println(string);
			//输出内容
			//username=八戒&password=admin1234
	}

}


~~~
##### beanUtils自动封装参数
##### HttpServletResponse生成验证码
~~~ java
package com.bruceliu.controller;

import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.util.Random;

import javax.imageio.ImageIO;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.bruceliu.utils.BaseDao;

@WebServlet("/CodeServlet.do")
public class CodeServlet extends HttpServlet {

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//使用JavaAPI绘制一张随机验证码图片，响应给页面
		int width=70; //宽度
		int height=28; //高度

		//获取一个画布  RED  GREEN BLUE 三原色
		BufferedImage b=new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
		//画笔
		Graphics g = b.getGraphics();
		g.setColor(Color.WHITE);
		g.fillRect(0, 0, width, height);
		//画笔为黑色为黑色
		g.setColor(Color.BLACK);
		g.drawRect(0, 0, width-1, height-1);//把图片的边框画为黑色

		//生成随机验证码  大小字母和数字 62
		char[] array = { 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', '0', '1', '2', '3', '4', '5', '6', '7', '8',
				'9', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z' }; // 62

		String str = "\u7684\u4e00\u4e86\u662f\u6211\u4e0d\u5728\u4eba\u4eec\u6709\u6765\u4ed6\u8fd9\u4e0a\u7740\u4e2a\u5730\u5230\u5927\u91cc\u8bf4\u5c31\u53bb\u5b50\u5f97\u4e5f\u548c\u90a3\u8981\u4e0b\u770b\u5929\u65f6\u8fc7\u51fa\u5c0f\u4e48\u8d77\u4f60\u90fd\u628a\u597d\u8fd8\u591a\u6ca1\u4e3a\u53c8\u53ef\u5bb6\u5b66";
		char[] arrays = str.toCharArray();


		StringBuffer sb=new StringBuffer();

		//随机取四个
		Random r=new Random();
		for (int i =1; i <=4; i++) {
			int index=r.nextInt(arrays.length); //0-61下标
			String c = arrays[index]+""; //一个随机数
			sb.append(c);
			Font f=new Font("微软雅黑", Font.BOLD, 16);
			g.setFont(f);
			// 0-255
			int red = r.nextInt(256);
			int green = r.nextInt(256);
			int blue = r.nextInt(256);
			Color color=new Color(red, green, blue); //随机色
			g.setColor(color);
			g.drawString(c, (width/5)*i, 18);
		}

		System.out.println("系统生成的正确验证码是:"+sb.toString());

		//绘制干扰线
		for (int i =1; i <=8; i++) {
			int red = r.nextInt(256);
			int green = r.nextInt(256);
			int blue = r.nextInt(256);
			Color color=new Color(red, green, blue); //随机色
			g.setColor(color);

			int x1=r.nextInt(width);
			int y1=r.nextInt(height);

			int x2=r.nextInt(width);
			int y2=r.nextInt(height);

			g.drawLine(x1, y1, x2, y2);
		}


		response.setContentType("image/jpeg");
		ServletOutputStream out = response.getOutputStream();
	    ImageIO.write(b, "jpeg", out);

	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request, response);
	}

}

~~~
##### 注解试实现文件上传
