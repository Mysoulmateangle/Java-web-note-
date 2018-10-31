#### html
~~~ html
<!----> 标签
 <!--注解-->
 ~~~
###### meta标签
 ***元素据以名称/值的形式被成对传递***
~~~ html
<!DOCTYPE html> <!--不是html标签，是用来注解，申明当前网页面的html版本-->

<html>
	<head>
		<meta charset="utf-8" /><!--<meta>标签没有结束标签，用来提供相关网页的元信息，针对搜索引擎和跟新频度的描述和关键词
     <meta>标签永远位于head元素内部
     <meta http-equiv="charset" content="iso-8859-1">
    <meta http-equiv="expires" content="31 Dec 2008">
    <meta name="keywords" content="HTML,ASP,PHP,SQL">-->
		<title></title>
	</head>
	<body>

	</body>
</html>
~~~
###### meta> 标签的作用
1.文档描述
~~~ html
<html>

<head>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />

<meta name="author"
content="w3school.com.cn">

<meta name="revised"
content="David Yang,8/1/07">

<meta name="generator"
content="Dreamweaver 8.0en">

</head>

<body>
<p>本文档的 meta 属性标识了创作者和编辑软件。</p>
</body>

</html>
~~~
2.文档关键字
~~~ html
<html>

<head>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />

<meta name="description"
content="HTML examples">

<meta name="keywords"
content="HTML, DHTML, CSS, XML, XHTML, JavaScript, VBScript">

</head>

<body>
<p>本文档的 meta 属性描述了该文档和它的关键词。</p>
</body>

</html>

~~~
3.重定向
~~~ html
<!DOCTYPE HTML>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<meta http-equiv="Refresh" content="5;url=http://www.w3school.com.cn" />
</head>

<body>
<p>
对不起。我们已经搬家了。您的 URL 是 <a href="http://www.w3school.com.cn">http://www.w3school.com.cn</a>
</p>

<p>您将在 5 秒内被重定向到新的地址。</p>

<p>如果超过 5 秒后您仍然看到本消息，请点击上面的链接。</p>

</body>
</html>

~~~
###### a标签 跳转地址使用
~~~ html

  <a href="http://www.w3school.com.cn">W3School</a>
~~~
##### p标签
> 用来定义段落
##### h标签
>用来定义标题
##### hr标签
>用来创建水平线
##### br标签
>用来不进行新的段落换行
##### Link标签
>定义了文档与外部资源之间的关系，通常用于连接到样式表
#### style标签
>定义了html文档的样式文件引用地址，
##### 图像标签
~~~ html
<img src="url" alt="some_text">
alt用于当无法加载图片的时候替换文本属性告诉失去的信息
~~~
##### table标签
每个表哥有若干行tr>，每行被分为若干单元格td>
也有边框属性border,表头属性th>
##### 无序列表
>用ul标签，li标签
##### 有序标签
>用ol标签和li标签
##### 自定义列表
>自定义列表不仅仅是一列项目，而是项目及其注释的组合。

      自定义列表以 <dl> 标签开始
      每个自定义列表项以 <dt> 开始。
      每个自定义列表项的定义以 <dd> 开始。
        <dl>
        <dt>Coffee</dt>
        <dd>- black hot drink</dd>
        <dt>Milk</dt>
        <dd>- white cold drink</dd>
        </dl>
##### html区块元素
  区块元素在浏览器中显示的时候，会以新行的形式进行显示
  h1,p,ul,table标签，div等
##### html内联元素
td,img,a,span标签
#### div元素
>用div进行网页布局
##### 单选框
~~~ html

~~~
##### 下拉框
~~~ html
<select name="cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat">Fiat</option>
<option value="audi">Audi</option>
</select>
~~~
#### 单选框
###### name相同
~~~ html
<form>
<input type="radio" name="sex" value="male" checked>Male
<br>
<input type="radio" name="sex" value="female">Female
</form>
~~~
#### 表单处理程序在表单的 action 属性中指定：
##### 复选框

~~~ html
<form>
<input type="checkbox" name="vehicle" value="Bike">I have a bike
<br>
<input type="checkbox" name="vehicle" value="Car">I have a car
</form>
~~~
##### 限制内容
~~~ html
<form>
  Quantity (between 1 and 5):
  <input type="number" name="quantity" min="1" max="5">
</form>
~~~
##### form标签
