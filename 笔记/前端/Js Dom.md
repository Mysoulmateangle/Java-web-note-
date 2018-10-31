#####　Dom（文档对象模型）

>通过html DOM可访问javascript html 文档ｄｅ所有元素
>javascript 能够改变页面的所有html元素。html属性。css样式。对页面所有时间做出反应
##### 查找元素
通过ID,标签，类名，
~~~ html
doucument.getElementById("idname")
doucument.getElementsByTagName("标签")
<--查找id为main元素中的所有p标签--!>
var x=document.getElementById("main");
var y=x.getElementsByTagName("p");
~~~
改变html内容
~~~ html
doucument.getElementById("idname").innnerHTML="<span>我是谁</span>"

~~~
改变html属性值
~~~ html
doucument.getElementById("image").src="landscape.jsp"
~~~
改变html样式
~~~ html
doucument.getElementById("idname").style.visibility="visible"
~~~
###### DOM事件
>单击事件
 ~~~ html
 <h1 onclik="change(this)">这是一级标签</h1>
 <script type="text/javascript">
 function change(obj){
   obj.innnerHTML="成功的路上并不拥挤，因为坚持到底的人很少"
 }
 </script>
 <h1 id="hh">这是一级标签</h1>
 <script type="text/javascript">
 doucument.getElementById("id").onclick=function (){
   this.innnerHTML="成功的路上并不拥挤，因为坚持到底的人很少"
 }
 </script>
 ~~~
 ##### onload事件
 >onload 一般可以做一些网页的环境准备工作
 window.onload=function(){
   var hh=doucument.getElementById("hh")
   hh.innnerHTML="dadadada"
 }
##### 键盘事件 有onkeydown,onkeypress,onkeyup
分别是键盘敲击下，键盘按住，键盘弹起后的是事件
##### 鼠标移动事件
onMouseDown 按下鼠标时候触发
onMouseOver 鼠标经过时触发
onMouseup 按下鼠标松开鼠标的时候触发
onMouseOut 鼠标移出时触发
onMouseMove 鼠标移动时触发
##### 焦点事件 onfocus/onblur
##### DOm对节点的运用
对于节点需要先找到节点的父节点，然后在父节点上添加子节点
~~~ html

  <input type="button" value="添加段落标签" onclick="add()"/>
  <div id="div1">
    <p>段落标签</p>
  </div>

  <script type="text/javascript">

    var div1 = document.getElementById("div1");

    function add(){

      //1.创建标签		<p></p>
      var p = document.createElement("p");
      //2.创建内容		新的段落标签
      var text = document.createTextNode("新的段落标签");
      //3.给p标签添加内容	<p>新的段落标签</p>
      p.appendChild(text);
      //4.把p标签添加到div中
      div1.appendChild(p);
    }


  </script>
~~~
##### 克隆节点
~~~ html
<input type="button" value="添加段落标签" onclick="add()"/>
<div id="div1">
  <img src="../img/头像1.jpg" width="100px" height="100px" />
  <br />
</div>

<script type="text/javascript">

  var div1 = document.getElementById("div1");

  function add(){

    //1.获取要被克隆的标签对象
    var img = document.getElementsByTagName("img")[0];
    //2.克隆
    var newImg = img.cloneNode(true);//深度克隆

    //3.创建br标签
    var br = document.createElement("br");

    div1.appendChild(newImg);
    div1.appendChild(br);
  }


</script>
~~~
###### 删除节点
 ~~~ html
 <input type="button" value="添加段落标签" onclick="add()"/>
		<input type="button" value="删除段落标签" onclick="delete1()"/>
		<div id="div1">
			<img src="../img/头像1.jpg" width="100px" height="100px" />
			<br />
		</div>

		<script type="text/javascript">

			var div1 = document.getElementById("div1");

			function add(){

				//1.获取要被克隆的标签对象
				var img = document.getElementsByTagName("img")[0];
				//2.克隆
				var newImg = img.cloneNode(true);//深度克隆

				//3.创建br标签
				var br = document.createElement("br");

				div1.appendChild(newImg);
				div1.appendChild(br);
			}

			function delete1(){

				//1.找到要删除的标签对象
				var img = document.getElementsByTagName("img")[0];
				var br = document.getElementsByTagName("br")[0];
				//2.找到要删除的标签对象的父标签对象
				var parentNode = img.parentNode;
				//3.删除
				parentNode.removeChild(img);
				parentNode.removeChild(br);
			}

		</script>
 ~~~
