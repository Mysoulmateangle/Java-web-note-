### 会话技术
Cookie：将数据保存到浏览器客户端的技术
>服务端创建Cookie对象，然后发送cookie信息到浏览器
通过浏览器在访问web应用的时候，会带上cookie信息

~~~ java
Cookie的方法
Cookie(String name, String value) 创建cookie对象的构造方法，value是字符串类型，不能使用中文
Cookie cookie = new Cookie("name", "zhangsan");
通过响应将cookie数据发送到浏览器端
response.addCookie(cookie);
获取请求中的cookie数据
				//ctrl + 1，Enter
				//ctrl + 2, l
				Cookie[] cookies = request.getCookies();
				if(cookies != null){
					for (Cookie cookie2 : cookies) {
						// 获取cookie的name和对应的value
						System.out.println(cookie2.getName());
						System.out.println(cookie2.getValue());
					}
				}

public String getName() 取得Cookie的名字
public String getValue() 取得Cookie的值
public void setValue(String newValue)设置Cookie的值
public void setMaxAge(int expiry)设置Cookie的最大保存时间，单位秒，即cookie的有效期，默认会话结束，cookie失效
public int getMaxAge()获取Cookies的有效期
public void setPath(String uri) 设置cookie的有效路径，比如把cookie的有效路径设置为"/aaa"，那么浏览器访问"aaa"的web资源时，就会带上cookie
public String getPath() 获取cookie的有效路径
~~~
Session:将数据保存到服务端的技术
>访问应用的时候回创建一个session对象，给每一个session分配一个id,然后发送给浏览器，当下次浏览器访问的时候会通过id头，让服务器识别session对象

~~~ java

//会根据session的id 查找服务端是否有对应的session对象，如果有，返回session对象;如果没有创建session对象
		//创建session对象后，会将JSESSIONID以cookie的形式存在浏览器端
		HttpSession session = request.getSession();
		//session的id，唯一的   JSESSIONID
		String id = session.getId();
		System.out.println(id);

		//默认情况下，JSESSIONID的过期时间是会话结束时，为了能正常访问session对象，需要手动将JSESSIONID存储到cookie中，并设置一个合适的过期时间
		Cookie cookie = new Cookie("JSESSIONID", id);
		cookie.setMaxAge(30 * 60);
		response.addCookie(cookie);

		//设置session的过期时间，单位秒，默认30分钟
		session.setMaxInactiveInterval(10 * 60);

		//向session域中存入数据
		session.setAttribute("name", "张三");
-----------------------		
//根据JSESSIONID找session对象，如果存在，返回；如果不存在，返回null
		HttpSession session = request.getSession(false);
		if(session != null){
			//获取域中的数据
			System.out.println(session.getAttribute("name"));

			//获取域中所有的属性值
			//session.getAttributeNames();
		}

~~~
#### 禁用cookie后
session依赖cookie，因为服务器需要根据sessionId，然后找到客户端的session对象，如果浏览器禁用了cookie，就需要对URL进行重写，这样服务器收到的请求中就会带有sessionId。
使用方法：response.encodeURL(String url)
它会判断客户端浏览器是否禁用了Cookie，如果禁用了，那么这个方法会在URL后面追加jsessionid，否则不会追加。
