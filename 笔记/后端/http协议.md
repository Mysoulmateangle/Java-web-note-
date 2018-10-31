#### http协议
* 什么是http协议
 >*超文本传输协议，用于定义web浏览器与web服务器进行数据交换的过程*
* http请求的内容***requerst***
 >*当客户端连接上服务器后，向服务器发送请求的时候，会发送一个http报文 ：包括一个请求行，若干消息头，以及实体内容*
* 请求行中的请求方式
>*常用方式有**post**,**get***
 >>1. https://www.baidu.com/s?wd=%BF%EC%BD%DD%BC%FC%CF%D4%CA%BE%D7%C0%C3%E6&fr=wenku
 这就是一个get请求，前面为url地址后面跟上一个？号，后面是以键值对存储的提交服务器的信息，键值对以&隔开，url地址后附带的参数的数据容量不能超过1K.
  * post请求是将数据放入**实体内容**中进行传输
>

```java
//获取请求方式
    System.out.println(req.getMethod());
    //获取项目名称
    System.out.println(req.getContextPath());
    //获取完整请求路径
    System.out.println(req.getRequestURL());
    //获取除了域名外的请求数据
    System.out.println(req.getRequestURI());
    //获取请求参数
    System.out.println(req.getQueryString());
    System.out.println("----------------------------------------------------------");
    //获取请求头
    String header = req.getHeader("user-Agent");
    System.out.println(header);
    header = header.toLowerCase();
    //根据请求头数据判断浏览器类型
    if(header.contains("chrome")){
        System.out.println("您的访问浏览器为谷歌浏览器");
    }else if(header.contains("msie")){
        System.out.println("您的访问浏览器为IE浏览器");
    }else if(header.contains("firefox")){
        System.out.println("您的访问浏览器为火狐浏览器");
    }else{
        System.out.println("您的访问浏览器为其它浏览器");
    }
    System.out.println("----------------------------------------------------------");
    //获取所有的消息头名称
    Enumeration<String> headerNames = req.getHeaderNames();
    //获取获取的消息头名称，获取对应的值，并输出
    while(headerNames.hasMoreElements()){
        String nextElement = headerNames.nextElement();
        System.out.println(nextElement+":"+req.getHeader(nextElement));
    }
    System.out.println("----------------------------------------------------------");
    //根据名称获取此重名的所有数据
    Enumeration<String> headers = req.getHeaders("accept");
    while (headers.hasMoreElements()) {
        String string = (String) headers.nextElement();
        System.out.println(string);
    }

```
* http响应的内容***respose***
>*一个状态行.若干的消息头.以及实体的内容*
>>1.
状态行中404为页面找不到，500为服务器内部问题，200为服务器正常返回数据，100-199为等待提交下次一次请求才能完成整个处理

***服务器对客户端进行控制***
