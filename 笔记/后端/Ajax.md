#### Ajax 
*** Ajax就是异步的javascrip和xml***
> Ajax本质就是与服务器进行交互，前提条件不刷新网页

***运用***
1.异步的提交数据到后台
2.校验是否可用的状态
3.异步加载下拉框
4.自动补全
5.异步删除等

***流行的AJAX框架（了解）***
客户端：
           底层框架：prototype(prototype.js) 、   jquery(jquery.js)......
           高层框架：YUI（Yahoo User Interface）、 EXTJS、 Dojo ......

Vue.js  AngularJS

服务器端：
~~~ java
           DWR  使用JS直接调用 POJO（Plaint Old Java Object）中的方法
           GWT（google）
           1 创建XMLHttpRequest：
         IE：
                  new ActiveXObject("Microsoft.XMLHTTP")  --IE6
                  new ActiveXObject("Msxml2.XMLHTTP")   ---IE7
         其他浏览器以及IE7以上版本IE
               new XMLHttpRequest( );
              function createXmlHttpRequest(){
         		if(window.XMLHttpRequest==false){
         			return new XMLHttpRequest();
         		}else{
                       //返回值为false时说明是老版本IE浏览器（IE5和IE6）
         			return new ActiveXObject("Microsoft.XMLHTTP");
         		}
         	}

         2、设置回调函数：
         xhr.onreadystatechange = callBack;

         function callBack(){
         if(xhr.readyState == 4 && xhr.status == 200){
         		var data = xhr.responseText;
                  alert(data);
                }
         }
   ~~~
