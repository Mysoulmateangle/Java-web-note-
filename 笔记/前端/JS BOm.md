#### BOM(浏览器对象模型)
##### windows对象
所有浏览器都支持window对象，它表示浏览器窗口
全局变量是window对象的属性
全局函数是window对象的方法
dom中的doucument也是window对宪法的属性之一
#### 实现当前页面的跳转
~~~ html
window.location.herf="http://www.baidui.com"
window.location="http://www.baidui.com"
location.reload();
window.open("http://www.baidu.com","baidu");
~~~
alert("文本") //警告框
confirm("文本") //确认框
prompt("文本","默认值")//提示框
## 定时器
setTimeout()
