##### EL表达式
> expression langiage 表达式语言
  作用是提供了在JSp中简化表达式的方法
  格式：${ EL表达式 }   等价于  findAttribute(name)
  ***获取作用域的值***
  按照page,requerst,session,application(servletContext)的顺序查找指定的key的value值
  ***获取对象的属性***
~~~ java
    User user = new User(1,"zhangsan",20);
    request.setAttribute("user", user);
		${user.id}
		${user.name}
		${user.age}
        user----> 保存在request中时的key值
        id，name ，age 对象的属性名
    EL不能进行集合的遍历
    ***EL能做一些基本的运算***
    ${10+2 } ${10 / 2}  ${5==5 }  ${5<3 }
${list2[0].name=="aaa" }
${list2[0].age==20 }
${list2[0].age == list2[1].age }
~~~
#### JSTL表达式

JSTL：JavaServer Pages Standard Tag Library，JSP标准标签库是一个不断完善的开放源代码的JSP标签库，是由apache的jakarta小组来维护的。
JSTL支持通用的、结构化的任务，比如迭代，条件判断，XML文档操作，国际化标签，SQL标签。 除了这些，它还提供了一个框架来使用集成JSTL的自定义标签。

下载jakarta-taglibs-standard-1.1.2.zip 包并解压，将jakarta-taglibs-standard-1.1.2/lib/下的两个jar文件：standard.jar和jstl.jar文件拷贝到/WEB-INF/lib/下。（1.2版本，只需要jstl.jar即可）
2.2 JSTL分类
根据JSTL标签所提供的功能，可以将其分为5个类别。
c(core)  核心标签库
fmt     格式化（国际化）
fn       函数  （JSTL提供的EL函数）
xml
Sql
2.3 JSTL核心标签
核心标签是最常用的JSTL标签。引用核心标签库的语法如下：
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

常用标签

2.3.1通用标签
set,out,remove

2.3.2条件标签
if,choose

2.3.3 迭代标签foreach
for基础遍历

foreach遍历

  测试list集合遍历获取学生列表
  ~~~ html
      <table border="1" width="80%" bordercolor="red" cellspacing="0"
          align="center">
          <tr>
              <th>学号</th>
              <th>姓名</th>
              <th>成绩</th>
              <th>班级</th>
              <th>是否是第一个</th>
              <th>是否是最后一个</th>
              <th>计数count</th>
              <th>索引index</th>
          </tr>
   <!-- varStatus:变量状态：遍历出的每一项内容的状态：
      isFirst()       first
      isLast()        last
      getCount()     count  计数
      getIndex()     index
       -->
       <!-- var :遍历出的每一项使用变量先存储
            items：集合(使用El表达式)
            -->
          <c:forEach var="stu" items="${students}" varStatus="vs">
              <tr>
                  <td>${stu.id}</td>
                  <td>${stu.name}</td>
                  <td>${stu.score}</td>
                  <td>${stu.classes}</td>
                  <td>${vs.first}</td>
                  <td>${vs.last}</td>
                  <td>${vs.count}</td>
                  <td>${vs.index}</td>
              </tr>
          </c:forEach>
   </table>
~~~
2.4 JSTL格式化标签
JSTL格式化标签用来格式化并输出文本、日期、时间、数字。引用格式化标签库的语法如下：
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt_rt" %>
常用标签

<fmt:formatDate>标签：格式化时间和日期
例如：
<fmt:formatDate value="${current}"/>
通过type格式指定具体的格式形式。
<fmt:formatDatevalue="${d}"type="date"/>只格式成日期
<fmt:formatDatevalue="${d}"type="time"/>只格式成时间
<fmt:formatDatevalue="${d}"type="both"/>格式成日期+时间
通过pattern属性指定具体的格式要求
<fmt:formatDatevalue="${d}"pattern="yyyy-MM-dd"/>
2.6 fn标签库
fn:contains(string, substring)
如果参数string中包含参数substring，返回true
fn:containsIgnoreCase(string, substring)
如果参数string中包含参数substring（忽略大小写），返回true
fn:endsWith(string, suffix)
如果参数 string 以参数suffix结尾，返回true
fn:escapeXml(string)
将有特殊意义的XML (和HTML)转换为对应的XML character entity code，并返回
fn:indexOf(string, substring)
返回参数substring在参数string中第一次出现的位置
fn:join(array, separator)
将一个给定的数组array用给定的间隔符separator串在一起，组成一个新的字符串并返回。
fn:length(item)
返回参数item中包含元素的数量。参数Item类型是数组、collection或者String。如果是String类型,返回值是String中的字符数。
fn:replace(string, before, after)
返回一个String对象。用参数after字符串替换参数string中所有出现参数before字符串的地方，并返回替换后的结果
fn:split(string, separator)
返回一个数组，以参数separator 为分割符分割参数string，分割后的每一部分就是数组的一个元素
fn:startsWith(string, prefix)
如果参数string以参数prefix开头，返回true
fn:substring(string, begin, end)
返回参数string部分字符串, 从参数begin开始到参数end位置，不包括end位置的字符
fn:substringAfter(string, substring)
返回参数substring在参数string中后面的那一部分字符串
fn:substringBefore(string, substring)
返回参数substring在参数string中前面的那一部分字符串
fn:toLowerCase(string)
将参数string所有的字符变为小写，并将其返回
fn:toUpperCase(string)
将参数string所有的字符变为大写，并将其返回
fn:trim(string)
去除参数string 首尾的空格，并将其返回
