##### String，StringBuffer 和 Stringbuilder 用法
这三个类之间的区别主要是在两个方面，即运行速度和线程安全这两方面。

首先说运行速度，或者说是执行速度，在这方面运行速度快慢为：StringBuilder > StringBuffer > String
　　String最慢的原因：

***String为字符串常量，而StringBuilder和StringBuffer均为字符串变量，即String对象一旦创建之后该对象是不可更改的，
但后两者的对象是变量，是可以更改的：***


~~~ java
1 String str="abc";
2 System.out.println(str);
3 str=str+"de";
4 System.out.println(str);
~~~

　　如果运行这段代码会发现先输出“abc”，然后又输出“abcde”，好像是str这个对象被更改了，其实，这只是一种假象罢了，JVM对于这几行代码是这样处理的，首先创建一个String对象str，并把“abc”赋值给str，然后在第三行中，其实JVM又创建了一个新的对象也名为str，然后再把原来的str的值和“de”加起来再赋值给新的str，而原来的str就会被JVM的垃圾回收机制（GC）给回收掉了，所以，str实际上并没有被更改，也就是前面说的String对象一旦创建之后就不可更改了。所以，Java中对String对象进行的操作实际上是一个不断创建新的对象并且将旧的对象回收的一个过程，所以执行速度很慢。

　　而StringBuilder和StringBuffer的对象是变量，对变量进行操作就是直接对该对象进行更改，而不进行创建和回收的操作，所以速度要比String快很多。

　　另外，有时候我们会这样对字符串进行赋值
~~~ java
1 String str="abc"+"de";
2 StringBuilder stringBuilder=new StringBuilder().append("abc").append("de");
3 System.out.println(str);
4 System.out.println(stringBuilder.toString());
//　　这样输出结果也是“abcde”和“abcde”，但是String的速度却比StringBuilder的反应速度要快很多，这是因为第1行中的操作和

　　String str="abcde";

　//　是完全一样的，所以会很快，而如果写成下面这种形式

1 String str1="abc";
2 String str2="de";
3 String str=str1+str2;
　//　那么JVM就会像上面说的那样，不断的创建、回收对象来进行这个操作了。速度就会很慢。
~~~~
　　2. 再来说线程安全

　　在线程安全上，StringBuilder是线程不安全的，而StringBuffer是线程安全的

　　如果一个StringBuffer对象在字符串缓冲区被多个线程使用时，StringBuffer中很多方法可以带有synchronized关键字，所以可以保证线程是安全的，但StringBuilder的方法则没有该关键字，所以不能保证线程安全，有可能会出现一些错误的操作。所以如果要进行的操作是多线程的，那么就要使用StringBuffer，但是在单线程的情况下，还是建议使用速度比较快的StringBuilder。

　　3. 总结一下
　　String：适用于少量的字符串操作的情况

　　StringBuilder：适用于单线程下在字符缓冲区进行大量操作的情况

　　StringBuffer：适用多线程下在字符缓冲区进行大量操作的情况

##### 模糊查询语法，分页查询语法
~~~ sql
SELECT * FROM	t_user WHERE userId = 1;

UPDATE t_user
SET userName = "杨杨" and userid=1
WHERE
	userId = 1 INSERT INTO t_user
VALUES
	(
		NULL,
		"dad",
		2313,
		"dada",
		1,
		2018 - 07 - 09,
		0
	) DELETE
FROM
	t_user
WHERE
	userId = 1;
select * from t_user limit 4,8(从第五行开始)
SELECT * FROM t_user WHERE userid like "%1%"
~~~
#### 输入输出流
[输入输出流详解](https://blog.csdn.net/hulamua/article/details/53665816)
> . io流的具体分类
一、按I/O类型来总体分类：
 1. Memory

 1）从/向内存数组读写数据: CharArrayReader、CharArrayWriter、ByteArrayInputStream、ByteArrayOutputStream

2）从/向内存字符串读写数据 StringReader、StringWriter、StringBufferInputStream

2.Pipe管道  实现管道的输入和输出（进程间通信）: PipedReader、PipedWriter、PipedInputStream、PipedOutputStream

3.File 文件流 对文件进行读、写操作 ：FileReader、FileWriter、FileInputStream、FileOutputStream

4. ObjectSerialization 对象输入、输出 ：ObjectInputStream、ObjectOutputStream

5.DataConversion数据流 按基本数据类型读、写（处理的数据是Java的基本类型（如布尔型，字节，整数和浮点数））：DataInputStream、DataOutputStream

6.Printing 包含方便的打印方法 ：PrintWriter、PrintStream

7.Buffering缓冲 在读入或写出时，对数据进行缓存，以减少I/O的次数：BufferedReader、BufferedWriter、BufferedInputStream、BufferedOutputStream

8.Filtering滤流，在数据进行读或写时进行过滤：FilterReader、FilterWriter、FilterInputStream、FilterOutputStream过

9.Concatenation合并输入 把多个输入流连接成一个输入流 ：SequenceInputStream

10.Counting计数  在读入数据时对行记数 ：LineNumberReader、LineNumberInputStream

11.Peeking Ahead 通过缓存机制，进行预读 ：PushbackReader、PushbackInputStream

#####  [套接字解释](https://www.cnblogs.com/Ricezhang/p/3735989.html)

***套接字***是一种通信机制，凭借这种机制，客户/服务器系统的开发工作既可以在本地单机上进行，也可以跨网络进行，
Linux所提供的功能（如打印服 务，ftp等）通常都是通过套接字来进行通信的，
套接字的创建和使用与管道是有区别的，因为套接字明确地将客户和服务器区分出来，套接字可以实现将多个客 户连接到一个服务器。

###### 序列化和反序列化
				当你想把的内存中的对象状态保存到一个文件中或者数据库中时候；

				当你想用套接字在网络上传送对象的时候；

				当你想通过RMI传输对象的时候；

###### 使用checkbox实现批量删除功能
###### HttpServletRequest 中 getRequestURL和getRequestURI的区别

~~~ java

test1.jsp=======================

<a href ="test.jsp?name=wf">跳转到test2.jsp</a>

test2.jsp=======================

HttpServletRequest req = (HttpServletRequest) request;
HttpServletResponse resp = (HttpServletResponse) response;
String servletPath = req.getRequestURI()
String servletPath = req.getRequestURL()
得到的值为：req.getRequestURI()-----> /test/test.jsp

   req.getRequestURL()----->(http://localhost:8080/test/test.jsp  )

~~~
